# 一台電腦 多網卡控制多tello的ros接口

## 原始
https://github.com/appie-17/tello_driver.git

## 如何設定linux的socket權限

* 針對在蟒蛇中執行以下程式會需要管理員的權限
    ```python
    # 指定特定網卡（wlx5c925e80f3f7）傳送封包
    sock.setsockopt(socket.SOL_SOCKET, 25, 'wlx5c925e80f3f7'.encode())
    ```

* 又`roslaunch`對於有管理員密碼的`sudo`除了直接在管理員權限下執行之外沒有找到其他方法
* linux可以只設定一部分的權限
* 不能直接`setcap /usr/bin/env python2`，因為`image_transport`會壞掉 

### 操作邏輯
1. 建立虛擬環境
    ```bash
    pip install virtualenv
    virtualenv --system-site-packages -p python2.7 ~/catkin_ws5/socketPY
    ```
2. 賦權
    ```bash
    sudo setcap cap_net_raw=eip ~/catkin_ws5/socketPY/bin/python
    ```
3. 設定`tello_driver_node`的解析器為虛擬環境中的那隻蟒蛇，在第一行
    ```python
    #!/home/yuqiang/catkin_ws5/socketPY/bin/python2
    ```
4. 後續一樣`roslaunch tello_driver tello_node`即可


### 參考

* https://medium.com/codeda/ros-run-python-in-virtual-env-8c579304b9c9
* https://medium.com/@badbot/safe-packet-capture-python-without-sudo-b08c4c4e531
* https://stackoverflow.com/questions/36215201/python-scapy-sniff-without-root
* https://answers.ros.org/question/366380/ubuntu1804ros2-dashing-failed-to-load-entry-point-issue/
* https://answers.ros.org/question/165246/launch-node-with-root-permissions/
* https://blog.csdn.net/eqiang8271/article/details/6793089
* https://stackoverflow.com/questions/35229902/why-does-cap-net-raw-not-work-with-so-bindtodevice
* https://stackoverflow.com/questions/1117958/how-do-i-use-raw-socket-in-python
