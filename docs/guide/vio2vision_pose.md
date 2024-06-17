# vio->vision_pose

> 以下是 debug 过程

- 原来的控制方法不够稳定，体现在当状态不好时悬停都十分困难。
- 测试这个是因为ego_planner带的控制没有很好的跟踪轨迹，有时候期望和目标位置能差20cm,躲障碍时问题就很大，就想试试直接发p a v 给px4

## **现象**: VIO 接入 vision_pose 就崩掉

阅读了源码，发现:

- 早先的测试中，6c 跑vins几乎没有正常过，莫名其妙的飘，原因基本可以确定是地磁的原因，vins拿到的imu数据被px4中的EKF做了处理(EKF对传感器数据修正)，因此磁场差的时候vins就会崩掉，把罗盘关掉直接就好了。
- 这种机制造成了一个问题，如果把vins的数据作为vision_pose 发给px4 ,让px4做EKF，会崩掉，我在固件中将对EKF对imU数据修正的处理去掉后，就不再崩了。

## 消除bias 

修改固件后VINS精度变差的原因排查，猜测原因如下

- 传感器校准
- 外参z轴没有标定好 (猜测是主要原因)
- 原来的EKF对Z轴的精度提升有影响，也许融合了气压计的值

始终没有将VINS标定到一个非常合适的值

## 去掉 offset 估计

### 飞行状态变差

尝试了控制定点非常稳定，但是发现画不了圆(掉高，完全称不上是圆)。

[MAVROS Offboard control example (C++) | PX4 User Guide](https://docs.px4.io/main/en/ros/mavros_offboard_cpp.html)

### 结论

1. 由于VIO的 数据来源data_raw 的数据被 EKF处理过，所以不能将VIO计算出的位姿信息反给飞控做EKF。
2. 可以修改固件使得data_raw 不被EKF处理，但是这样会出现两个隐患
    1. 很难标定到一个非常好的外参，感觉每次都会有一些变化(直观感受)
    2. 手举着飞机时感觉一切正常，但是飞起来后效果就差很多了，除了悬停很稳外，剩下的几乎不可用。
3. 可以只用双目来解决 2.b出现的问题，但是飘的太快了，特别是高度（一个来回飘1m）。
    
4. 由1,2,3 可推测
    1) data_raw 不是真正的raw，校正的意义是比较大的。
    2) 真正的raw效果并不好
    如果实在想实现，可能必须要移植滤波算法了。
5. 可以使用一个临时的解决方法，两个飞控，一个专门用来飞行，一个专门作为VIO的数据源，看起来非常的笨，但是实测效果很不错，完全可用。
    
6. 因此先用 5.的方法实现多机，写一些脚本使得使用简单一些，同时购买挑选合适的IMU，有机会也真正测一下D435i上的IMU吧。


# PVA 参数调试
## Practise
```cpp
//不能这样写，注意坐标系
mpCtrl.type_mask = mpCtrl.IGNORE_YAW_RATE|mpCtrl.IGNORE_AFY|mpCtrl.IGNORE_VY;

```
[mavros_msgs/PositionTarget Documentation](http://docs.ros.org/en/api/mavros_msgs/html/msg/PositionTarget.html)

MPC_XY_P 0.95→1.2

MPC_XY_VEL_P_ACC 1.8→1.2

修改 MPC_JERK_AUTO -> default 4 ->15

## 参考源码与文档
对比了源码与文档，有了新的发现

[[px4 - Multicopter Position Control 细究]]


## VIO 输出作为 vision_pose

这两天有一些其他的进展，之前vins飘的原因应该算完全找到了，vins拿到的imu数据被px4中的EKF做了处理(EKF对传感器数据修正)，因此磁场差的时候vins就会崩掉，把罗盘关掉直接就好了。 这种机制造成了一个问题，如果把vins的数据作为vision_pose 发给px4 ,让px4做EKF，会崩掉，我在固件中将对EKF对imU数据修正的处理去掉后，就不再崩了。（研究这个是因为ego_planner带的控制没有很好的跟踪轨迹，有时候期望和目标位置能差20cm,躲障碍时问题就很大，就想试试直接发p a v 给px4，然后发现了这个问题）
但是 这样的VINS效果并不好，特别是飞机飞起来的时候。


