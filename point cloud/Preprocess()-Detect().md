---
tags: function
---
/modules/perception/lidar/lib/detection/lidar_point_pillars/point_pillars.cc
```cpp
void PointPillars::preprocess(const float* in_points_array, const int in_num_points) {
//根据reproduce_result_mode_判断适用CPU还是GPU做预处理
  if (reproduce_result_mode_) {
    preprocessCPU(in_points_array, in_num_points);
  } else {
    preprocessGPU(in_points_array, in_num_points);
  }
}
```