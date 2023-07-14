---
tags: function
---
```cpp
bool DetectionComponent::InitAlgorithmPlugin() {
//读取传感器信息sensor info
  ACHECK(common::SensorManager::Instance()->GetSensorInfo(sensor_name_, &sensor_info_));
//std::unique_ptr<lidar::LidarObstacleDetection> detector_; 定义一个lidar::LidarObstacleDetection对象的智能指针detecor_
//unique_ptr：smart pointer：- 表达对对象的唯一拥有权；自动管理对象生命周期(创建和删除)；防止对象被无意中复制
//判断detector_是否正确实例化
  detector_.reset(new lidar::LidarObstacleDetection);
  if (detector_ == nullptr) {
    AERROR << "sensor_name_ " << "Failed to get detection instance";
    return false;
  }
  
//定义初始化选项，包括传感器名称和是否使用高精地图
  lidar::LidarObstacleDetectionInitOptions init_options;
  init_options.sensor_name = sensor_name_;
  init_options.enable_hdmap_input = FLAGS_obs_enable_hdmap_input && enable_hdmap_;

//调用 lidar_obstacle_detection的Init()函数初始化检测器
  if (!detector_->Init(init_options)) {
    AINFO << "sensor_name_ "
          << "Failed to init detection.";
    return false;
  }
//调用lidar2world_trans_.Init初始化lidar到世界坐标系的转换
  lidar2world_trans_.Init(lidar2novatel_tf2_child_frame_id_);
  return true;
}
```