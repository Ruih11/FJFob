---
tags: function
---
```cpp
bool LidarObstacleDetection::Init(const LidarObstacleDetectionInitOptions& options) {

  // std::cout<<"---------------------------"<<std::endl;
//获取配置管理器config manager的单例
//通过config manager获取模型配置model manager
//从配置文件lidar_obstacle_detection.conf中读参数,获得检测器名detector_name_等信息
  auto& sensor_name = options.sensor_name;
  auto config_manager = lib::ConfigManager::Instance();
  const lib::ModelConfig* model_config = nullptr;
  ACHECK(config_manager->GetModelConfig(Name(), &model_config));

  const std::string work_root = config_manager->work_root();
  std::string config_file;
  std::string root_path;
  ACHECK(model_config->get_value("root_path", &root_path));
  config_file = cyber::common::GetAbsolutePath(work_root, root_path);
  config_file = cyber::common::GetAbsolutePath(config_file, sensor_name);
  config_file = cyber::common::GetAbsolutePath(config_file, "lidar_obstacle_detection.conf");

  LidarObstacleDetectionConfig config;
  ACHECK(cyber::common::GetProtoFromFile(config_file, &config));
  detector_name_ = config.detector();

//判断是否使用地图,结合是否适用hdmap输入与use_map_manager的结果判断
  use_map_manager_ = config.use_map_manager();
  use_object_filter_bank_ = config.use_object_filter_bank();

  use_map_manager_ = use_map_manager_ && options.enable_hdmap_input;

//获取scene manager场景管理器的单例
  SceneManagerInitOptions scene_manager_init_options;
  ACHECK(SceneManager::Instance().Init(scene_manager_init_options));
//通过sensor_name初始化点云预处理
  PointCloudPreprocessorInitOptions preprocessor_init_options;
  preprocessor_init_options.sensor_name = sensor_name;
  ACHECK(cloud_preprocessor_.Init(preprocessor_init_options));
//若use_map_manager为真，则初始化地图管理器map manager；如果为假，则设置use_map_manager为false
  if (use_map_manager_) {
    MapManagerInitOptions map_manager_init_options;
    if (!map_manager_.Init(map_manager_init_options)) {
      AINFO << "Failed to init map manager.";
      use_map_manager_ = false;
    }
  }
```
## in
### const LidarObstacleDetectionInitOptions& options
struct LidarObstacleDetectionInitOptions 
{
  std::string sensor_name = "velodyne64";
  bool enable_hdmap_input = true;
};