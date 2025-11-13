# 📋 基本情報

- **パッケージ名**: `joy_noda_chassis`
- **優先度**: `高`
- **予定工数**: `2時間`

# 🎯 パッケージ概要

## 目的

noda_chassisパッケージ及び、gear_position_controlパッケージを使用して機構の昇降動作の実装が正常に確認できた。次はこれを、コントローラのボタンを使用してテストできるようにしたい。

## 主要機能

- パラメータ
	- 使用するボタンのインデックス番号
	- noda_chassisのシャーシ番号の配列（サイズ4）
	- それぞれのnoda_chassisに送信する目標値[mm]の配列（サイズ4）
- joyからデータをsubscribe
- 指定されたボタンが偶数回押されたら`/chassis_reset`アクションをすべてのnoda_chassisに送信して、ホーミング処理
	- cliで動作確認したときのコマンド
	```
	ros2 action send_goal /chassis_reset1 noda_chassis/action/ChassisReset "{}" --feedback
	```
- 奇数回押されたら`/noda_chassis_move`アクションをすべてのnoda_chassisに指定した目標値を送信
	- cliで動作確認したときのコマンド
	```
	ros2 action send_goal /noda_chassis_move1 noda_chassis/action/NodaChassisMove "{target_mm: 25.0}" --feedback
	```

# 📡 通信仕様

## パブリッシュするトピック

## サブスクライブするトピック

- トピック名: `/joy`
	- メッセージ型: `sensor_msgs/msgJoy`
	- 用途: コントローラからの入力を取得

## サービス

## アクション

- アクション名: `/chassis_reset<chassis_number>`
	- メッセージ型: `noda_chassis/action/ChassisReset`
	- 用途: noda_chassisのホーミング処理
- アクション名: `/noda_chassis_move<chassis_number>`
	- メッセージ型: `noda_chassis/action/NodaChassisMove`
	- 用途: noda_chassisの目標値[mm]を送信

# ⚙️ パラメータ
- `button_index`
	- 型: int
	- 初期値: 0
	- 用途: 使用するボタンのインデックス
- `chassis_number_array`
	- 型: int[4]
	- 用途: 制御するnoda_chassisの番号配列
- `target_value_mm`
	- 型: float[4]
	- 用途: 目標値[mm]

# 作業指示

- まず、最初にパッケージのディレクトリ構造と、メインの処理の構成を提示すること
- メインの処理について、どのような関数を作成し、どういう処理を実行するのか説明すること
- これらの設計構想を確認し、受理されるまでパッケージやノードは作成しないこと
- 受理され次第、実装作業を行い、完成したら教えてください
- colcon buildの指示がされたら、指定されたディレクトリでビルドを実行すること
- エラーが発生した場合は、まず、どういうエラーが発生したのかエラーコードを解析し、何が原因なのか特定すること
- 問題の原因が特定でき次第、修正作業を行うこと
- コード
	- 1つのノードファイルに収めること
- 状態管理
	- モータの現在角度、目標速度などの内部状態は、ノードクラス内のプライベートなメンバ変数で管理すること
- デバッグ
	- RCLCPP_INFOなどを使用して、容易にデバッグが行えるようにしてください
- テスト
	- 一応ros2 testが行えるようにしてください

# 📝 ドキュメント要件

- `[ ] README.md作成`
- `[ ] 使用例（動作確認用のコマンド）`
- `[ ] プログラム内のすべての関数に日本語でコメントをつける`

