---
tags: function
--- 
/modules/perception/lidar/lib/detection/lidar_point_pillars/point_pillars_detection.cc
```cpp
void PointPillarsDetection::PclToArray(const base::PointFCloudPtr& pc_ptr, float* out_points_array, const float normalizing_factor) {
  for (size_t i = 0; i < pc_ptr->size(); ++i) {
    const auto& point = pc_ptr->at(i);
    out_points_array[i * 4 + 0] = point.x;
    out_points_array[i * 4 + 1] = point.y;
    out_points_array[i * 4 + 2] = point.z;
    out_points_array[i * 4 + 3] = static_cast<float>(point.intensity / normalizing_factor);
  }
}
```