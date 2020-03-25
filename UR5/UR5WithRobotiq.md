# UR5 [Real robot and Offline Simulation] and Robotiq 2 Gripper Setup
This tutorial assumes you have Ubuntu 16.04 running with ROS Kinetic. Everything should be doable with the 18.04 as well but changing the distro from ``` kinetic ``` to ``` melodic ```. Instructions for the UR5 with offline simulation are after installing the basic packages that serve for both online and offline work.

## Initial Setup
UR5 is usually tethered with an ethernet cable, meaning it has an IP address. In order to connect with it, we have first to check
for the IP from UR5, which can be found at:

![initialscreen](https://user-images.githubusercontent.com/24254286/71302030-409ee880-2385-11ea-8741-c68eb76c2b85.jpg)

Now the setup can be done in Ubuntu, go to ``` Network Connections ``` and create a new connection:

![secondscreen](https://user-images.githubusercontent.com/24254286/71302100-329d9780-2386-11ea-8a12-cc6e0517e8a9.png)

Go to IPv4 settings and set the connection as Manual, not DHCP so the IP doesn't change:

![thirdscreen](https://user-images.githubusercontent.com/24254286/71302101-329d9780-2386-11ea-95a2-e310abe2b3c0.png)

Since the computer has to be in the same domain as the UR5, then we choose an arbitrary ending for the IP, in this case we chose 34.

## UR5 Installation (General)
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

to bring the REAL (online) robot up:

``` $ roslaunch ur_modern_driver ur5_bringup.launch robot_ip:=192.168.131.12 ``` 

Obviously the IP shoud be the IP that you have set on ``` Initial Setup ```

## UR5 Offline Simulation
To interface with the real robot we have to use the ```ur_modern_driver``` which is actually deprecated, but it's what works with the real robot, so we should keep the same configuration as above, only with the additional of the offline simulator from Universal Robots, ```URSim```.

**The version to be downloaded should be <= 3.9.1** because newer versions aren't compatible with the ```ur_modern_driver``` discussion about this topic can be found [here](https://github.com/ros-industrial/ur_modern_driver/issues/316).

Download the URSim from [here](https://www.universal-robots.com/download/?option=50483#section16632).

Follow these instructions to install it (taken from the download page itself): e.g: version 3.9.0.64176

``` $ cd ~ ```
``` $ tar xvzf URSim_Linux-3.9.0.64176.tar.gz ```
``` $ cd ursim-3.9.0.64176 ```
``` $ ./install.sh ```

To run it manually:

``` $ cd .. ```
``` $ ursim-3.9.0.64176/start-ursim.sh ```

You should get this starting screen:

![initialScreen](https://user-images.githubusercontent.com/24254286/77501500-fcfdc400-6e36-11ea-84a3-3d4b724d11f3.png)

Click on ```Run Program``` then ```Move``` to move the robot, check the visual from the robot to see it moving!

![moveJointEx](https://user-images.githubusercontent.com/24254286/77501502-fec78780-6e36-11ea-8391-44f3eb24d6a0.jpg)

Now, i recommend you to create an alias to ease your life when running the offline simulator by:

``` $ echo "alias ur5='cd ~/ursim-3.9.0.64176 ; ./start-ursim.sh ;'" >> .bashrc ```

Now, if you want to run the offline simulator:

``` $ ur5 ```

Now run the ROS node to interface with the simulator, same as before but using the localhost IP:

``` $ roslaunch ur_modern_driver ur5_bringup.launch robot_ip:=127.0.0.1 ``` 

Try to move the ``` wrist_3_joint ``` joint manually on the teach pendant and check if this joint moves accordingly by echoeing on ROS:
moving the joints and in parallel image

``` $ rostopic echo /joint_states/position ```

![example](https://user-images.githubusercontent.com/24254286/77501492-f96a3d00-6e36-11ea-8a17-0b3949fe8453.png)

You should see the last value from the list varying, which matches the wrist3 joint position:

``` [-1.6007002035724085, -1.7271001974688929, -2.2029998938189905, -0.8079999128924769, 1.5951000452041626, 0.10260496288537979] ```


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

Why should we downgrade to use ```ur_modern_driver``` you can find [here](https://github.com/ros-industrial/universal_robot/issues/183).

When using older versions from URSim (<= 3.4.0) usually the installation shell script uses jdk 6, which is no more found on the repositories, [this](https://github.com/arunavanag591/ursim) repository has a tutorial how to manually do it correctly.

Helpful links:
https://answers.ros.org/question/276468/how-to-connect-to-ur-sim-from-ros/
https://answers.ros.org/question/243601/cant-establish-connection-to-ur5-using-ros-industrial-driver/

