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

