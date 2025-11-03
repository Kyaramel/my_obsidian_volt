外部から角度指令[rad]を受け取って、サーボを制御する信号に変換して sabacan_gpio_node に送信するパッケージ及び、node を作成してほしい。

パッケージ名: `sabacan_gpio_servo`
ノード名: `sabacan_gpio_servo_node`

フォルダ構成

- `sabacan_gpio_servo/`
  - `src/`
    - `sabacan_gpio_servo_node.cpp`
  - `CMakeLists.txt`
  - `packages.xml`
  - `README.md`

パラメータ

- `board_id`: 制御する GPIO ボードの ID
- `pin_number`: 制御する GPIO ボードのピン番号

Publish

- `/sabacan_gpio_ref<board_id>`: サバ缶 GPIO ノードへ指令値を送信
  - メッセージ型: `sabacan_msgs::msg::SabacanGPIORes`
    ```
    uint8 pin_number # ピン番号
    int32 pwm_period
    float32 duty # ESCなら0.0~1.0 or 0~100 depending on PWM percentage
    ```

Subscribe

- `/sabacan_gpio_servo_ref<board_id>/servo<pin_number>`: パラメータで指定されたボード ID、ピン番号のトピックを Sub
  - メッセージ型: `std_msgs::msg::float32`
    ```
    float32 data
    ```