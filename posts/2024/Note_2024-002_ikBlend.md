---
parent: 2024
layout: page
title: ZZ-Journal24-002-ikBlend
date: 2024-05-28 00:00:00 +0800
---

# Journal: FK-IK Blending Revision...
... and preparation for autoIK.py

`TODO` for scripting towards autoIK.py:
- `multMatrix` variant function for quickly removing forward-rotations from a section of a joint chain
	- notes on multMatrix: [here](../mayaNodeStuff/mNode_multMatrix.md)
	- not sure if i should make this a utility function or just code and comment directly each time i need to do this. had a need for this multiple times in the dino rig project
- `rename` (and add renaming to existing functions)
- `ikHandle` utility function, must catch returns to hold IK handle for reparenting and nodework
- `pole vector constraint` utility function
	- notes on IK handles: [here](../mayaNodeStuff/mNode_ikHandle_ikEffector.md)
- blendWeighted matrix mixer. more info [here](../mayaNodeStuff/mNode_blendMatrix.md)
- "matrix caching" `holdMatrix` utility function: for long one-time matrix multiply results that don't need re-evaluation past creation
	- explained below
- discrete utility functions:
	- `pickMatrix` (for picking out or only using rotations etc)
	- `composeMatrix` (quickly cast transform components to matrices)
	- `decomposeMatrix` (quickly grab transform components from matrices)
	- `inverseMatrix` (invert transforms)
- selection validation functions:
	- selecting the sub-hierachy between two joints(?)

note: "**forward rotations**" refer to the `joint.rotate` attribute (the animation-facing attribute), rather than the `joint.jointOrient` rotation or the combined transform in the `joint.matrix` result

# rig demo

(GIFs TBA, recording)


# node diagrams

### overview map
![alt text](img/2024/Note/002/ikBlend_logicMap.png)


### FK-IK switching map
![alt text](img/2024/Note/002/ikBlend_switchLogic.png)


### IK Target root adjustments
![alt text](img/2024/Note/002/ikBlend_ikTarget.png)

on the use of "matrix caching" `holdMatrix` node here:

the aim of the result of this matrix value is to get the position of the wrist relative to the root of the joint chain, unmoved.

the regular way would to climb the entire chain from the root to the wrist (+1 connection per joint to the `multMatrix`), and remove all the forward rotations along the way (+1 `composeMatrix` per joint).

the shortcut, is to touch `wrist.worldMatrix` to a holdMatrix, with no forward rotations, and use that for the `ikTargetController.offsetParentMatrix` connection. 

> the only downside is this easy solution is not dynamic, and requires a manual update if that part of the rig is to be edited after creation

doing the regular way may make more sense when created through the script, in a run-and-forget manner. might be post-creation-editing unfriendly though from the hairy ladder standpoint

### Pole Vector root adjustments
![alt text](img/2024/Note/002/ikBlend_PoleVector.png)