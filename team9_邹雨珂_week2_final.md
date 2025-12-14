# Week 2 实验报告

---

## 一、实验目的

1. **Linux 基本命令**：理解并掌握文件管理、权限管理、进程管理等基本命令；
2. **Python 和 C++ 开发环境配置**：安装与配置 Python3、GCC/G++ 编译器，编写并运行简单的程序；
3. **ROS1 Noetic 安装与配置**：安装并配置 ROS1 Noetic，启动 ROS Master、查看节点与话题；
4. **ROS 工作空间与功能包创建**：创建并配置 ROS 工作空间与功能包，编写 HelloWorld 节点；
5. **Turtlesim 控制**：通过键盘、rostopic、程序控制小乌龟；
6. **多乌龟控制与 Launch 文件使用**：通过 Launch 文件同时启动多个乌龟，使用命名空间控制每只乌龟，使用 rqt_graph 可视化通信关系

---

## 二、实验内容与步骤

### 任务 1：Linux 基础命令练习

### 1.1 创建、查看、删除文件

- 创建文件：

```bash
cd ~/Desktop
touch hello.txt

```

- 查看文件内容（空文件会没有内容）：

```bash
cat hello.txt

```

- 复制文件：

```bash
cp hello.txt hello_copy.txt

```

- 移动文件到新目录：

```bash
mkdir test_dir
mv hello.txt test_dir/

```

- 删除文件：

```bash
rm hello_copy.txt

```

- 删除目录：

```bash
rm -r test_dir

```

![1.png](1.png)

![2.png](2.png)

### 1.2 进程管理与终止进程

- 查看所有进程：

```bash
ps aux

```

- 搜索特定进程（例如 Python）：

```bash
ps aux | grep python

```

- 杀死进程（替换 `<PID>` 为实际进程号）：

```bash
kill -9 <PID>

```

---

![13.png](13.png)

### 任务 2：Python 与 C++ 编程练习

### 2.1 Python HelloWorld

1. 创建 Python 文件 `hello.py`：

```bash
cd ~/Desktop
touch hello.py
gedit hello.py

```

2.写入以下代码：

```python
print("Hello ROS from Python!")

```

3.运行：

```bash
python3 hello.py

```

![3.png](3.png)

### 2.2 C++ 程序编写与编译

1. 创建 C++ 文件 `sum.cpp`：

```bash
cd ~/Desktop
touch sum.cpp
gedit sum.cpp

```

2.写入以下代码：

```cpp
#include <iostream>
using namespace std;

int main() {
    int a, b;
    cout << "Enter two numbers: ";
    cin >> a >> b;
    cout << "Sum = " << a + b << endl;
    return 0;
}

```

![4.png](4.png)

1. 编译程序：

```bash
g++ sum.cpp -o sum

```

4.运行程序：

```bash
./sum

```

### 2.3 VSCode 调试环境准备

1. 打开 VSCode：

```bash
code .

```

2.安装必要的插件：`C/C++`、`Python`、`CMake`、`ROS`。

3.创建 `launch.json`，选择 C++ 或 Python 进行调试。

![5.png](5.png)

---

### 任务 3：ROS1 Noetic 安装验证

### 3.1 启动 ROS Master

1. 在终端执行：

```bash
roscore

```

![6.png](6.png)

### 3.2 查看 ROS 节点

1. 在新终端执行：

```bash
rosnode list

```

![7.png](7.png)

### 3.3 查看 ROS 环境变量

1. 执行以下命令查看 ROS 环境变量：

```bash
echo $ROS_DISTRO
echo $ROS_PACKAGE_PATH

```

---

![8.png](8.png)

### 任务 4：ROS 工作空间与功能包

### 4.1 创建 catkin 工作空间

1. 创建工作空间并编译：

```bash
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws
catkin_make

```

2.配置环境变量：

```bash
source devel/setup.bash

```

### 4.2 创建功能包

1. 创建功能包：

```bash
cd ~/catkin_ws/src
catkin_create_pkg beginner_tutorials roscpp std_msgs geometry_msgs

```

2.编译工作空间：

```bash
cd ~/catkin_ws
catkin_make
source devel/setup.bash

```

### 4.3 编写 HelloWorld 节点（C++ 示例）

1. 创建 C++ 文件：

```bash
cd ~/catkin_ws/src/beginner_tutorials/src
touch hello_ros.cpp
gedit hello_ros.cpp

```

2.编写代码：

```cpp
#include <ros/ros.h>

int main(int argc, char** argv)
{
    ros::init(argc, argv, "hello_cpp");
    ros::NodeHandle nh;
    ROS_INFO("Hello ROS from C++!");
    return 0;
}

```

3.修改 CMakeLists.txt 文件：

```
add_executable(hello_ros src/hello_ros.cpp)
target_link_libraries(hello_ros ${catkin_LIBRARIES})

```

1. 编译并运行：

```bash
catkin_make
rosrun beginner_tutorials hello_ros

```

---

![9.png](9.png)

### 任务 5：Turtlesim 小乌龟模块

### 5.1 启动 turtlesim

1. 启动 `turtlesim` 仿真工具：

```bash
rosrun turtlesim turtlesim_node

```

2.启动键盘控制：

```bash
rosrun turtlesim turtle_teleop_key

```

### 5.2 使用 rostopic 控制

1. 发布速度控制：

```bash
rostopic pub /turtle1/cmd_vel geometry_msgs/Twist "{linear: {x: 2.0}, angular: {z: 1.5}}"

```

### 5.3 程序控制小乌龟

1. 创建 C++ 程序控制小乌龟画圆：

```bash
cd ~/catkin_ws/src/beginner_tutorials/src
touch turtle_circle.cpp
gedit turtle_circle.cpp

```

2.编写代码：

```cpp
#include <ros/ros.h>
#include <geometry_msgs/Twist.h>  // 发布乌龟速度需要 Twist 消息类型

int main(int argc, char** argv)
{
    ros::init(argc, argv, "turtle_circle_cpp");
    ros::NodeHandle nh;
    ros::Publisher pub = nh.advertise<geometry_msgs::Twist>("/turtle1/cmd_vel", 10);
    ros::Rate rate(10);  // 设置 10Hz 发布频率
    geometry_msgs::Twist msg;
    msg.linear.x = 2.0;   // 乌龟向前运动
    msg.angular.z = 2.0;  // 同时旋转 → 组合成画圆

    while (ros::ok())

    {
        pub.publish(msg);  // 每次循环都发布指令
        rate.sleep();
    }
    return 0;
}

```

3.编译并运行：

```bash
catkin_make
rosrun beginner_tutorials turtle_circle
￼

```

---

![10.png](10.png)

### 任务 6：多乌龟控制与 Launch 文件

### 6.1 创建一个 Launch 文件

1. 创建并编辑 `multi_turtles.launch` 文件：

```bash
cd ~/catkin_ws/src/beginner_tutorials/launch
gedit multi_turtles.launch

```

2.写入以下内容：

```xml
<launch>
    <node pkg="turtlesim" type="turtlesim_node" name="turtlesim"/>
</launch>

```

### 6.2 启动 turtlesim 并创建第二只乌龟

1. 启动第一个乌龟：

```bash
roslaunch beginner_tutorials multi_turtles.launch

```

2.创建第二只乌龟：

```bash
rosservice call /spawn 5.0 5.0 0.0 "turtle2"

```

### 6.3 查看话题

查看所有话题：

```bash
rostopic list

```

### 6.4 控制两只乌龟

使用 `rostopic` 控制两只乌龟的运动：

```bash
rostopic pub -r 10 /turtle1/cmd_vel geometry_msgs/Twist "{linear: {x: 2.0}, angular: {z: 1.0}}"
rostopic pub -r 10 /turtle2/cmd_vel geometry_msgs/Twist "{linear: {x: 1.0}, angular: {z: -1.0}}"￼￼

```

![11.png](11.png)

![12.png](12.png)

### 6.5 使用 `rqt_graph` 查看通信关系

```bash
rqt_graph

```

### 6.6 使用 `rqt_plot` 绘制乌龟速度图

### 步骤 1：启动 ROS 环境

首先，确保 ROS 环境已经正确启动

```bash
roscore

```

### 步骤 2：启动小乌龟仿真环境

1. 启动 `turtlesim` 仿真工具

```bash
rosrun turtlesim turtlesim_node

```

### 步骤 3：启动 `rqt_plot`

1. 在新的终端中启动 `rqt_plot` 工具

```bash
rqt_plot

```

这将打开 `rqt_plot` 工具的图形界面，可以用来实时绘制 ROS 数据。

### 步骤 4：选择需要绘制的数据

1. 在 `rqt_plot` 窗口的左上角，有一个下拉框用于选择 ROS 话题。点击下拉框，查看 ROS 中发布的所有话题。
2. 你需要选择与小乌龟运动相关的消息话题。例如，如果要查看 **小乌龟的线速度**，选择 `/turtle1/cmd_vel/linear/x`，查看角速度，则选择 `/turtle1/cmd_vel/angular/z`。
3. 在下拉框中选择 `/turtle1/cmd_vel/linear/x` 来绘制小乌龟在 X 方向上的线速度，或选择 `/turtle1/cmd_vel/angular/z` 来绘制乌龟的角速度。

### 步骤 5：发送控制指令，观察速度图

1. 在另一个终端，使用 `rostopic` 发送控制指令来驱动小乌龟进行运动。例如，要让小乌龟沿着直线运动并同时旋转：

```bash
rostopic pub -r 10 /turtle1/cmd_vel geometry_msgs/Twist "{linear: {x: 2.0}, angular: {z: 1.0}}"

```

1. 在执行该指令后，`rqt_plot` 中会实时显示小乌龟的速度图。
    - **线速度图**：选择 `/turtle1/cmd_vel/linear/x`，会看到 X 轴的速度在变化，表示小乌龟在向前运动。
    - **角速度图**：选择 `/turtle1/cmd_vel/angular/z`会看到 Z 轴的角速度变化，表示小乌龟的旋转速度。

### 步骤 6：分析速度图

1. **速度图中的横轴**通常表示时间（以秒为单位），**纵轴**表示速度的大小。
2. 在图中可以看到：
    - 当小乌龟开始运动时，线速度或角速度会在图中显示出来。
    - 如果你改变了运动指令，例如加速或改变角度，图表会相应地发生变化。

## 三、问题与解决过程

### 问题

在整个实验过程中，我遇到了以下几个问题：

1. **路径错误导致找不到 launch 文件**：
    
    由于我在执行 ROS 命令时，路径输入不准确，导致 ROS 无法找到 `launch` 文件。
    
2. **spawner 节点问题**：
    
    在我使用 ROS Noetic 时，尝试通过 `spawner` 节点来生成第二只乌龟，结果发现 ROS 系统报错，无法识别 `spawner` 节点。原因是 ROS Noetic 版本已经移除了该节点，使用 `/spawn` 服务来替代生成乌龟。这一问题让我一度陷入困境，因为在早期版本的 ROS 中是使用 `spawner` 来生成多乌龟的。
    

### 解决过程

1. **路径错误的解决过程**：
    - 在遇到路径错误后，我逐步检查了 ROS 工作空间中的文件夹和文件结构，确保每个包和文件夹的名称都一致。为了解决路径问题，我首先运行了 `tree ~/catkin_ws/src/package1 -L 2` 命令，检查了整个文件结构，确保 `launch` 文件在正确的位置。
    - 我还通过 `roslaunch package1 start_multiple_turtles.launch` 命令重新检查了 `launch` 文件的路径，并且确保了 `package1` 的拼写和文件名没有错误。在此之后，我再次尝试运行并成功启动了两个小乌龟仿真窗口。
2. **spawner 节点的替代解决过程**：
    - 在学习了 ROS1 Noetic 的更新内容后，我发现 `spawner` 节点已经不再被支持，改为使用 `/spawn` 服务来生成第二只乌龟。为了解决这个问题，我修改了 `launch` 文件中的节点配置，使用了以下命令来替代：
        
        ```xml
        <node name="spawn_turtle2" pkg="rosservice" type="rosservice" args="call /spawn 2.0 2.0 0.0 'turtle2'"/>
        
        ```
        
    - 此修改后，第二只乌龟就成功生成，且在两个仿真窗口中都能看到两只乌龟各自独立运行。

---

## 六、实验总结

通过这次实验，我深入理解了 ROS1 Noetic 的工作原理和基本操作流程，也掌握了如何使用 ROS 控制小乌龟并进行多乌龟控制。实验过程不仅加深了我对 ROS 节点、话题、服务等概念的理解，还让我更加熟悉了使用命令行进行调试和修改的方式。

在进行实验时，我遇到了路径错误和 spawner 节点无法识别的问题，这让我意识到 ROS 在不同版本中的变化，特别是在命名空间管理和服务调用方面的差异。通过不断调试和查阅 ROS 官方文档，我逐步解决了这些问题，最终成功实现了多乌龟控制。

实验中最令我印象深刻的是 ROS 系统如何通过话题和命名空间来管理节点间的通信。通过 `rostopic` 和 `rqt_graph`，我能够实时观察到每个节点和话题的通信状态。在处理多乌龟控制时，通过合理设置命名空间，可以确保每只乌龟的控制命令互不干扰，这为后续开发更复杂的系统打下了基础。

通过这次实验，我不仅提高了自己对 ROS 系统的理解，还熟悉了 ROS 编程的基本流程和调试方法。