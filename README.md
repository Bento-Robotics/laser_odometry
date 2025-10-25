# laser_odometry
Everything to get the Lidar mapping to work:

To get started, download all dependencies first with the following command: 
```
sudo apt install ros-jazzy-slam-toolbox
``` 
Then intitialize a new workspace for your tf2 stuff
```
mkdir -p tf2_ws/src
cd tf2_ws/src
```
Then simply install the rf2o_laser_odometry github: https://github.com/MAPIRlab/rf2o_laser_odometry
``` 
git clone https://github.com/MAPIRlab/rf2o_laser_odometry.git 
cd ..  # go back one folder
colcon build  # compile package
```
After you've finished building, you will have to source your workspace with: 
``` 
source install/setup.bash
```
You can now run r2fo stuff in this terminal.

---
 
We need to tell ROS where the LiDAR is located on the robot.  
For now we will say everything is zero distance apart...  
```
                                   # linear-  x   y   z    x   y   z -rotation
ros2 run tf2_ros static_transform_publisher  0.0 0.0 0.0  0.0 0.0 0.0  base_link laser_frame
                                                                         # from → to
```
And for our 2d LiDAR we want a 2d "floor plane", on which the robot drives around on  
Imagine it like a shadow cast from a light above the robot.  
We will name it `base_footprint`...  
(due to our only-zeroes half-assing this feels redundant, but you will see how useful it is once you expand into 3d simulation and sensing)  
```
ros2 run tf2_ros static_transform_publisher  0.0 0.0 0.0  0.0 0.0 0.0  base_footprint base_link
```
Plus we need to know where our r2fo odometry should be...  
```
ros2 run tf2_ros static_transform_publisher  0.0 0.0 0.0  0.0 0.0 0.0  odom base_footprint
```
And lastly, for the upcoming steps, we need to know the whereabouts of the map we're going to generate...  
```
ros2 run tf2_ros static_transform_publisher  0.0 0.0 0.0  0.0 0.0 0.0  map odom
```

Now run the command:  
```
ros2 run rf2o_laser_odometry rf2o_laser_odometry.launch.py
```
You should see a lot of spamming with lines stating: Laser odom [x,y,yaw]=[...]  
If you only see: waiting for laser_scans..... then something went wrong, make shure your LiDAR is producing scans and your transforms are correct.  


Now for the second part you will start the SLAM mapping software yo generate a map.  
⚠️ It is crucial that you start this *after* r2fo, as mapping requires working odometry, which r2fo provides!  
Simply run the command: 
```
ros2 launch slam_toolbox async_slam_toolbox_node
```
...and you should see a map on your visiualization tool of choice. We use Rviz2.
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

---

Also, a useful note on slam_toolbox:
you can reset your map with:
```
ros2 service call /slam_toolbox/reset slam_toolbox/srv/Reset
```

