## Project: 3D Motion Planning

# Required Steps for a Passing Submission:
1. Load the 2.5D map in the colliders.csv file describing the environment.
2. Discretize the environment into a grid or graph representation.
3. Define the start and goal locations.
4. Perform a search using A* or other search algorithm.
5. Use a collinearity test or ray tracing method (like Bresenham) to remove unnecessary waypoints.
6. Return waypoints in local ECEF coordinates (format for `self.all_waypoints` is [N, E, altitude, heading], where the drone’s start location corresponds to [0, 0, 0, 0].
7. Write it up.
8. Congratulations!  Your Done!
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it! Below I describe how I addressed each rubric point and where in my code each point is handled.

### Explain the Starter Code

#### 1. Explain the functionality of what's provided in `motion_planning.py` and `planning_utils.py`
These scripts contain a basic planning implementation that includes...
the significant difference betwen 
##### `motion_planning.py`
This script is almost identical to the backyard_flyer_solution.py script. The significant difference is the `plan_path` method.
In this method the grid is defined and the waypoints along the shortest obstacle free path the drone will have to follow to get to the goal is calculated. This data is sent to the simulator for visualization
##### `planning_utils.py`
* `create_grid` function:
it creates a 2.5D configuration space where ones are obstacles and zeros are free space. Obstacles below the drone_altitude are marked as free space. Obstacles are also made bigger than they are by padding them with the safety_distance.
* `Action Class`:
this is a class that inherits from the Enum class to set possible movements for the drone. The class also removes actions that result in the drone flying into an obstacle
* `a_star` function:
This is an implementation of the A star algorithm. It takes in a grid, a heuristic function, a start and a goal.

### Implementing Your Path Planning Algorithm

#### 1. Set your global home position
Here students should read the first line of the csv file, extract lat0 and lon0 as floating point values and use the self.set_home_position() method to set global home. Explain briefly how you accomplished this in your code.
I read the first line of the csv file and set the global home position with the code snippet below. I use the `next` method as an iterator to read the first line
```python
file = open('colliders.csv', 'r')
reader = csv.reader(file)
header = reader.__next__()
lat0, lon0 = [float(header[i].split()[1]) for i, j in enumerate(header)]
global_home = (lon0, lat0, 0)
```

#### 2. Set your current local position
I use the `global_to_local` method to accomplish this. It converts longitude, latitude and altitude to the NED coordinate frame.

#### 3. Set grid start position from local position
This is another step in adding flexibility to the start location. As long as it works you're good to go!

#### 4. Set grid goal position from geodetic coords
This is accomplished with this snippet of code
```python
goal_north, goal_east, goal_alt = global_to_local([-122.400150, 37.796005, TARGET_ALTITUDE], self.global_home)
grid_goal = (int(np.ceil((-north_offset + goal_north))), int(np.ceil(-east_offset + goal_east)))
```
The geodetic coordinates  of the goal are possed into the global_to_local method to convert the frame. An offset is subsequently applied to set the goal position in the local frame of the drone.


#### 5. Modify A* to include diagonal motion (or replace A* altogether)
I modified A star by adding 4 more (diagonal) motions
* North West
* North East
* South West
* South East

Each with a cost of sqrt(2).
The code works as it did previously with 4 extra actions now available.
This can be found in the `valid_actions` function in `planning_utils.py`


#### 6. Cull waypoints 
I used a collinearity check to prune the flight path of unnecessary points in `planning_utils.py`



### Execute the flight
#### 1. Does it work?
It works!

### Double check that you've met specifications for each of the [rubric](https://review.udacity.com/#!/rubrics/1534/view) points.
  
# Extra Challenges: Real World Planning

For an extra challenge, consider implementing some of the techniques described in the "Real World Planning" lesson. You could try implementing a vehicle model to take dynamic constraints into account, or implement a replanning method to invoke if you get off course or encounter unexpected obstacles.


