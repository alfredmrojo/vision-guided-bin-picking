# Vision-Guided Robotic Bin Picking for Recycling

**FEMTO-ST research internship · 2026**

## The problem

To sort recyclables, a robot needs to do more than recognise an item. It has to work out where the item is, which way it is lying, and how to reach it with a gripper. The question I worked on at FEMTO-ST was: **Could a robot get enough information from a single colour camera to do that, without relying on a dedicated depth sensor?**

We built a prototype on a real robot and tested it with an isolated whiteboard marker. I worked on the vision model, camera calibration, depth analysis, and Python software that connects the camera to the robot's picking motion.

![Niryo Ned2 with a wrist-mounted RealSense camera and a calibration checkerboard](robot-setup.jpeg)

*The test setup: a Niryo Ned2 arm, a camera beside the gripper, and a checkerboard for calibration.*

## Building the prototype

The camera moved with the robot's wrist, so it could look around the workspace and then get closer to an object. A custom 3D-printed bracket held the camera in place, and a Jetson Orin ran the vision software. The RealSense camera provided both colour images and measured depth. Its depth readings gave us a reference for testing what an AI model could infer from the colour image alone.

I helped calibrate the camera by observing a fixed checkerboard from **12 robot poses** and comparing five methods for locating the camera relative to the wrist. This was the link between vision and motion: a detection in an image could be turned into a target the robot could move towards. We checked the result by commanding the arm towards a point measured in the camera view. It reached the expected area, with a residual offset to account for before grasping.

## Testing depth from a single image

I compared two views of the same scene: the distance estimated from one colour image by **MoGe2** and the distance measured by the RealSense. Across **12 acquisition runs**, the project collected **more than 4.5 million matched pixel measurements**, stored in SQLite so the same data could be analysed and reused.

The model captured the overall shape of the scene, but individual estimates could be noisy around edges and reflective surfaces. I investigated how the error changed with distance and tested a correction that adjusted the model's predictions differently at different ranges. The correction was based on the model's own distance estimate, so a future version could apply it without needing the depth sensor at that step.

| Distance error against the RealSense reference | Before correction | After correction |
| --- | ---: | ---: |
| Median absolute error | 6.85 cm | 5.95 cm |
| Mean absolute error | 15.36 cm | 14.84 cm |
| Error below which 90% of samples fell | 28.87 cm | 28.54 cm |

The correction improved the typical estimate, but the larger errors changed very little. That distinction mattered: an average improvement is useful for research, but a gripper needs dependable distances at the object. **We used measured RealSense depth for the physical picking tests and kept single-image depth as a separate experiment.**

## Turning a detection into a pick

1. **Find the marker.** I fine-tuned a YOLOv8 vision model to outline the marker rather than just put a box around it. The outline gave the software a centre point and a direction for the gripper. The model scored about **0.95 mAP@50 for outlines** on the marker validation set. That measures detection performance, not grasp success.
2. **Locate it in the robot's workspace.** The software combined the detection, measured depth, and camera calibration to estimate a position the arm could reach.
3. **Line up the gripper and approach.** I worked on the Python controller that centres the wrist over the target before lowering it. An earlier approach tilted the wrist while centring and could lose sight of the object up close. Separating sideways movement from descent kept the gripper aligned. The test workflow also included a close-range confirmation before the grasp.

![Live camera view showing the detected marker and its estimated position](fixed-object-detection.png)

*Live view: the software finds the object and displays its estimated position and depth.*

![3D preview showing the robot pose, approach point and planned grasp target](grasp-preview.png)

*Planning view: the approach point and grasp target can be inspected before moving the arm.*

## Getting the robot back on the bench

During testing, the robot stopped reporting data from a wrist joint. We isolated the problem to the actuator, replaced it, and configured the replacement so the controller could recognise it. We then recalibrated the joint before resuming the tests. It was a practical part of the project: an improvement to the vision software only mattered if the arm was in a condition to carry out the motion.

## What the project demonstrated

The fixed-marker tests brought the vision model, camera calibration, depth measurements, and robot motion together on physical hardware. A whiteboard marker gave us a repeatable target so we could see which part of the workflow needed improvement. It was not the intended recycling target.

The next step towards a sorter would be to test bottles, cans, and cartons in clutter, record how often the robot successfully picks and places them, and check whether single-image depth can replace measured depth during the grasp. The internship established the prototype and showed why that last step needs its own validation.
