# laser_odometry
Everything to get the Lidar mapping to work
To get started, download all dependencies first with the following command: 
```
sudo apt install ros-humble-slam-toolbox
``` 
Then intitialize a new workspace for your tf2 stuff
```
mkdir tf2_ws
```
as well as a source folder in the new workspace
```
cd tf2_ws

mkdir src
```
Then simply clone the rf2o_laser_odometry github: https://github.com/MAPIRlab/rf2o_laser_odometry
``` 
git clone https://github.com/MAPIRlab/rf2o_laser_odometry.git 
``` 
After you've cloned the git repo, simply go back one folder 
``` 
cd .. 
```
and then build with: 
``` 
colcon build 
```
After you've finished building, you will have to source your entire build with: 
``` 
source install/setup.bash
``` 
Now you can run the command: 
``` 
ros2 launch rf2o_laser_odometry rf2o_laser_odometry.launch.py 
``` 
You should see a lot of spamming with a line inbetween stating: Laser odom [x,y,yaw]=[]
If you only see: waiting for laser_scans..... then something went wrong, probably because you dont have a lidar connected to your device. 
**Info:** If you dont start the command first the following command(s) will give you error messages saying they cant find the laser scan. That is because the command stated above runs the tf2 transformations that tell the programm where the robotor is on the map, which are curcial for the process to work.

Now for the second part you will start the actual mapping software with the dependencies youve installed. 
Simply run the command: 
```
ros2 run slam_toolbox async_slam_toolbox_node --ros-args -p base_frame:=base_link 
```
and you should see a map on your visiualization tool, we use Rviz2
If you still dont see any map, you might want to check if your topic in your commands and in Rviz2 match, or if you even have any topics at all. 
If you want to check if you have any topics at all simply type into your command line: 
```
ros2 topic list
```
Lastly the topic you need to check in Rviz2 are: 
- Fixed Frame should be base_link 
- Click add, search by topics, and add pose
- Click add, search by topics, and add laserScan and make sure the topic is /scan
- Click add, search by topics, and add Map

Now you should have a working map to see!
(if you want to not have to repeat the last 3 steps, simply press Ctrl and S while in Rviz2 to save it as a preset)
