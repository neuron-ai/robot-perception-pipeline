# robot-perception-pipeline
This project implements a perception-to-action pipeline for humanoid robotics, integrating RGB-D vision, object detection, environmental memory, cognitive reasoning, and action execution. The system is designed to simulate how a robot can see, understand, decide, and act in real-world environments.

## Dataset

This project uses the TUM RGB-D Dataset for simulation.

Download:
https://vision.in.tum.de/data/datasets/rgbd-dataset

After downloading, extract it and place the folder like this:

project/
│
├── rgbd_dataset_freiburg1_xyz/
│   ├── rgb/
│   └── depth/


<img width="535" height="819" alt="image" src="https://github.com/user-attachments/assets/9f12cd95-8c59-4501-99a3-ec0db65d32b9" />


# Vision Module – Perception and Temporal Stability

The Vision module performs real-time RGB-D perception using an Intel RealSense camera or RGB-D dataset. Object detection is performed using YOLO, while depth information is extracted to estimate the object's 3D spatial position.

To improve reliability, the system implements multi-frame confirmation and depth stability checks. An object must remain stable across several frames before being considered valid. This prevents the robot from reacting to flickering detections caused by reflections, occlusions, or sensor noise. For example, if a bottle appears in one frame but disappears in the next, the system will not attempt to pick it. This makes perception more cautious and human-like rather than reactive.

Future improvements includes:

Object tracking (DeepSORT / ByteTrack), Segmentation-based grasp region detection, Point-cloud based 3D localization

# World Model – Memory and Uncertainty Management

The World Model functions as the robot's short-term memory system. It stores detected objects along with their:

3D position
detection confidence
timestamp

Instead of immediately removing objects when they disappear, the system applies confidence decay over time. This models uncertainty similar to human perception. For example, if an object becomes temporarily occluded, the robot still remembers its presence for a short time. However, if it remains unseen for several seconds, confidence gradually decreases and the object may be removed from memory.

Future improvements includes:

Bayesian belief updates, spatial probability maps, object trajectory tracking

# Brain Module – Cognitive Reasoning and Decision Making

The Brain module performs task-level reasoning using a structured state machine. It manages states such as:

SEARCHING, GRASPING

Before issuing a pick command, the system verifies several conditions:
detection confidence threshold, multi-frame stability confirmation, reachability within the robot's physical limits
Target locking ensures that once an object is selected for grasping, the robot maintains focus instead of switching targets due to new detections. Failure recovery logic allows the system to retry a grasp attempt. After exceeding a retry threshold, the robot re-evaluates the environment.

Possible future enhancements include:
task prioritization, shortest path optimization, probabilistic planning, reinforcement learning for exploration

# Action Interface – Execution and Feedback Loop

The Action Interface connects the cognitive layer to robotic hardware. Instead of directly controlling motors, it sends structured commands to the robot controller and receives feedback about action outcomes. This feedback loop enables adaptive behavior. If a grasp attempt fails, the Brain is informed and can retry or re-plan the task. Continuous perception during manipulation ensures that the robot does not act blindly but instead observes and reacts throughout execution.

In real deployment, this module can integrate with:

1)ROS topics
2)robotic control APIs
3)hardware motion controllers
4)Future improvements may include:
5)force sensor feedback
6)collision monitoring
7)trajectory refinement


