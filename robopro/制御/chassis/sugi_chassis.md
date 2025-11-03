# 基本情報
- パッケージ名: `sugi_chassis`
- 優先度: 中
- 予定工数: 1週間あればいいんじゃね


# パッケージ概要
## 目的
すぎ足回り（東大方式）を制御するパッケージ及びノード


## 主要機能
- `/gear_position_control_ref_mm/board<robomas_board_id>/motor<motor_number>`にGoalを送信（指令値を送信）
- `sabacan_gpio_node`からリミットスイッチの信号を受信
- 収納時、展開時にリミットスイッチがのフィードバックが1になる
- 段差乗り越え動作を指示するノードは別で作成
	- `/sugi_chassis_ref`でtrue, falseで展開、収納を制御
- モータの制御は、展開収納機構は`gear_position_control_node`のアクションに全任
- 必要なパラメータ
	- 制御するモータボードID
	- 制御するモータの番号
	- 受信するGPIOボードのID2つ
	- 受信するリミットスイッチのピン番号2つ
	- chassisの番号
	- 収納・展開時のモータの目標値
- 最終的には、足の数だけこのノードを実行して、他のノードから展開・収納指令を送信する
- 起動した最初に機構が収納されているか判定を行い、展開されていれば、収納する。この判定はリミットスイッチを使用して行う


# 通信使用

## publishするトピック
- なし

## subscribeするトピック
1. `/sabacan_gpio_res<gpio_board_id>`（定義済み）
	- 型: `sabacan_msgs/msg/sabacan_gpio_res`
	- 用途: 指定されたピン番号のリミットスイッチのみフィードバックを取得
2. `/sugi_chassis_ref<chassis_number>`（未定義）
	- 型: `std_msgs/msg/bool`
	- 展開、収納フラグ

## action
1. `/gear_position_control_ref_mm/board<board_id>/motor<motor_number>`（定義済み）
	- ファイル名: `GearPositionControl.action`
	- 用途: モータを指定位置まで移動させる
	- 以下内容
```
std_msgs/Float32 goal_mm
---
std_msgs/Float32 final_mm
---
std_msgs/Float32 current_mm
```


## service
1. `/gear_position_control_reset/board<robomas_board_id>/motor<motor_number>`（定義済み）
	- 型: `std_srvs::srv::Trigger`
	- 用途: モータ角のリセット指令を送信

## parameter
1. `robomas_board_id`
	- 型: `int`
	- デフォルト値: 0
	- 用途: 制御するモータボードID
2. `motor_number`
	- 型: `int`
	- デフォルト値: 0
	- 用途: 制御するモータのID
3. `gpio_board_id_expand`
	- 型: int
	- デフォルト値: 0
	- 用途: 展開用GPIOボードのID
4. `gpio_board_id_storage`
	- 型: int
	- デフォルト値: 1
	- 用途: 収納用GPIOボードのID
5. `pin_number_expand`
	- 型: int
	- デフォルト値: 0
	- 用途: 展開用リミットスイッチのピン番号
6. `pin_number_storage`
	- 型: int
	- デフォルト値: 0
	- 用途: 収納用リミットスイッチのピン番号
7. `chassi_number`
	- 型: int
	- デフォルト値: 0
	- 用途: sugi_chassisの番号
8. `distance_expand_mm`
	- 型: float
	- デフォルト値: 100.0
	- 用途: 展開時の目標距離[mm]
9. `distance_storage_mm`
	- 型: float
	- デフォルト値: 0.0
	- 用途: 収納時の目標距離[mm]


# READMEに記載する内容
- 動作確認用のコマンド
- プログラム内のすべての関数に日本語でコメントをつける