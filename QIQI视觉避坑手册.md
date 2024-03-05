# 视觉避坑手册-RM-QIQI

---

## ROS2必备工具/包
- ROS2-humble
- concol
- python3
- camera-calibration

---

## 编译/包


- 每次修改src中的源代码后一定要在/home/dev_ws中重新编译一遍，将更改后的源代码编译并替换掉install文件夹中的旧版本编译后文件，如此才能实现代码的更新
```shell
colcon build	//重新编译的指令
ros2 run	//该命令执行的是install文件夹中的文件，故需要更新其中文件才能在终端中运行新版代码
```

- 每个具体到节点的代码文件，在修改后都需要在对应父类文件夹的setup.py文件中进行相应的配置：
找到entry_points={...}
修改大括号内的内容即可（初次配置好后默认就已经是完整的，无需再配置）

- 在ubuntu上用命令行下载安装某些包时最好保证较好的网速，而且部分包在安装时会定位到外网，也就需要挂梯子，如在conda虚拟环境中用pip安装torch相关工具包(这个是在ubuntu22.04上安装ROS1-noetic用的，即ROS2-humble与ROS1-noetic共存，22级这届由于孙队的哨兵导航是在ubuntu20.04上ros1-noetic框架下写的，而各个兵种的自瞄代码都部署的是陈军的开源代码，只在ubuntu22.04上的ros2-humble成功运行过，所以在找能让ros1和ros2在同一个ubuntu系统上共存的方案)
> ubuntu系统的梯子可以用Clash，免费注册一个，有免费的加速节点，第一次用之前还要手动配置一下网络代理IP，之后上外网就先切换为手动网络代理+开启Clash，如果是访问国内网站如csdn等就再切换回自动代理即可

---

## 相机


- 调用hik_camera前一定先source对应工作空间里的.bash/.sh文件(一般是用.bash文件)

- 每次进行完相机标定后需要将opt.yaml改名为camera_info.yaml(与包中的相机参数文件名保持一致)，然后在以下两个路径中替换掉原参数文件，分别是：`./RM-Vision-main/ws/src/ros2_hik_camera-main/config`,`/RM-Vision-main/ws/src/rm_vision-main/rm_vision-bringup/config`

> 安装camera_calibration包时不要盲目跟教程直接调用！！！
> ```shell
> sudo apt install ros-galactic-camera-calibration-parsers
> ```
> 上面这条命令是从csdn教程上抠的，它会在`/opt/ros/humble/`中下载并安装`camera-calibration-parsers`，而不是多数教程会用到的`camera-calibration`
所以一般会调用下面这条下载安装命令
> ```shell
> sudo apt install ros-humble-camera-calibration
> ```

---

## 仿真


- 调出`rqt`或`rviz2`后可以查看经过cv处理后的图像result_img，在其中心处有绘制一个红色小圆圈作为图像中心点，与中心点绘制相关的代码为`rm_auto_aim-main/armor_detector/src/detectoe.cpp`中的`cam_center`参数部分

---

## 调车(与电控联调)


整车进行实战训练前的一次完整自瞄调试流程

#### 1. 确认弹速
弹速是后续调车的

#### 2. 安装相机并修改其相对位姿参数(xyz/rpy)

#### 3. 修改枪口前推距离和枪口垂直距离

#### 4. 打靶测试
  1. 打1m靶 -> 此时可视作无空气阻力影响的理想情况，要保证击打位置在正中心
  2. 打3m靶 -> 有空气阻力影响，需要调空气阻力系数，使击打位置下坠程度在可控范围内
  3. 打5m靶 -> 空气阻力影响较大，继续调空气阻力系数，保证能打中装甲板即可
  4. 测试击打旋转/平移中的实车装甲板 -> 调机器人固有偏差(预测时间)，使旋转和平移两种情况下的最终击打效果均为良好即可

---

## 参赛前准备


- 出发前10天操作手训练，期间