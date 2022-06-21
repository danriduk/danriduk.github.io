---
comments: true
layout: post
tags: maya python rigging
---

## Scripting a joint orient tool in maya using python

Mayas joint orient tool leaves alot to be desired.
Without alot of fiddling with the settings and manual shifting of the joints rotation its extremely annoying and time consuming to get a good base oriention of your joint chain, which will cause alot of problems down the line when building your rig.

Some requirements we want for orientation:

    - consistent up-vector along the joint chain aligned to the planes normal.
    - single axis pointing down the chain.
    - end joints oriented to its parent joint, not to the world.

![Joint Orient Comparison](/assets/images/building_a_joint_orient_tool/01.JPG)

Here you can see the issues with mayas joint orient not achieving any of the required results outlined above.

Compared to my custom tool written in python.

<hr>

We can utilise the creation and deletion of aim constraints to achieve what we want, using the chains parent or child as the up objects.

However, the aim constraint places rotation values on the transforms of the joint, we can freeze its transforms which then moves the rotation values onto the joint orient attributes of the joint.

To outline the steps we want to take for this goal:

<hr>
*These code snippets are part of a larger class and library of tools i am building.*

- get the joint chain

```python
def _get_chain(self, node):
    # get joint chain
    chain = [jnt for jnt in mc.listRelatives(
                                                node,
                                                type='joint',
                                                c=1,
                                                ad=1
                                                )]
    chain.append(node)

    return chain
```

- align the chain to its plane with aim constraints

```python
def _orient_planar(self, node, upVector=(0, 1, 0)):
    # orient the mid joint of a 3 joint chain to its plane
    chain = self._get_chain(node)

    for jnt in chain:
        par = mc.listRelatives(jnt, p=1, pa=1)
        if not par:
            continue

        children = self._unparent_child(jnt)
        if not children:
            continue

        mc.delete(mc.aimConstraint(
                                    children[0], jnt,
                                    aim=(1, 0, 0), u=upVector,
                                    wut='object', wuo=par[0]
                                    ))

        mc.makeIdentity(jnt, a=1)
        self._reparent_child(jnt, children)
```

- freeze transforms to move the rotations into the orient values
- zero out the end joints orient values

```python
def _zero_end_orients(self, node):
    # orients the end joint of a chain to its parent joint
    chain = self._get_chain(node)[::-1]

    if chain:
        attrUtils.attrHelpers.set_attr(
                                        chain[-1], 'jointOrient',
                                        [0, 0, 0],
                                        value_type='float3'
                                        )
```

- align the root joint of the chain to its child

```python
def _align_to_child(self, node, upVector=(0, 1, 0)):
    # orients the parent of a joint to its child if no parent is found
    chain = self._get_chain(node)

    for jnt in chain:
        children = self._unparent_child(jnt)
        if children:

            mc.delete(mc.aimConstraint(
                                        children[0], jnt,
                                        aim=(1, 0, 0), u=(0, 1, 0), wu=(0, 1, 0),
                                        wut='objectrotation', wuo=children[0]
                                        ))

            mc.makeIdentity(jnt, a=1)
            self._reparent_child(jnt, children)
```

- the class method used to orient a given chain

```python
@classmethod
def orient_chain(cls, node):
    # checks
    if not mc.objExists(node):
        return None
    if not mc.objectType(node) == 'joint':
        return None

    # orient joint chain
    jntHelper = JntHelpers()
    jntHelper._orient_planar(node, upVector=(0, 1, 0))
    jntHelper._zero_end_orients(node)
    jntHelper._align_to_child(node)
```