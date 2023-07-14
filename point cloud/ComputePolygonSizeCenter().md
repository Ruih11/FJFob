---
tags: function
---
```cpp
void ObjectBuilder::ComputePolygonSizeCenter(ObjectPtr object) {
  if (object->lidar_supplement.cloud.size() < 4u) {
    return;
  }
  Eigen::Vector3f dir = object->direction;
  common::CalculateBBoxSizeCenter2DXY(object->lidar_supplement.cloud, dir, &(object->size), &(object->center));
  if (object->lidar_supplement.is_background) {
    float length = object->size(0);
    float width = object->size(1);
    Eigen::Matrix<float, 3, 1> ortho_dir(-object->direction(1), object->direction(0), 0.0);
    if (length < width) {
      object->direction = ortho_dir;
      object->size(0) = width;
      object->size(1) = length;
    }
  }
  for (size_t i = 0; i < 3; ++i) {
    if (object->size(i) < kEpsilonForSize) {
      object->size(i) = kEpsilonForSize;
    }
  }
  object->theta = static_cast<float>(atan2(object->direction(1), object->direction(0)));
}
```