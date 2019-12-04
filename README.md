# SLAM_Demos

This project, built on ROS, provides some basic demos of SLAM. The mobile robot Pioneer 3-DX is employed in the project, integrating with two different depth sensors, namely, a laser radar from HOKUYO and an RGB-D camera Kinect. Users are able to conduct SLAM by relying on either of these two sensors and compare the maps generated by them.

## Testing Platform

- Ubuntu: 16.04
- ROS: kinetic
- Gazebo: 8.6

I believe Gazebo 7.0, the default version within ROS kinetic, should also be fine to run this project because it doesn't depend on any version-specific libraries from Gazebo.

## SLAM by Lidar

The following command will open Gazebo and load the robot model in Gazebo.  The launching option `kinetic:=false` can be omitted since it is the default value.

```bash
roslaunch p3dx_gazebo p3dx_gazebo.launch kinect:=false
```

If everything is fine, we can see the robot in Gazebo as shown in the picture below.

![SLAM_by_Lidar_Gazebo](image/SLAM_by_Lidar_Gazebo.png)

Then, the following command will launch a ROS node `gmapping` together with RViz. The launching option `kinetic:=false` is default and can be omitted, too.

```bash
roslaunch p3dx_slam gmapping_rviz.launch kinect:=false
```

After that, the generated map is displayed in RViz.

![SLAM_by_Lidar_RViz](image/SLAM_by_Lidar_RViz.png)

We can then drive the robot in the simulated workspace by sending velocity commands via the ROS node `teleop_twist_keyboard`. A map will be established while the robot is moving around. An example of the generated map is shown below. You can find the picture of this map at `p3dx_slam/maps/map_laser.pgm` together with a configuration file `map_laser.yaml`, which can be used to achieve robot navigation after further development.

![SLAM_by_Lidar_map](image/SLAM_by_Lidar_map.png)



## SLAM by Kinect

It is quite simple to switch from "SLAM by Lidar" to "SLAM by Kinect". The only thing to do is specify the option `kinect:=true` when launching the simulation. Similar with the former example, the following commands will open Gazebo and RViz, and load the robot which is equipped with a Kinect to the simulated workspace.

```bash
roslaunch p3dx_gazebo p3dx_gazebo.launch kinect:=true
roslaunch p3dx_slam gmapping_rviz.launch kinect:=true
```

We can observe that a Kinect has been installed at top of the robot.

![SLAM_by_Kinect_Gazebo](image/SLAM_by_Kinect_Gazebo.png)

By launching the ROS node `teleop_twist_keyboard` and moving the robot around (better in a very slow speed this time), a map will be generated as before. An example map is saved at `p3dx_slam/maps`, which is also shown below. By comparing the results of using the lidar and using the Kinect, it is obvious that the lidar does a better job in SLAM (considerable slippage and distortion can be observed in the map generated by the Kinect).

![SLAM_by_Kinect_map](image/SLAM_by_Kinect_map.png)



## A Third Mode

As seen above, it is more expected to conduct SLAM by using the lidar. However, the Kinect gives a kind of more realistic way to sense the surroundings. This project allows users to build up maps by the lidar while observe surroundings through the Kinect. Have a try with the following commands.

```bash
roslaunch p3dx_gazebo p3dx_gazebo.launch kinect:=true
roslaunch p3dx_slam gmapping_rviz.launch kinect:=false
```

In the `Displays` panel of RViz, click `Add`, select the tag `PointCloud2` and then set the topic to be `/kinect/depth/points`. If everything is fine, you will have a similar case as shown below, where SLAM is conducted by using the lidar and the surroundings are visualized by the Kinect.

![A_Third_Mode_RViz](image/A_Third_Mode_RViz.png)



## Known Issues

Luckily there are few known issues ...

However, there is indeed one operation users should avoid when running simulations, that is, **not launch the simulation like this**.

```bash
roslaunch p3dx_gazebo p3dx_gazebo.launch kinect:=false
roslaunch p3dx_slam gmapping_rviz.launch kinect:=true
```

You will meet problems of building up maps by doing so (although all the components seem to work well) since the ROS node `gmapping`, which is responsible for map construction, expects data from the topic `/kinect/scan` but the robot is not equipped with a Kinect currently.

Check the list of all the ROS topics.

![Known_Issues_Topics](image/Known_Issues_Topics.png)
