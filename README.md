# ROS2-Occupancy-Grid-Maps
Software that produces occupancy grid maps based on robot sensor data. The purpose is to have additional experience designing and implementing algorithms that overcome sensor data that is noisy and incomplete.

## How the Program Works

### Overview
Our project utilizes a **ROS2 launch file** to orchestrate multiple nodes that simulate a differential drive robot. The launch file takes the argument `robot_file`, specifying the robot's configuration, and incorporates two additional arguments, `bag_in` and `bag_out`, for managing ROS bag files. The system runs the built-in `robot_state_publisher` node alongside our custom `simulator_node` and `velocity_translator_node`.

#### Sample Command
```bash
ros2 launch project4a start.launch.py \
  robot_file:=project4a/robot_files/bad.robot \
  bag_in:=project4a/input_bags/input4 \
  bag_out:=project4a/output_bags/output5
```

---

### Key Components of `start.launch.py`

1. **Launch Arguments**
   - **`robot_file`:** Specifies the robot configuration file. The value is accessed using `sys.argv` for flexibility without hardcoding.
   - **`bag_in` and `bag_out`:** Used for input and output ROS bag files.

2. **Loading Robot Parameters**
   - The function `load_disc_robot` reads the robot's details (e.g., URDF, radius, wheel distance, error parameters) from the specified file, making them accessible across nodes.

3. **Robot State Publisher Node**
   - Publishes the robot's state using the URDF description for integration with the ROS2 ecosystem.

4. **Simulator Node**
   - Simulates the robot's motion, handling parameters like radius, height, and wheel distance, along with error characteristics.

5. **Velocity Translator Node**
   - Translates velocity commands into wheel velocities (`/vl` and `/vr`) to interface with the simulator node.

6. **ROS Bag Playback and Recording**
   - **`ros2 bag play`:** Plays input bag files specified by `bag_in`.
   - **`ros2 bag record`:** Records all topics to the file specified by `bag_out`.

---

### `velocity_translator_node.py`

This node handles velocity translation for the robot's wheels.

- **Subscriptions:**
  - Listens to `/cmd_vel` (Twist messages) for angular and linear velocities.
- **Publications:**
  - Publishes wheel velocities to `/vl` and `/vr` as `Float64` messages.
- **Key Functions:**
  - **Parameters:** Reads `distance` and `radius` from the robot file.
  - **Callback:** Computes wheel velocities (`vl`, `vr`) using differential drive equations and publishes them.

---

### `simulator_node.py`

This node simulates the robot's motion using wheel velocities.

- **Subscriptions:**
  - Reads `/vl` and `/vr` for left and right wheel velocities.
- **Timers:**
  - A 0.1-second periodic callback manages velocity updates and pose calculations.
- **Key Functions:**
  1. **Error Handling:** Multiplies velocities by error multipliers calculated using a Gaussian distribution.
  2. **Pose Updates:** Computes new pose based on velocities using differential drive equations.
  3. **Transform Broadcasting:** Updates the robot's position via tf2 transformations.

---

### Results and Reflection

#### Did the Results Meet Expectations?
Yes, the results met our expectations. While it was initially challenging to implement tf broadcasting and calculate pose updates, our system successfully simulated the robot's motion and accurately responded to input commands. The calculated wheel velocities and transformations aligned with theoretical expectations.

---

### Screenshots of Test Cases

#### Test Case 1
<img width="378" alt="image" src="https://github.com/user-attachments/assets/b34fae60-6964-42d4-9368-8d7b2c09a60a" />

#### Test Case 2
<img width="378" alt="image" src="https://github.com/user-attachments/assets/28f6db3d-41a1-473d-a16c-434009bde394" />

#### Test Case 3
<img width="378" alt="image" src="https://github.com/user-attachments/assets/e07d452d-a49d-4897-9427-fc18c48cefd4" />


#### Test Case 4
<img width="378" alt="image" src="https://github.com/user-attachments/assets/73a05c68-fe92-4aa3-a6b6-29cd3b8f6135" />


#### Test Case 5
<img width="378" alt="image" src="https://github.com/user-attachments/assets/8bced15b-0bca-4ecb-8bd1-3eab9f1cec18" />


---

### Team Members
- Jonas Land
- Ayushri Jain
```
