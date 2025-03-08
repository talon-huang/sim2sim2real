持续更新中~~~  
有问题欢迎在Issues中反馈，欢迎大家一起加入学习。


若启动了conda环境先把环境关闭

```bash 
conda deactivate
```

安装环境
1. 安装ros2_control   
https://github.com/DDTRobot/TITA_ROS2_Control_Sim.git




按照以下命令行拉取编译代码  

仿真部分
```bash 
#拉下代码
git clone https://github.com/talon-huang/sim2sim2real.git

cd sim2sim2real

#拉去其余代码
vcs import < sim2sim2real.repos
```
编译之前先修改这个文件，src/tita_locomotion/tita_controllers/tita_controller/src/fsm/FSMState_RL.cpp
![alt text](/pictures/image.png)
把位置修改为把推理出来的model_gn.engine路径

```bash 
#编译
source /opt/ros/humble/setup.bash && colcon build --packages-up-to locomotion_bringup gazebo_bridge webots_bridge robot_inertia_calculator template_ros2_controller tita_controller joy_controller keyboard_controller

source install/setup.bash 

#启动webots仿真
source /opt/ros/humble/setup.bash && source install/setup.bash && ros2 launch locomotion_bringup sim_bringup.launch.py

#启动键盘指令输入终端
source /opt/ros/humble/setup.bash && source install/setup.bash && ros2 run keyboard_controller keyboard_controller_node --ros-args -r __ns:=/tita


```

实机部分
```bash 
#拷贝文件进入机器
scp -r 你的路径/tita_ros2/src robot@192.168.42.1:~/tita_ros2/

#连接机器：
ssh robot@192.168.42.1
#密码：
apollo

#关掉自启的ros2
systemctl stop tita-bringup.service

cd tita_ros2/

source /opt/ros/humble/setup.bash

#编译
colcon build --packages-up-to locomotion_bringup robot_inertia_calculator template_ros2_controller tita_controller joy_controller keyboard_controller hw_broadcaster

source install/setup.bash 

#连接遥控器
crsf-app -bind

#分别运行
nohup ros2 launch locomotion_bringup hw_bringup.launch.py ctrl_mode:=wbc &
nohup ros2 launch joy_controller joy_controller.launch.py &
```