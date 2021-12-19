# waveshare epaper guide

#### 本教程适用微雪epaper 7.5寸屏v2版本，搭配esp32开发板，其他版本未测试，理论上替换用各自的驱动程序也是可行的

#### 购买链接：[7.5寸墨水屏](https://item.taobao.com/item.htm?ut_sk=1.XfYIWYa87DgDAIXd0h3uTjaJ_21380790_1639874496836.Copy.1&id=558747822216&sourceType=item&price=273&suid=59FFEFE9-8EB1-467C-910E-4833EC9CFB23&shareUniqueId=14173726027&un=e0cc3b15d2028c114c6e4bed9157ce66&share_crt_v=1&un_site=0&spm=a2159r.13376460.0.0&sp_tk=5LiN5Yiw5LiK5LiK5LuW5aSa6YKj552A5piv5bm05LuW&cpp=1&shareurl=true&short_name=h.fijX7EN&bxsign=scdfovVtR9MgM8hxudN8VZo6XA7IRa04vxma1xw4aU1xaPRiDHBXpf5wBajJAiP_8HQitYNj1oBUT1krOqJG0WP3cWNzy5HSP6m-L6_WESIxu0Up9TvnPxgQLFUVd3JolBN&sm=deeff5&app=chrome) [esp32开发板](https://item.taobao.com/item.htm?spm=a1z10.5-c-s.w4002-22579135905.9.7b9e74f6dWa8eS&id=584324378178)

# 开始前准备工作
1. 安装驱动  [windows版驱动](https://www.waveshare.net/w/upload/6/62/CP210x_USB_TO_UART.zip)

# 刷入MicroPython固件
详细步骤移步[官方教程](https://docs.micropython.org/en/latest/esp32/tutorial/intro.html)

1. 安装刷机工具[esptool](https://github.com/espressif/esptool/)
   `pip install esptool`
2. 清除flash，这里的port参数，我电脑实际是com4，如果不是，需要修改
   `esptool.py --port /dev/ttyUSB0 erase_flash`
3. 刷入固件 [固件下载地址](https://micropython.org/download/esp32/)
  `esptool.py --chip esp32 --port /dev/ttyUSB0 --baud 460800 write_flash -z 0x1000 esp32-20190125-v1.10.bin`

# 进入MicroPython交互式解释器（REPL）
详细步骤移步[官方文档](https://docs.micropython.org/en/latest/esp8266/tutorial/repl.html)
1. 通过串口
   `picocom /dev/ttyUSB0 -b115200`
2. 通过网络-WebREPL  http://micropython.org/webrepl (注意需要是http连接)
   先通过有线串口进入REPL，然后执行以下命令开启WebREPL
   `import webrepl_setup`

# 让esp32连接wifi
详细步骤移步[官方文档](https://docs.micropython.org/en/latest/esp8266/tutorial/network_basics.html)
  ```python
   import network
   sta_if = network.WLAN(network.STA_IF)
   sta_if.connect('<your ESSID>', '<your password>')
   sta_if.ifconfig()
  ```
  或封装一个方法
  ```python
   def do_connect():
      import network
      sta_if = network.WLAN(network.STA_IF)
      if not sta_if.isconnected():
          print('connecting to network...')
          sta_if.active(True)
          sta_if.connect('<essid>', '<password>')
          while not sta_if.isconnected():
              pass
    print('network config:', sta_if.ifconfig())
  ```


# MicroPython文件系统
[官方文档](https://docs.micropython.org/en/latest/esp8266/tutorial/filesystem.html)

```python
import os
ls.listdir()
['boot.py', 'port_config.py', 'data.txt']
```
其中boot.py是系统启动文件（系统启动时第一个运行的文件），port_config.py是串口配置文件，data.txt是数据文件

# 上传epaper驱动程序到esp32
先下载对应的micropython版驱动程序：https://github.com/luckylu/micropython-waveshare-epaper

有两种上传文件方式
1. 通过无线网络 WebREPL上传 http://micropython.org/webrepl
2. 通过有线串口 安装
