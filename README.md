# 基于CH343芯片的UART通信上位机处理
## 1. 环境

* 软件环境
  * Ubuntu 22.04 
  * ROS2 humble
  *  [CH343的Linux环境下的vcp驱动](https://github.com/WCHSoftGroup/ch343ser_linux)
  * 配套的下位机代码处理

* CH343的Linux环境下的vcp驱动安装
  * 需要注意的是驱动安装时需要使用gcc12,并且需要将已经Linux原有的cdc-acm驱动删除

  * 删除cdc-acm驱动
  ```
  sudo rmmod cdc-acm
  ls /dev/ttyACM*
  ```
  * 安装驱动,需要在安装前关闭security boot, 否则有可能出现签名认证失败的报错.具体安装详见驱动的README.md
  ```
  git clone https://github.com/WCHSoftGroup/ch343ser_linux
  ```
  * 更换gcc版本
  ```
  sudo apt install gcc-12
  sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-12 100 --slave /usr/bin/g++ g++ /usr/bin/g++-12 --slave /usr/bin/gcov gcov /usr/bin/gcov-12
  sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-11 80 --slave /usr/bin/g++ g++ /usr/bin/g++-11 --slave /usr/bin/gcov gcov /usr/bin/gcov-11
  gcc --version        // if it is 12, that is ok
  ```
  
  * 检查,如果有/dev/ttyUSBCH3430(1/2),就可以了.

    ```
    ls /dev/tty* 
    ```

## 2.文件说明

## 3.实际效果及测试数据

### 3.1实验条件：
  * UART: 2M异步，stop bits = 2,  oversample = 16, simple sample = true, 无校验位，start bit = 1, 数据位8bits

  * 上位机（同一个serial driver node. 开tx与rx线程）
    * TX: 五个Node 1ms txcallback, 每个callback分别传两种CMD
        * TWOCRC_CHASSIS-CMD
        * TWOCRC-GIMBAL-CMD
        * TWOCRC-SENTRY-GIMBAL-CMD
        * TWOCRC-GIMBAL-MSG
      TWOCRC-ACTION-CMD
    * RX: 单个线程read + 通用decode + publish

  * 下位机：
    * TX: 调用NewRosComm transmit API, 每毫秒传输五个TWOCRC-GIMBAL-MSG
    * RX：下位机通用decode
    

## 4.未来优化方向
  * 
