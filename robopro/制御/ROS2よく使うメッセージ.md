# ROS 2 メッセージリファレンス：常用メッセージ一覧

このドキュメントは、本ロボットプロジェクトで使用する主要なROS 2メッセージ（標準およびカスタム）をまとめたものです。

## I. 標準 ROS 2 パッケージ

### 1. std_msgs (基本データ型)

汎用的なプリミティブ型やヘッダ情報を提供します。

- **`std_msgs/msg/Header`**
    - **用途**: タイムスタンプ（`stamp`）や座標系名（`frame_id`）など、データにコンテキストを与えるヘッダ情報。

```
builtin_interfaces/Time stamp
string frame_id
```

- **`std_msgs/msg/Float64`**
    - **用途**: 単一の64ビット浮動小数点数（`data`）を伝達。

```
float64 data
```

### 2. geometry_msgs (空間・運動)

位置、姿勢、速度など、ロボットの運動学的なデータを扱います。

- **`geometry_msgs/msg/Point`**
    - **用途**: 空間上の点 (x, y, z)。

```
float64 x
float64 y
float64 z
```

- **`geometry_msgs/msg/Quaternion`**
    - **用途**: 空間内の姿勢（回転）を表現。

```
float64 x
float64 y
float64 z
float64 w
```

- **`geometry_msgs/msg/Pose`**
    - **用途**: 位置 (`Point`) と姿勢 (`Quaternion`) の組み合わせ。

```
geometry_msgs/Point position
geometry_msgs/Quaternion orientation
```

- **`geometry_msgs/msg/Twist`**
    - **用途**: 並進速度 (`linear`) と角速度 (`angular`)。

```
geometry_msgs/Vector3 linear
geometry_msgs/Vector3 angular
```

- **`geometry_msgs/msg/TransformStamped`**
    - **用途**: ある座標系から別の座標系への変換（TF）。

```
std_msgs/Header header
string child_frame_id
geometry_msgs/Transform transform
```

（`transform`は`geometry_msgs/Vector3 translation`と`geometry_msgs/Quaternion rotation`を含む）

### 3. sensor_msgs (センサデータ)

物理的なセンサからの入力を扱います。

- **`sensor_msgs/msg/Joy`**
    - **用途**: ゲームコントローラ（ジョイスティック）の入力状態。

```
std_msgs/Header header
float32[] axes
int32[] buttons
```

- **`sensor_msgs/msg/LaserScan`**
    - **用途**: 2D LIDARの平面スキャンデータ。

```
std_msgs/Header header
float32 angle_min
float32 angle_max
float32 angle_increment
# ... 他のフィールド ...
float32[] ranges
float32[] intensities
```

- **`sensor_msgs/msg/Imu`**
    - **用途**: IMU（慣性計測ユニット）のデータ（姿勢、角速度、加速度）。

```
std_msgs/Header header
geometry_msgs/Quaternion orientation
float64[9] orientation_convariance
geometry_msgs/Vector3 angular_velocity
# ... 他のフィールド ...
geometry_msgs/Vector3 linear_acceleration
```

- **`sensor_msgs/msg/Image`**
    - **用途**: カメラからの画像データ。

```
std_msgs/Header header
uint32 height
uint32 width
string encoding
# ... 他のフィールド ...
uint8[] data
```

### 4. nav_msgs (ナビゲーション)

自己位置推定や経路計画に関する情報を扱います。

- **`nav_msgs/msg/Odometry`**
    - **用途**: ロボットの推定位置と速度の情報（共分散付き）。

```
std_msgs/Header header
string child_frame_id
geometry_msgs/PoseWithCovariance pose
geometry_msgs/TwistWithCovariance twist
```

（`PoseWithCovariance`や`TwistWithCovariance`には`float64[36] covariance`が含まれる）

- **`nav_msgs/msg/Path`**
    - **用途**: ロボットの移動経路を構成する一連の姿勢の配列。

```
std_msgs/Header header
geometry_msgs/PoseStamped[] poses
```

- **`nav_msgs/msg/OccupancyGrid`**
    - **用途**: 占有格子地図データ（-1:未知, 0:空き, 100:占有）。

```
std_msgs/Header header
nav_msgs/MapMetaData info
int8[] data
```

### 5. can_msgs (低レベル通信)

- **`can_msgs/msg/Frame`**
    - **用途**: CAN通信の生フレームデータ。

```
std_msgs/Header header
uint32 id
bool is_rtr
bool is_extended
bool is_error
uint8 dlc
uint8[8] data
```

## II. ロボット固有パッケージ（sabacan系）

### 1. モータ制御指令系 (Ref)

モータ駆動ノードへの指令値を伝達します。

- **`sabacan_msgs/msg/sabacan_robomas_ref`**
    - **トピック名**: `/sabacan_robomas_ref<board_id>`
    - **用途**: ロボマスモータ全体への指令値。

```
uint8 motor_number
float32 ref
```

- **`sabacan_single_control_msgs/msg/sabacan_robomas_single_ref`**
    - **トピック名**: `/sabacan_robomas_ref<board_id>/motor<motor_number>`
    - **用途**: ロボマスで**モータ1つ**を制御するための指令値。

### 2. GPIO指令系 (Ref)

GPIOボードへPWM出力やESC制御の指令値を伝達します。

- **`sabacan_msgs/msg/SabacanGPIORefFloat`**
    - **トピック名**: `/sabacan_gpio_ref_float<board_id>`
    - **用途**: PWMデューティ比など、浮動小数点型の指令値。

```
uint8_t pin_number
float ref_float
```

- **`sabacan_msgs/msg/SabacanGPIORefInt`**
    - **トピック名**: `/sabacan_gpio_ref_int<board_id>`
    - **用途**: ESC値など、整数型の指令値。

```
uint8_t pin_nuber
int32 ref_int
```

### 3. フィードバック・ステータス系 (Status)

ハードウェアからの状態やセンシング値を受け取ります。

- **`sabacan_msgs/msg/sabacan_robomas_status`**
    - **トピック名**: `/sabacan_robomas_status<board_id>`
    - **用途**: ロボマスモータのフィードバック。

```
uint8 motor_number
string motor_type
string control_type
bool motor_state
float32 torque
float32 speed
float32 pos
float32 abs_pos
float32 abs_speed
int32 abs_turn_cnt
```

- **`sabacan_msgs/msg/SabacanGPIOStatus`**
    - **トピック名**: `/sabacan_gpio_status<board_id>`
    - **用途**: GPIOピンの現在の入力状態。

```
uint8_t pin_number
bool input
```

## III. Action インターフェース

移動や保持といった、完了までに時間がかかる非同期処理に使用されます。

- **アクション名**: `/gear_position_control_ref_mm/board<board_id>/motor<motor_number>`
    - **ファイル名**: `GearPositionControl.action`
    - **用途**: モータを指定位置まで移動させ、その位置を保持する。

```
# Goal
std_msgs/Float32 goal_mm
---
# Result
std_msgs/Float32 final_mm
---
# Feedback
std_msgs/Float32 current_mm
```