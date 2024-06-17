---
sidebar: auto
---

# Px4参数修改

> 针对V1.13.3

## 禁用 Mag

```
SYS_HAS_MAG Disabled
EKF2_MAG_TYPE None
```

## 使用TF mini

- 插线
- 修改 ->  SENS_TFMINI_CFG
- 查看是否有DISTANCE



## 使用 RTK

定高收敛慢

> 切换目标高度时，飞行器高度震荡，发现local_pose没有很好的跟随GPS的z轴数据，

```
EKF2_GPS_NOISE 0.5 -> 0.1
```

## 
