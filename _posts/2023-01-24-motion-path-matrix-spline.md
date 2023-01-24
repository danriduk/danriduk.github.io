---
comments: true
layout: post
tags: maya python rigging
---

In the progress building up my rigging tools i wanted a better way to handle spline and spine rigs.
The two main types of spine setups you see are using maya SplineIK solver or a nurbs curve with follicles/uvPin to drive the joints, both work well and are probably more than enough for my needs however i have some issues with both of them that i didnt like

Mayas SplineIK:

    - Twists only from the start or end of the chain.
    - When stretching is implemented, the end join shifts and is not locked.

Nurbs Curve:

    - My main issue with the is i dont like the idea of having something thats deformed inside the rig then deforming the final geometry mesh.


Motion Path Spline:

![Motion Path Spline1](/assets/images/motion_path_matrix_spline/01.png)
![Motion Path Spline2](/assets/images/motion_path_matrix_spline/05.png)

In this setup i use 2 curves, one for the spline that drives the joint positions and the 2nd is used as an up vector.
We can chain together motion path nodes by composing a matrix from the up curves postition to avoid having an intermediate transform (like a locator) to use, this avoids using a deformer (unlike a nurbs setup)

![Motion Paths](/assets/images/motion_path_matrix_spline/02.png)

The curves are driven by simple clusters sitting parented under the cons which allows for twisting at any place on the curve (unlike spline IK), the clusters can be swapped for some decompose matrix nodes to keep everything in the node graph and matrix based.

![Clusters](/assets/images/motion_path_matrix_spline/03.png)

The setup to output for the joint positions is faily simple using mayas new offset parent matrix connections.

![Joint Matrix](/assets/images/motion_path_matrix_spline/04.png)

This node setup is just repeated for all the joints in the spline, and when stretch is added you get the end joint nicely pinned to the end with no shifting that happens with mayas splineIK

![Stretching](/assets/images/motion_path_matrix_spline/06.png)

Using the new matrix nodes and connections creates a very clean outliner with 0 constraints since everything is handled inside the node graph.

![Outliner](/assets/images/motion_path_matrix_spline/07.png)