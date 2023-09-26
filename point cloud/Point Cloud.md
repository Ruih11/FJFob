---
tags: AD
---

# 3D representations
![[Pasted image 20230912082257.png]]
point cloud is close to raw data collected from sensors such as lidar and depth sensor, which indicates using point cloud as data format is a easier way to realize **end to end** learning process.
point cloud is a relatively simple and accurate data format. Neither mesh nor volumetric 3D representation includes resolution as an important property.

# point cloud data format
## ASCII
>ASCII uses text to convey information and is considered a universal format that can be easily opened in text editors.
 ASCII file formats : XYZ, OBJ, PTX (Leica), and ASC.
>

## Binary systems
>Binary systems, on the other hand, store data directly in binary code, making the files more compact and faster to process. Some of the most binary file formats : FLS (Faro), [[PCD]] (Point Cloud Library), and LAS.
>


# When we talk about Point cloud processing, what should we think about?

## permutation invariance
![[Pasted image 20230914085118.png]]
point cloud consists of a set of unordered points

## transformation invariance
Rotation and translation of point cloud should not alter classification result.

### Sparsity
![[Pasted image 20230912154535.png]]
When the network try to learn the features far away from the device. The features learned will be super unstable.
points are aggregated on the surface of objects. So there are lots of empty sites inside. 
### granularity 颗粒度
global feature or local feature?
size of voxels/pillars?
