---
tags: function
---
```cpp
void ObjectBuilder::ComputeOtherObjectInformation(ObjectPtr object) {
  object->anchor_point = object->center;
  double timestamp = 0.0;
  size_t num_point = object->lidar_supplement.cloud.size();
  for (size_t i = 0; i < num_point; ++i) {
    timestamp += object->lidar_supplement.cloud.points_timestamp(i);
  }
  if (num_point > 0) {
    timestamp /= static_cast<double>(num_point);
  }
  object->latest_tracked_time = timestamp;
}
```