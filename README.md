# slam_toolbox_test
[![Open in Dev Containers](https://img.shields.io/static/v1?label=Dev%20Containers&message=Open&color=blue&logo=visualstudiocode)](https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/teruyamato0731/slam_toolbox_test)

ROS2 humble で slam_toolbox を使用してみる。
なお, odom を実装していないので起動時に一度のみ Map が更新される。
X11をマウントしてGUIアプリの使用ができるようにしている。
Ubuntu 22.04で動作確認済み。

# Quick Start
あなたがすでにVS CodeとDockerをインストールしている場合は、上記のバッジまたは[こちら](https://vscode.dev/redirect?url=vscode://ms-vscode-remote.remote-containers/cloneInVolume?url=https://github.com/teruyamato0731/slam_toolbox_test)をクリックすることで使用することができる。<br>
これらのリンクをクリックすると、vscodeが必要に応じてdev container拡張機能を自動的にインストールし、ソースコードをコンテナボリュームにクローンし、使用するためのdev containerを起動する。

# How to use
1. Docker, vscode, devcontainer拡張機能をインストールする。
1. X11のアクセスをローカルに対して許可する。
    ```bash
    xhost +local:
    # non-network local connections being added to access control list
    ```
1. 自身の作成したリポジトリをcloneしvscodeで開く。<br>
    リポジトリのリンクやディレクトリ名は各自で読み替えること。
    ```bash
    git clone https://github.com/teruyamato0731/slam_toolbox_test.git
    code slam_toolbox_test
    ```
1. 「Reopen in Container」でdevcontainerを開く
1. `ls /dev/ | grep ttyUSB` を実行し、シリアル変換機のポートを確認しておく。
    ([見つからない場合、こちらを参照](https://github.com/teruyamato0731/ld06_test#trouble-shooting))
1. [src/slam_toolbox/config/mapper_params_online_async.yaml](src/slam_toolbox/config/mapper_params_online_async.yaml) を下記のように編集
    ```yaml
        # ROS Parameters
        odom_frame: odom
        map_frame: map
        base_frame: laser
        scan_topic: /scan
        mode: mapping #localization
    ```
1. それぞれ別のターミナルで下記コマンドを実行。
    ```bash
    sudo apt update && rosdep update && rosdep install --from-path src --ignore-src -y
    colcon build
    source install/local_setup.bash
    ros2 launch ldlidar ldlidar.launch.py serial_port:=/dev/${USB_PORT}
    # 例
    # ros2 launch ldlidar ldlidar.launch.py serial_port:=/dev/ttyUSB0
    ```
    ```bash
    source install/local_setup.bash
    ros2 launch slam_toolbox online_async_launch.py
    ```
    ```bash
    ros2 run tf2_ros static_transform_publisher 0 0 0 0 0 0 odom laser
    ```
    ```bash
    rviz2 -d laser.rviz
    ```
