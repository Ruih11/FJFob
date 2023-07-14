---
tags:function
---
输入参数为障碍物检测选项和点云帧
```cpp
LidarProcessResult LidarObstacleDetection::ProcessCommon(const LidarObstacleDetectionOptions& options, LidarFrame* frame) {
  //获取传感器名称
  const auto& sensor_name = options.sensor_name;
	
  PERCEPTION_PERF_BLOCK_START();
  //如果use_map_manager_为真，则执行地图更新
  if (use_map_manager_) {
    MapManagerOptions map_manager_options;
    if (!map_manager_.Update(map_manager_options, frame)) {
      return LidarProcessResult(LidarErrorCode::MapManagerError, "Failed to update map structure.");
    }
  }
  PERCEPTION_PERF_BLOCK_END_WITH_INDICATOR(sensor_name, "map_manager");

//创建detection_options对象，调用Detect()方法
//如果检测失败，则返回错误结果
  DetectionOptions detection_options;
  if (!detector_->Detect(detection_options, frame)) {
     return LidarProcessResult(LidarErrorCode::DetectionError, "Failed to detect.");
  }
  // lasersenet_detector_->cnnDetection(frame);

//创建ObjectBuilderOptions对象，调用Build()方法
//struct ObjectBuilderOptions {Eigen::Vector3d ref_center = Eigen::Vector3d(0, 0, 0);};
//如果目标构建失败，则返回错误结果
  ObjectBuilderOptions build_options;
  if (!builder_.Build(build_options, frame)) {
    return LidarProcessResult(LidarErrorCode::ObjectBuilderError, "Failed to build objects.");
  }

  PERCEPTION_PERF_BLOCK_END_WITH_INDICATOR(sensor_name, "detection");
//如果全部成功则返回正确结果
  return LidarProcessResult(LidarErrorCode::Succeed);
}
```