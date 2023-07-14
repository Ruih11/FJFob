---
tags: function
---
```cpp
bool DetectionComponent::Init() {
//读取配置文件
//modules/perception/launcher_perception/data/lidar/models/lidar_obstacle_pipeline/lidar_front/lidar_obstacle_detection.conf
  LidarDetectionComponentConfig comp_config;
  if (!GetProtoConfig(&comp_config)) {
    return false;
  }
  ADEBUG << "Lidar Component Configs: " << comp_config.DebugString();
//初始化变量
  output_channel_name_ = comp_config.output_channel_name();
  sensor_name_ = comp_config.sensor_name();
  lidar2novatel_tf2_child_frame_id_ = comp_config.lidar2novatel_tf2_child_frame_id();
  lidar_query_tf_offset_ = static_cast<float>(comp_config.lidar_query_tf_offset());
  enable_hdmap_ = comp_config.enable_hdmap();
  writer_ = node_->CreateWriter<LidarFrameMessage>(output_channel_name_);
//创建 writer 节点
#ifdef PUB_LIDAR_DETECTION
  pub_writer_ = node_->CreateWriter<PerceptionObstacles>("/zhito/perception/detection");
#endif

//初始化算法插件 algorithm plugin
  if (!InitAlgorithmPlugin()) {
    AERROR << "Failed to init detection component algorithm plugin.";
    return false;
  }
  return true;
}
```