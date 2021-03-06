# OBSTACLE AVOIDANCE

The objective of this practice is to implement the logic of the VFF navigation
algorithm.

Navigation using VFF (Virtual Force Field), consists of:
- Each object in the environment generates a repulsive force towards the robot.
- Destiny generates an attractive force in the robot.

This makes it possible for the robot to go towards the target, distancing itself
of the obstacles, so that their address is the vector sum of
all the forces.

The solution can integrate one or more of the following levels
of difficulty, as well as any other one that occurs to you:
* Go as quickly as possible
* Choose the safest way
* Obstacles in movement
* Robustness in situations of indecision (zero vector sum)

## EXECUTION

### For the TurtleBot based exercise
We will use two terminal to run this practice:

1. Run the simulator Gazebo with the corresponding world:
1a. With real Turtlebot, you'll need two nodes:
    `$ roslaunch /opt/jderobot/share/jderobot/gazebo/launch/turtlebot-rplidar.launch`
    `$ roslaunch kobuki_node minimal.launch --screen`

1b. With simulated Turtlebot, you'll need only one node:
    `$ roslaunch /opt/jderobot/share/jderobot/gazebo/launch/kobuki-simple-ros.launch`

2. In another termianl, run the obstacle_avoidance component:
    `$ python2 obstacle_avoidance.py obstacle_avoidance_conf.yml`

To simplify the closure of the environment, just close the
window(s) of obstacle_avoidance. *`Ctrl + C` will be problematic*.

### For the Formula1 based exercise

1. For the infrastructure of the exercise, launch Gazebo with the f1_simple_circuitobstacles world through the command 

```
roslaunch ./launch/obstacle_avoidance_f1.launch
```


2. Then you have to execute the academic application, which will incorporate your code:
```
python ./obstacle_avoidance_f1.py obstacle_avoidance_conf_f1.yml
```


## How to do the practice
To carry out the practice, you must edit the file `MyAlgorithm.py` and
insert the control logic.

[MyAlgorithm.py](MyAlgorithm.py#L121)
```
    def execute(self):
        self.currentTarget=self.getNextTarget()
        self.targetx = self.currentTarget.getPose().x
        self.targety = self.currentTarget.getPose().y

        # TODO
        # insert your code here
```

### API
* `pose3d.getPose3d().x` - to get the position of the robot (x coordinate)
* `pose3d.getPose3d().y` - to obtain the position of the robot (y coordinate)
* `pose3d.getPose3d().yaw` - to get the orientation of the robot with
  regarding the map
* `laser.getLaserData()` - to obtain laser sensor data
  It is composed of 180 pairs of values: (0-180º distance in millimeters)
* `motors.sendV()` - to set and send the linear speed
* `motors.sendW()` - to set and send the angular velocity

### Own API
To simplify, the implementation of control points is offered.
To use it, only two actions must be carried out:
1. Obtain the following point:
   `self.currentTarget = self.getNextTarget()`
2. Mark it as visited when necessary:
   `self.currentTarget.setReached(True)`


## Conversion of types

### Laser

```
    laser_data = self.laser.getLaserData ()

    def parse_laser_data (laser_data):
        laser = []
        for i in range (laser_data.numLaser):
            dist = laser_data.distanceData [i] /1000.0
            angle = math.radians (i)
            laser + = [(dist, angle)]
         return laser
```

```
        laser_vectorized = []
        for d, a in laser:
            # (4.2.1) laser into GUI reference system
            x = d * math.cos (a) * -1
            y = d * math.sin (a) * -1
            v = (x, y)
            laser_vectorized + = [v]

        laser_mean = np.mean (laser_vectorized, axis = 0)
```

### Coordinate system
```
    def absolute2relative (x_abs, y_abs, robotx, roboty, robott):
    
        # robotx, roboty are the absolute coordinates of the robot
	# robott is its absolute orientation
        # Convert to relatives
        dx = x_abs - robotx
        dy = y_abs - roboty

        # Rotate with current angle
        x_rel = dx * math.cos (-robott) - dy * math.sin (-robott)
        y_rel = dx * math.sin (-robott) + dy * math.cos (-robott)

return x_rel, and y_rel
```


## Debugging
The graphical interface (GUI) allows to visualize each of the vectors of
calculated forces. For this purpose, the following variables should be given 
value:
```
        # Car direction
        self.carx = 0.0
        self.cary = 0.0

        # Obstacles direction
        self.obsx = 0.0
        self.obsy = 0.0

        # Average direction
        self.avgx = 0.0
        self.avgy = 0.0
```

As well as the destination that we have assigned:
```
        # Current target
        self.targetx = 0.0
        self.targety = 0.0
```

## Demonstration video
[Video1](https://www.youtube.com/watch?v=5SVkvfKPi_s)

[Video2](https://www.youtube.com/watch?v=MtxzNFUYPKo)

## Theory and Hints
[Theory](THEORY.md) and [Hints](HINTS.md)

## Attributions

* *Copyright (C) 2016 [CC-BY-4.0] (https://creativecommons.org/licenses/by/4.0/) Victor Arribas (@varhub)*
* *Basic code made by Alberto Martín (@almartinflorido)*
* *Code of practice made by Eduardo Perdices*
* *Gazebo models and worlds made by Francisco Pérez (@fqez)*
* *Adapted to Turtlebot by Julio Vega (julio.vega@urjc.es)*

