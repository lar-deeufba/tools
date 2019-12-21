# UR5 and Robotiq 2 Gripper Setup
This tutorial assumes you have Ubuntu 16.04 running with ROS Kinetic. Everything should be doable with the 18.04 as well but changing the distro from ``` kinetic ``` to ``` melodic ```.

## Initial Setup
UR5 is usually tethered with an ethernet cable, meaning it has an IP address. In order to connect with it, we have first to check
for the IP from UR5, which can be found at:

![initialscreen](https://user-images.githubusercontent.com/24254286/71302030-409ee880-2385-11ea-8741-c68eb76c2b85.jpg)

Now the setup can be done in Ubuntu, go to ``` Network Connections ``` and create a new connection:

![secondscreen](https://user-images.githubusercontent.com/24254286/71302100-329d9780-2386-11ea-8a12-cc6e0517e8a9.png)

Go to IPv4 settings and set the connection as Manual, not DHCP so the IP doesn't change:

![thirdscreen](https://user-images.githubusercontent.com/24254286/71302101-329d9780-2386-11ea-95a2-e310abe2b3c0.png)

Since the computer has to be in the same domain as the UR5, then we choose an arbitrary ending for the IP, in this case we chose 34.

## UR5 Installation
Install some dependencies that were not covered from rosdep:

``` $ sudo apt-get install ros-kinetic-moveit ```

``` $ sudo apt-get install ros-kinetic-industrial-msgs ``` 


Clone the official Universal Robot package in your catkin workspace:

```  $ git clone -b kinetic-devel https://github.com/ros-industrial/universal_robot.git ``` 

Install required dependencies in an easier way by using rosdep:

``` $ rosdep update ```

``` $ rosdep install --from-paths src --ignore-src --rosdistro kinetic ```


Since we're not getting the newest version, in order to bring the robot up we have to install
the ``` ur_modern_driver ``` in the catkin workspace:

```  $ git clone -b kinetic-devel git@github.com:ros-industrial/ur_modern_driver.git ```

Go to the root of your catkin workspace and:

``` $ catkin_make ``` 


to bring the robot up:

``` $ roslaunch ur_modern_driver ur5_bringup.launch robot_ip:=192.168.131.12 ``` 


## Using the Robotiq 2 Gripper

Permission to open the gripper's port

``` $ sudo chmod 777 /dev/ttyUSB0``` 

Interfacing the gripper with ROS:

``` $ rosrun robotiq_2f_gripper_control Robotiq2FGripperRtuNode.py /dev/ttyUSB0``` 

To make the usage easier we create an alias that concatenates both commands:

``` $ cd ~/ ```

``` $ echo "open-gripper() { sudo chmod 777 /dev/ttyUSB0 ; rosrun robotiq_2f_gripper_control Robotiq2FGripperRtuNode.py /dev/ttyUSB0 ; }" >> .bashrc ```

## Troubleshooting

If the gripper doesn't open or close:

``` $ rosrun rfinger reset  ```

``` $ rosrun rfinger init  ```

![Reasonhydowngrade](https://github.com/ros-industrial/universal_robot/issues/183)

