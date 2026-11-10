# Vision-Guided Robotic Bin Picking

**FEMTO-ST research internship · 2026 · Two-person project**

Can a robot find an object with a wrist-mounted camera and work out how to pick it up? We built a prototype for recycling-oriented sorting and tested it with an isolated whiteboard marker as a controlled target.

![Niryo Ned2 robot with a wrist-mounted RealSense camera and a calibration checkerboard](robot-setup.jpeg)

## What I worked on

- **Computer vision and AI:** Fine-tuned YOLOv8 to segment markers and used their contours to estimate where the gripper should align.
- **Robotics:** Calibrated the camera relative to the Niryo Ned2 so the robot could turn image detections into grasp targets in its own coordinate frame.
- **Python and data:** Worked on the pick-and-place controller and compared MoGe2 single-image depth estimates with RealSense measurements across more than 4.5 million pixel samples.

## What it demonstrated

The marker model reached about **0.95 mask mAP@50 on the validation set**. We tested the fixed-object workflow using RealSense depth for the grasp. The single-camera depth experiment was evaluated separately against that reference.

![3D preview showing the robot pose, approach point and planned grasp target](grasp-preview.png)

[See the live detection and grasp estimate](fixed-object-detection.png)
