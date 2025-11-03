
# 📋 基本情報
- **パッケージ名**: `noda_chassis`
- **優先度**: `高`
- **予定工数**: `2025/10/31`

# 🎯 パッケージ概要
## 目的
R2 野田足回り昇降機構を制御するパッケージおよびノード

## 主要機能
- `/gear_position_control_ref_mm/board<board_id>/motor<motor_number>`にGoalを送信（指令値を送信）
- `sabacan_gpio_node`からフォトインタラプタの信号を受信
- 最上点と最下点についている切り欠きをフォトインが受信して昇降の制御
- 段差乗り越え動作を指示するノードは別で作成
    - `/noda_chassis_ref`で最下面からの高さを指定
- モータの制御は`gear_position_control_node`のアクションに全任
- 必要なパラメータ
    - 制御するモータボード ID
    - 制御するモータの ID
    - 受信する GPIO ボードの ID
    - 受信するフォトインタラプタのピン番号
- 最終的には、足の数だけこのノードを実行して、他のノードからそれぞれの足の昇降高さ指令を送信する
- 起動した最初に位置合わせ動作を行う（最下面のフォトインタラプタが反応するまで、モータを下に移動）
  

# 📡 通信仕様
  
## パブリッシュするトピック
| トピック名                                           | メッセージ型                                                 | 説明                 | 頻度                    |

| ---------------------------------------------------- | ------------------------------------------------------------ | -------------------- | ----------------------- |

| `/sabacan_robomas_ref<board_id>/motor<motor_number>` | `sabacan_single_control_msgs/msg/sabacan_robomas_single_ref` | ロボマスモータの制御 | だいたい 50 Hz くらい？ |

  

## サブスクライブするトピック

  

| トピック名                         | メッセージ型                              | 説明               |

| ----------------------------- | ----------------------------------- | ---------------- |

| `/sabacan_gpio_res<board_id>` | `sabacan_msgs/msg/sabacan_gpio_res` | GPIO ボードのフィードバック |

| `/noda_chassis_ref<chassis_number>` | `std_msgs/msg/float32`              | 最下面からの上昇高さ[mm]   |


## サービス
- サービス名: `/gear_position_control_reset/board<board_id>/motor<motor_number>`
	- メッセージ型: `std_srvs::srv::Trigger`
	- 用途: モータ角のリセット



## アクション

- アクション名: `/gear_position_control_ref_mm/board<board_id>/motor<motor_number>`
	- ファイル名: `GearPositionControl.action`
	- モータを指定位置まで移動させる
	- Goal: `std_msgs/msg/Float32` 目標距離[mm]
	- Result: `std_msgs/msg/Float32` 最終的な到達位置[mm]
	- Feedback: `std_msgs/msg/Float32` 移動中の現在位置[mm]
  

# ⚙️ パラメータ

  

| パラメータ名             | 型        | デフォルト値 | 説明                    |

| ------------------ | -------- | ------ | --------------------- |

| `robomas_board_id` | `int`    | 0      | 制御モータボード ID           |

| `motor_number`     | `int`    | 0      | 制御するモータの ID           |

| `gpio_board_id`    | `int[2]` | [0, 1] | 受信する GPIO ボードの IDの配列。順番に最下面、最上面  |

| `pin_number`       | `int[2]` | [0, 1] | 受信するフォトインタラプタのピン番号の配列。順番に最下面、最上面 |

| `chassis_number`      | `int`    | 0      | 野田足の番号                |

  

# 📝 ドキュメント要件

  

- `[ ] README.md作成`

- `[ ] 使用例（動作確認用のコマンド）`
- `[ ] プログラム内のすべての関数にコメントをつける`