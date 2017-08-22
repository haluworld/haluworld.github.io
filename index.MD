#mac或者linux下adb连接不上
相信很多开发者或者用户遇到部分手机adb链接不上。在windows下原因很多，很多时候因为adb冲突，在windows上太多的软件有adb链接，各自有各自的adb，例如腾讯的tadb等等。
	下面还是说说Linux系列上的adb连接问题吧
	**1 查看设备VenderID**
	在mac下 或者Linux打开终端（Terminal），linux下可以按快捷键（CTRL＋T，忘了具体是不是大家去试试或者查查），mac下Control＋space，输入Terminal打开。打开终端后，输入命令
	
mac下：
```
system_profiler SPUSBDataType
```
linux下：

```
lsusb
```
结果如下：

```
USB:

    USB 3.0 Bus:

      Host Controller Driver: AppleUSBXHCILPTH
      PCI Device ID: 0x8c31 
      PCI Revision ID: 0x0005 
      PCI Vendor ID: 0x8086 

        Apple Internal Keyboard / Trackpad:

          Product ID: 0x0262
          Vendor ID: 0x05ac  (Apple Inc.)
          Version: 2.25
          Speed: Up to 12 Mb/sec
          Manufacturer: Apple Inc.
          Location ID: 0x14400000 / 4
          Current Available (mA): 1000
          Current Required (mA): 40
          Extra Operating Current (mA): 0
          Built-In: Yes

        BRCM20702 Hub:

          Product ID: 0x4500
          Vendor ID: 0x0a5c  (Broadcom Corp.)
          Version: 1.00
          Speed: Up to 12 Mb/sec
          Manufacturer: Apple Inc.
          Location ID: 0x14300000 / 3
          Current Available (mA): 1000
          Current Required (mA): 94
          Extra Operating Current (mA): 0
          Built-In: Yes

            Bluetooth USB Host Controller:

              Product ID: 0x8289
              Vendor ID: 0x05ac  (Apple Inc.)
              Version: 1.18
              Speed: Up to 12 Mb/sec
              Manufacturer: Apple Inc.
              Location ID: 0x14330000 / 7
              Current Available (mA): 1000
              Current Required (mA): 0
              Extra Operating Current (mA): 0
              Built-In: Yes

        USB Mouse:

          Product ID: 0x00fb
          Vendor ID: 0x0e8f
          Version: 0.01
          Speed: Up to 1.5 Mb/sec
          Manufacturer: YSTEK
          Location ID: 0x14200000 / 2
          Current Available (mA): 1000
          Current Required (mA): 100
          Extra Operating Current (mA): 0

        MX4 Pro:

          Product ID: 0x0c02
          Vendor ID: 0x2a45
          Version: ff.ff
          Serial Number: 0123456789ABCDEF
          Speed: Up to 480 Mb/sec
          Manufacturer: Meizu
          Location ID: 0x14100000 / 29
          Current Available (mA): 1000
          Current Required (mA): 500
          Extra Operating Current (mA): 0

```
我的是MX4 Pro，所以Vendor ID: 0x2a45
**2 添加VendorID**
进入/Users/user/.android，.android为隐藏文件夹，可以通过终端进入直接：

```
cd /Users/user/.android
ls
```
应该有个adb_usb.ini文件，在windows用户目录下也有该文件。添加上上面的Vendor ID

```
# ANDROID 3RD PARTY USB VENDOR ID LIST -- DO NOT EDIT.  
# USE 'android update adb' TO GENERATE.  
# 1 USB VENDOR ID PER LINE.  
0x2a45
```

**3 重启adb命令**
在终端下一次输入：

```
adb kill-server  
adb start-server  
```


	windows下部分手机也可以通过该方法解决，注意windows用户目录与linux系目录地址不一样的。
