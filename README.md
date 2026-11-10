# Vision-Guided Robotic Bin Picking

**FEMTO-ST research internship · 2026**

**Could a robot pick up an object for recycling using just an ordinary colour image?** A camera can show the robot what’s in front of it, but the robot also needs to know how far away the object is and how to approach it. At FEMTO-ST, I worked on a prototype that connects those steps, with the goal of eventually picking and sorting recyclables without relying on a dedicated depth sensor.

![Niryo Ned2 robot with a wrist-mounted RealSense camera and a calibration checkerboard](robot-setup.jpeg)

*The Niryo Ned2 test setup, with a camera mounted beside the gripper.*

## Could one image give us enough depth?

I compared distances predicted by an AI model from colour images with measurements from the RealSense depth camera. Across **more than 4.5 million pixel-level comparisons**, I looked at where the estimates were reliable and where they drifted. A correction brought the median error down from **6.85 cm to 5.95 cm** in this comparison. That was an improvement, but the larger errors still mattered for a gripper, so we used measured RealSense depth for the physical picking tests.

## From camera view to robot movement

- **Find the object.** I fine-tuned a vision model to outline a marker and estimate which way it was lying, so the gripper could line up with it.
- **Give the robot a target.** I calibrated the wrist camera so a point seen in the image could be translated into a position the robot could move to.
- **Plan the approach.** I worked on the Python controller that brings the gripper above the target, aligns it, and prepares the picking motion. The live view shows what the robot sees; the 3D preview shows the approach and grasp it plans.

![Live camera view showing the detected marker and its estimated position](fixed-object-detection.png)

*Live view: the software locates the test object and displays the information used to plan the grasp.*

![3D preview showing the robot pose, approach point and planned grasp target](grasp-preview.png)

*3D preview: the robot's approach and grasp positions can be checked before moving the arm.*

## What we tested

We used a **whiteboard marker** as a repeatable test object to check the link between detection, alignment, and picking on the physical robot. It was a first step toward the recycling goal. Picking from a pile of mixed objects and using single-image depth to control the grasp were the next steps.
