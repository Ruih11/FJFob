---
tags: function
---
```cpp
void ObjectBuilder::ComputePolygon2D(ObjectPtr object) {
  Eigen::Vector3f min_pt;
  Eigen::Vector3f max_pt;
  PointFCloud& cloud = object->lidar_supplement.cloud;
  GetMinMax3D(cloud, &min_pt, &max_pt);
  if (cloud.size() < 4u) {
    SetDefaultValue(min_pt, max_pt, object);
    return;
  }
  LinePerturbation(&cloud);
  common::ConvexHull2D<PointFCloud, PolygonDType> hull;
  hull.GetConvexHull(cloud, &(object->polygon));
}
```