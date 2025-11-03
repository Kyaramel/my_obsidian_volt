# 基本情報
- パッケージ名: `sugi_chassis`
- 優先度: 中
- 予定工数: 1週間あればいいんじゃね


# パッケージ概要
## 目的
すぎ足回り（東大方式）を制御するパッケージ及びノード


## 主要機能
- `/gear_position_control_ref_mm/board<board_id>/motor<motor_number>`にGoalを送信（指令値を送信）
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
- 最終的には、足の数だけこのノードを実行して、他のノードから展開・収納指令を送信する
- 起動した最初に機構が収納されているか判定を行い、展開されていれば、収納する。この判定はリミットスイッチを使用して行う


# 通信使用

## publishするトピック
- なし

## subscribeするトピック
- トピック名: `/sabacan_gpio_res<board_id>`
	- 型: `sabacan_msgs/msg/sabacan_gpio_res`
	- 用途: 指定されたピン番号のリミットスイッチのみフィードバックを取得
- トピック名: `/sugi_chassis_ref<chassis_number>`
	- 型: `std_msgs/msg/bool`
	- 展開、収納フラグ