本仓库为小鱼《ROS 2 机器人开发从入门到实践》第 6 章 Fishbot 机器人代码实践。
已知 Bug：6.5.2 Gazebo 接入 ros2_control
复现命令：

ros2 launch fishbot_description gazebo_sim.launch.py

现象

    Gazebo 仿真正常启动，机器人模型成功 spawn；
    执行 ros2 topic list | grep controller 无任何相关话题；
    使用 ros2 control list_controllers 无法连接 controller_manager 服务；
    在 rqt 中同样看不到 controller 相关话题。

尝试：使用教程原版源码，依然复现该问题；经过多轮调试仍未能解决。
推测：Humble 下gazebo_ros2_control插件参数解析相关版本坑。
如果有大佬发现问题根源，欢迎交流：a123456660822@163.com

我将我运行时的报错信息放在/src/fishbot_description/bug.txt

经过摸索，已经解决

原因：xacro/URDF 里混进了 # 注释

XML 只有 <!-- --> 是注释，# 会被 xacro 当成普通文本节点保留进 URDF。
gazebo_ros2_control 把整个 URDF 作为 --param robot_description:=<...> 传给 rcl 时解析失败，报：

[gazebo_ros2_control]: parser error
Couldn't parse parameter override rule: '--param robot_description:=<?xml version="1.0" ?>
... at ./src/rcl/arguments.c:343

我之前在chapt6_ws/src/fishbot_description/urdf/fishbot/actuator/caster.urdf.xacro 和 /home/jaron/chapt6/chapt6_ws/src/fishbot_description/urdf/fishbot/actuator/wheel.urdf.xacro 都加上了#

错误实例：

<gazebo reference="${wheel_name}_link">
    <mu1 value="20.0"/>  #切向摩擦系数
    <mu2 value="20.0"/>  #法向摩擦系数
    <kp value="1000000000.0"/>  #接触刚度系数
    <kd value="1.0"/>  #阻尼系数
</gazebo>

同时在diff小鱼源码和我的代码时，把他们都放在了中文路径下，导致无法解析，一直无法排除真正的问题


