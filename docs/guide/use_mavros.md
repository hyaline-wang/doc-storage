---
sidebar: auto
---


# mavros

> ros1 noetic

## mavros 转发至QGC

```bash
# 自动连接至打开的qgc
roslaunch mavros px4.launch gcs_url:=udp//:14550@-b

# 转发至 target_ip 设备上的 QGC
roslaunch mavros px4.launch gcs_url:=udp//:14550@<target_ip>:14550

```

## local_position

local_position的默认频率应该为30hz，如果低于此频率，表明ekf定位可能没融合出来。

```bash
rostopic hz /mavros/local_position/pose
```

## 