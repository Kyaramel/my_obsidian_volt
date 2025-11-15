# 野田足回りの昇降動作
- gear_position_control
	- actionのgoalを受信して指定した位置に送り制御
- noda_chassis_node
	- ホーミング処理
		- gpioやトルクを使用して原点を認識して角度をリセット
		- 角度リセットはgear_position_controlノードにサービスを送信
	- 動作処理
		- actionで原点から昇降する位置をmm単位でgoalを受信
		- gear_position_controlに目標位置のgoalを送信
		- 二重のaction
		- 下限上限のgpioが反応したら、actionをキャンセル
- joy_noda_chasiss_node
	- 4つのnoda_chassis_nodeを制御
	- パラメータで4つのchassi_number、目標値を設定


# ros2 actionについて
- ノードで使用するときは、サーバー側（処理を実行する側）とクライアント側（処理を依頼する側）でコールバックの種類が異なる
	- サーバー側
		- クライアントからゴール（目標値）を受け取って処理を行う
		- handle_goal
			- クライアントから新しいゴールリクエストが届いた最初に実行される
			- ゴールを受け入れるか（ACCEPT）、拒否するか（REJECT）を判断する
			- 例: ロボットが現在別のゴールを実行中→新しいゴールはREJECT
		- hande_cancel
			- すでにACCEPTしたゴールに対して、クライアントからキャンセルリクエストが届いたときに実行される
			- キャンセル要求をACCEPTするか、REJECTするかを判断する
			- 例: すでに処理が最終段階に入っていてキャンセルしたくない→REJECT
		- execute
			- handle_goalがゴールをACCEPTした直後に実行される
			- 実際のタスク処理を実行する本体。以下の処理を行う
				- フィードバックの送信（クライアントに進捗状況を送信）
				- キャンセルの確認（キャンセル要求が届いていかないか定期的にチェック）
				- 結果の送信（処理が完了したら、結果（成功/失敗/キャンセル）をクライアントに送信する）
	- クライアント側
		- サーバーに処理を依頼し、進捗と結果を受け取る
		- goal_response_callback
			- サーバーがゴールリクエストに対して応答を返したときに実行される
			- サーバーがゴールをACCEPTしたかどうかを確認する
			- REJECTされたときはエラー処理など行う
		- feedback_callback
			- サーバーがexecute処理中にpublish_feedback()を呼び出し、進捗状況を送信してきたときに呼び出される
			- 処理が完了するまで複数回呼び出される
		- result_callback
			- サーバー側がexecute処理を完了し、結果を送信したときに処理される
			- 処理の最終結果と結果のデータを受け取る
- 一連の流れ
	1. クライアントがサーバーにゴールを依頼: `async_send_goal`
	2. サーバーがゴールをACCEPTするかREJECTするか判断: `handle_goal`
	3. クライアントがサーバーがゴールをACCEPTしたかどうか受け取る: `goal_response_callback`
	4. ACCEPTした場合、サーバーは処理を実行: `execute`
	5. 処理中にサーバーがフィードバックを送信: `publish_callback`
	6. クライアントがフィードバックを受信する: `feedback_callback`
	7. サーガー側の処理が完了する: `succeed/abort`
	8. クライアントが結果を受け取る: `result_callback`


# ros2でBehaviorTree
- `Behavior Tree`はros2の標準機能ではない。もともと、ロボット制御やゲームとは関係ない分野で開発され、後にROS 2などで広く使われるようになった汎用的な意思決定フレームワーク
- treeでタスクの実行順書を制御する
- tick信号がtreeを構成するnode同士をつなぐ
- tickを受け取ったnodeは3状態のうちいずれかを返す
	- SUCCESS: タスクが成功
	- FAILURE: タスクが失敗
	- RUNNING: タスクを実行中
- nodeはいくつかに分類される
	- ControlNode: 複数の子ノードをその状態に応じて制御する
	- DecoratorNode: 1つの子ノードに対して、結果の変更や繰り返しの実行
	- ConditionNode: 子ノードを持たず、簡単な処理を実行する
	- ActionNode: 子ノードを持たず、このノード自体がなにかの命令を実行する
- BehaviorTreeにおいてユーザが実装しなければならない要素2つ
	- Treeの構造
	- ActionNode
- Treeはxml形式で記述
- Grootというソフトからguiで生成することもできる
- ActionNodeが外部のプログラムとの通信の役割を担っている
	- ROSと組み合わせるのもこれ
- nodeがtickの信号を受け取ると`Tick()`関数が実行される。ここに実行したいプログラムを実装する
- 2つのnode間のデータのやり取りにPortsが使われる
- インストール
```
sudo apt install libzmq3-dev libboost-dev qtbase5-dev libqt5svg5-dev libzmq3-dev libdw-dev
git clone --branch v3.8 https://github.com/BehaviorTree/BehaviorTree.CPP.git
cd BehaviorTree.CPP
mkdir build; cd build
cmake ..
make
sudo make install
```
- Behavior Treeのバージョンには気をつけた方が良さそう。何も考えずにインストールしちゃった。。。

## パッケージの作りかた
- ros2パッケージの作り方
	- 普通に`ros2 pkg create`でパッケージを作る
	- tree情報を記述したxmlファイルを`config/`
	- 各actionノードを記述したプログラムを`include/`
	- BehaviorTreeに登録し実行するプログラムを`src/`
- 設計思想: 作成したtopic, service, action通信を行うノードをなるべく細分化してノードを作成（1つの通信に1ノードくらい？）。treeでノードをまとめる