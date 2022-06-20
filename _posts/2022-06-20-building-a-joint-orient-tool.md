---
comments: true
layout: post
tags: maya python rigging
---

# Scripting a joint orient tool in maya using python

Mayas joint orient tool leaves alot to be desired.
Without alot of fiddling with the settings and manual shifting of the joints rotation its extremely annoying and time consuming to get a good base oriention of your joint chain, which will cause alot of problems down the line when building your rig.

Some requirements we want for orientation:

    - consistent up-vector along the joint chain aligned to the planes normal.
    - single axis pointing down the chain.
    - end joints oriented to its parent joint, not to the world.

![Joint Orient Comparison](/assets/images/building_a_joint_orient_tool/01.JPG)

Here you can see the issues with mayas joint orient not achieving any of the required results outlined above.

Compared to my custom tool written in python.


Too be continued ..