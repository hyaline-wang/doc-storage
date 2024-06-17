---
sidebar: auto
---

# Vim Edge2 固件编译

## 如何手动编译固件
khadas为他们家的板子提供了一键编译固件的方法。[https://github.com/khadas/fenix](https://github.com/khadas/fenix)可以使用docker 快速配置环境
容器创建好了后只有条命令需要执行。
```bash nums
source env/setenv.sh
DOWNLOAD_MIRROR=china make
```

docker 在第一次配置时有点慢，之后在编就快很多了，8分钟左右能编完。
如果想用 windows 上的rk-dev-tool 进行烧录，那么 **不选 raw** 

编译固件时，需要使用/dev/loop 设备，但是 snap 似乎会占用掉，找到了一个办法，删掉snap，虽然不优雅，但是管用，似乎也没有什么副作用。

[https://blog.csdn.net/shyjhyp11/article/details/109839033](https://blog.csdn.net/shyjhyp11/article/details/109839033)




## 中文支持

似乎默认的 dbus 有问题，一直无法安装语言，报错为
```bash
FileNotFoundError: [Errno 2] 没有那个文件或目录: '/var/crash/_usr_bin_gnome-language-selector.0.crash'
```
提示中，中文是乱码

命令行运行设置，报错为
``` bash
** (gnome-control-center:2905): WARNING **: 10:10:00.953: Error calling GetAll() when retrieving properties for /org/freedesktop/Accounts/User0: GDBus.Error:org.freedesktop.DBus.Error.UnknownMethod: No such interface “org.freedesktop.DBus.Properties” on object at path /org/freedesktop/Accounts/User0
Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/dbus/bus.py", line 177, in activate_name_owner
    return self.get_name_owner(bus_name)
  File "/usr/lib/python3/dist-packages/dbus/bus.py", line 361, in get_name_owner
    return self.call_blocking(BUS_DAEMON_NAME, BUS_DAEMON_PATH,
  File "/usr/lib/python3/dist-packages/dbus/connection.py", line 652, in call_blocking
    reply_message = self.send_message_with_reply_and_block(
dbus.exceptions.DBusException: org.freedesktop.DBus.Error.NameHasNoOwner: Could not get owner of name 'org.debian.apt': no such name

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/defer/__init__.py", line 487, in _inline_callbacks
    result = gen.send(result)
  File "/usr/lib/python3/dist-packages/aptdaemon/client.py", line 1623, in _run_transaction_helper
    daemon = get_aptdaemon(self.bus)
  File "/usr/lib/python3/dist-packages/aptdaemon/client.py", line 1709, in get_aptdaemon
    return dbus.Interface(bus.get_object("org.debian.apt",
  File "/usr/lib/python3/dist-packages/dbus/bus.py", line 241, in get_object
    return self.ProxyObjectClass(self, bus_name, object_path,
  File "/usr/lib/python3/dist-packages/dbus/proxies.py", line 250, in __init__
    self._named_service = conn.activate_name_owner(bus_name)
  File "/usr/lib/python3/dist-packages/dbus/bus.py", line 182, in activate_name_owner
    self.start_service_by_name(bus_name)
  File "/usr/lib/python3/dist-packages/dbus/bus.py", line 277, in start_service_by_name
    return (True, self.call_blocking(BUS_DAEMON_NAME, BUS_DAEMON_PATH,
  File "/usr/lib/python3/dist-packages/dbus/connection.py", line 652, in call_blocking
    reply_message = self.send_message_with_reply_and_block(
dbus.exceptions.DBusException: org.freedesktop.DBus.Error.Spawn.ExecFailed: Failed to execute program org.debian.apt: Permission denied
Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/dbus/bus.py", line 177, in activate_name_owner
    return self.get_name_owner(bus_name)
  File "/usr/lib/python3/dist-packages/dbus/bus.py", line 361, in get_name_owner
    return self.call_blocking(BUS_DAEMON_NAME, BUS_DAEMON_PATH,
  File "/usr/lib/python3/dist-packages/dbus/connection.py", line 652, in call_blocking
    reply_message = self.send_message_with_reply_and_block(
dbus.exceptions.DBusException: org.freedesktop.DBus.Error.NameHasNoOwner: Could not get owner of name 'org.debian.apt': no such name

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/defer/__init__.py", line 487, in _inline_callbacks
    result = gen.send(result)
  File "/usr/lib/python3/dist-packages/aptdaemon/client.py", line 1623, in _run_transaction_helper
    daemon = get_aptdaemon(self.bus)
  File "/usr/lib/python3/dist-packages/aptdaemon/client.py", line 1709, in get_aptdaemon
    return dbus.Interface(bus.get_object("org.debian.apt",
  File "/usr/lib/python3/dist-packages/dbus/bus.py", line 241, in get_object
    return self.ProxyObjectClass(self, bus_name, object_path,
  File "/usr/lib/python3/dist-packages/dbus/proxies.py", line 250, in __init__
    self._named_service = conn.activate_name_owner(bus_name)
  File "/usr/lib/python3/dist-packages/dbus/bus.py", line 182, in activate_name_owner
    self.start_service_by_name(bus_name)
  File "/usr/lib/python3/dist-packages/dbus/bus.py", line 277, in start_service_by_name
    return (True, self.call_blocking(BUS_DAEMON_NAME, BUS_DAEMON_PATH,
  File "/usr/lib/python3/dist-packages/dbus/connection.py", line 652, in call_blocking
    reply_message = self.send_message_with_reply_and_block(
dbus.exceptions.DBusException: org.freedesktop.DBus.Error.Spawn.ExecFailed: Failed to execute program org.debian.apt: Permission denied

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "/usr/lib/python3/dist-packages/aptdaemon/client.py", line 1582, in _run_transaction
    deferred = self._run_transaction_helper(method_name, args, wait,
  File "/usr/lib/python3/dist-packages/defer/__init__.py", line 597, in unwind_generator
    return _inline_callbacks(None, func(*args, **kwargs), Deferred())
  File "/usr/lib/python3/dist-packages/defer/__init__.py", line 533, in _inline_callbacks
    deferred.errback()
  File "/usr/lib/python3/dist-packages/defer/__init__.py", line 359, in errback
    self._next()
  File "/usr/lib/python3/dist-packages/defer/__init__.py", line 414, in _next
    sys.excepthook(self.result.type, self.result.value,
  File "/usr/lib/python3/dist-packages/apport_python_hook.py", line 153, in apport_excepthook
    with os.fdopen(os.open(pr_filename,
FileNotFoundError: [Errno 2] 没有那个文件或目录: '/var/crash/_usr_bin_gnome-language-selector.0.crash'

```


[https://askubuntu.com/questions/789966/software-updates-crashes-and-will-not-open](https://askubuntu.com/questions/789966/software-updates-crashes-and-will-not-open)

似乎是dbus 的问题，解决方法
```bash
sudo aptitude reinstall apt apt-utils aptdaemon aptdaemon-data update-manager update-manager-core dbus
```




## 软件安装

### realsense_sdk


### realsense driver install
```bash
sudo apt-get install libusb-1.0-0-dev
sudo apt-get install xorg-dev libglu1-mesa-dev
git clone https://github.com/IntelRealSense/librealsense.git
cd 
mkdir build
cd build
cmake ..
make -j8
sudo make install
```

https://github.com/openMVG/openMVG/issues/85
https://github.com/IntelRealSense/librealsense/issues/5518


**D430i**
当D435i 缺少RGB摄像头时，设备会被识别为D430i。在x86设备中 realsense-veiwer 会将其识别，并正常使用，但是当使用realsense-ros,会提示
> Unsupported device! Product ID: 0x0B4B


幸运的是 在4.54.1 (2023-06-27) 版本中，添加了这个支持 ，可惜，新版本不支持ROS1
https://index.ros.org/p/realsense2_camera/

### foxglove

edge2 的性能远远不如 nuc ，因此开一个 rviz基本上性能就占用完了。
可以使用 foxglove 远程可视化，注意不要传图像
```bash
# https://github.com/foxglove/ros-foxglove-bridge
sudo apt install ros-$ROS_DISTRO-foxglove-bridge

roslaunch --screen foxglove_bridge foxglove_bridge.launch port:=8765
```
launch 的写法
```xml
<launch>
  <!-- Including in another launch file -->
  <include file="$(find foxglove_bridge)/launch/foxglove_bridge.launch">
    <arg name="port" value="8765" />
    <!-- ... other arguments ... -->
  </include>
</launch>
```

## 接口信息

注意 ：有一个是**usb 2.0 **。



| 特征 | 值             |
| ---- | ------------- |
| 重量 | 57g(含散热器)  |
| 尺寸 |               |
| 功率 |   12v 2A （需要稳压）     |
| 性能 |               |

