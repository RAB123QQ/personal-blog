---
title: UR10e仿真环境搭建教程
publishDate: 2026-06-09 20:22:00
description: 'UR10e机械臂的仿真环境搭建教程'
tags:
  - UR机械臂
heroImage: { src: './ur_arm.png', color: '#3d78bd' }
language: '中文'
---
**环境要求**

```
ubuntu 
ros
docker
moveit
我的环境：ubuntu24.04 ，ros2 jazzy
```

## ur_robot_driver
首先需要安装ur_robot_driver这个软件包

可以安装二进制包，也可以从源代码构建，如果不需要改源码，一般推荐从二进制包安装

```
 sudo apt-get install ros-${ROS_DISTRO}-ur
```
安装moveit
```
sudo apt install ros-${ROS_DISTRO}-moveit
```


## docker的配置

```
# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update               #设置apt仓库

sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin  #安装dockers
```

这三个命令可以在运行docker的时候不用在前面加sudo
例如 
sudo docker ps  变成 docker ps 

```

sudo groupadd docker  #创建 docker 用户组（通常安装时已自动创建）

sudo usermod -aG docker $USER    #将当前用户加入docker组

newgrp docker  #激活更改 (或者直接重启)
```

## URsim

URSim 是 Universal Robots 公司的离线仿真器。它可以部署在远程主机或虚拟机中，其运行方式几乎与通过网络连接的真实机器人完全相同。

URSim的界面和示教器的界面是一模一样的

官方把URSim的环境封装好了放在docker里面

下载好ur_robot_driver之后运行

```
ros2 run ur_client_library start_ursim.sh -m ur10e
```

这将启动一个预装了 URSim Docker 容器 ip为`192.168.56.101`
然后会出现一个网址，把他复制到浏览器里面打开，然后点击连接，启动机械臂，然后再点击external control，然后点播放的按钮，这样就启动了机械臂并且可以通过192.168.56.101控制机械臂了


打开rviz2 ，如果连接正常，这个时候应该会看到机械臂在rviz里面
```
ros2 launch ur_robot_driver ur_control.launch.py ur_type:=ur10e robot_ip:=192.168.56.101
```

启动moveit规划机械臂运动路径
```
ros2 launch ur_moveit_config ur_moveit.launch.py
```