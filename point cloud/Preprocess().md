---
tags: function
---
/modules/perception/lidar/lib/pointcloud_preprocessor/pointcloud_preprocessor.cc
Preprocess函数接受三个参数：options（类型为PointCloudPreprocessorOptions），message（指向常量PointCloud对象的共享指针），以及frame（指向LidarFrame对象的指针）。它返回一个布尔值，表示预处理的成功或失败。
```cpp
bool PointCloudPreprocessor::Preprocess(const PointCloudPreprocessorOptions& options,
                                        const std::shared_ptr<zhito::drivers::PointCloud const>& message, LidarFrame* frame) const {

//检查frame、frame->cloud、frame->world_cloud是否为空指针
  if (frame == nullptr) {
    return false;
  }
  if (frame->cloud == nullptr) {
     //获取单例，初始化
    frame->cloud = base::PointFCloudPool::Instance().Get();
  }
  if (frame->world_cloud == nullptr) {
    frame->world_cloud = base::PointDCloudPool::Instance().Get();
  }

  // for sweeper
  //基于chassis_angle_定义变换矩阵chassis_own_transform_M
  Eigen::Matrix4d chassis_own_transform_M = Eigen::Matrix4d::Identity();
  chassis_own_transform_M << cos(chassis_angle_), sin(chassis_angle_), 0, 0,
                       -sin(chassis_angle_), cos(chassis_angle_), 0, 0,
                       0, 0, 1, 0,
                       0, 0, 0, 1;
//创建并初始化仿射变换chassis_own_transform
  Eigen::Affine3d chassis_own_transform = Eigen::Affine3d::Identity();
  chassis_own_transform.matrix() = chassis_own_transform_M;
  // Eigen::Matrix3d rotation_45degrees = Eigen::Matrix3d::Identity();
  // rotation_45degrees << cos(45.0/180.0 * M_PI), sin(45.0/180.0 * M_PI), 0,
  //                       -sin(45.0/180.0 * M_PI), cos(45.0/180.0 * M_PI), 0,
  //                       0, 0, 1;

//设置frame->cloud的时间戳为message的时间戳
  frame->cloud->set_timestamp(message->measurement_time());

//message中存在点
  if (message->point_size() > 0) {
	//将message的point_size()点数量值赋给frame(指向LidarFrame的指针）中点云的预留空间
	//在frame中预留与message中点云数量相同的空间，以便存储点云数据，减少动态内存分配的开销。
    frame->cloud->reserve(message->point_size());
        
    //遍历message中的点（指向LidarFrame对象的指针）
    base::PointF point;
    for (int i = 0; i < message->point_size(); ++i) {
      //创建指向PointXYZIT(T:timestamp)结构体的指针pt，赋值message中的点
      const zhito::drivers::PointXYZIT& pt = message->point(i);
      //过滤NaN或infinity
      if (filter_naninf_points_) {
        if (std::isnan(pt.x()) || std::isnan(pt.y()) || std::isnan(pt.z())) {
          continue;
        }
        //fabs()计算浮点数的绝对值
        if (fabs(pt.x()) >= kPointInfThreshold || fabs(pt.y()) >= kPointInfThreshold || fabs(pt.z()) >= kPointInfThreshold) {
          continue;
        }
      }

      //创建三维向量vec3d_lidar，将pt的三个分量赋值给vec3d_lidar，将vec3d_lidar的值赋给vec3d_novatel
      Eigen::Vector3d vec3d_lidar(pt.x(), pt.y(), pt.z());
      Eigen::Vector3d vec3d_novatel = vec3d_lidar;
      /**
      //过滤box附近点
      if (filter_nearby_box_points_ && vec3d_novatel[0] < box_forward_x_ && vec3d_novatel[0] > box_backward_x_ &&
          vec3d_novatel[1] < box_forward_y_ && vec3d_novatel[1] > box_backward_y_) {
        continue;
      }
	  //过滤指定高度阈值的点
      if (filter_high_z_points_ && (pt.z() > z_threshold_ || pt.z() < z_down_threshold_)) {
        continue;
      }
      //
      if (filter_region_ && (vec3d_novatel[0] > range_forward_x_ || vec3d_novatel[0] < range_backward_x_ ||
                            vec3d_novatel[1] > range_forward_y_ || vec3d_novatel[1] < range_backward_y_)) {
        continue;
      }
      // j7
      if (j7_label_ && vec3d_novatel[0] > trunk_left_edge_xmin_ && vec3d_novatel[0] < trunk_left_edge_xmax_ &&
           vec3d_novatel[1] > trunk_left_edge_ymin_ && vec3d_novatel[1] < trunk_left_edge_ymax_) {
          continue;
      }
      if (j7_label_ && vec3d_novatel[0] > trunk_right_edge_xmin_ && vec3d_novatel[0] < trunk_right_edge_xmax_ &&
           vec3d_novatel[1] > trunk_right_edge_ymin_ && vec3d_novatel[1] < trunk_right_edge_ymax_) {
          continue;
      }
      if (j7_label_ && vec3d_novatel[0] > trunk_bottom_xmin_ && vec3d_novatel[0] < trunk_bottom_xmax_ &&
            vec3d_novatel[1] > trunk_bottom_ymin_ && vec3d_novatel[1] < trunk_bottom_ymax_ &&
            vec3d_novatel[2] > trunk_bottom_zmin_ && vec3d_novatel[2] < trunk_bottom_zmax_) {
          continue;
      }
	//过滤扫地机刷子
      if (filter_sweeper_brush_ && (vec3d_novatel[0] < box_sweeper_brush_forward_x_ && vec3d_novatel[0] > box_sweeper_brush_backward_x_ &&
                            vec3d_novatel[1] < box_sweeper_brush_forward_y_ && vec3d_novatel[1] > box_sweeper_brush_backward_y_ && 
                            vec3d_novatel[2] < box_sweeper_brush_forward_z_ && vec3d_novatel[2] > box_sweeper_brush_backward_z_))
      {
        continue;
      }

      // for sweeper
      //过滤通过车辆底盘的点
      if (filter_through_chassis_ && (vec3d_novatel[0] < rough_box_forward_x_ && vec3d_novatel[0] > rough_box_backward_x_ &&
                            vec3d_novatel[1] < rough_box_forward_y_ && vec3d_novatel[1] > rough_box_backward_y_))
      {
        Eigen::Vector3d trans_point = chassis_own_transform * vec3d_novatel;
        if (filter_nearby_box_points_ && trans_point[0] < box_forward_x_ && trans_point[0] > box_backward_x_ &&
                                         trans_point[1] < box_forward_y_ && trans_point[1] > box_backward_y_) {
          continue;
        }
      }
      // filter point cloud for CNNSeg. <cnn_segmentation.cc 330 line>
      // Eigen::MatrixXd mat3d_lidar(3, 1);
      // mat3d_lidar(0, 0) = pt.x();
      // mat3d_lidar(1, 0) = pt.y();
      // mat3d_lidar(2, 0) = pt.z();
      // Eigen::MatrixXd vec3d_roration(3, 1);
      // vec3d_roration = rotation_45degrees * mat3d_lidar;
      // if (vec3d_roration(0, 0) <= -kPointInfThreshold || vec3d_roration(0, 0) >= kPointInfThreshold || vec3d_roration(1, 0) <= -kPointInfThreshold || vec3d_roration(1, 0) >= kPointInfThreshold)
      // {
      //   continue;
      // }

	//将过滤后的数据点添加到frame->cloud
      point.x = pt.x();
      point.y = pt.y();
      point.z = pt.z();
      //static_cast<>值的类型转换
      point.intensity = static_cast<float>(pt.intensity());
      //push_back()：cloud对象的成员函数，用于在末尾添加一个数据点
      //inline void push_back(const PointT& point, double timestamp,float height = std::numeric_limits<float>::max(),int32_t beam_id = -1, uint8_t label = 0)
      //FLT_MAX是float类型的最大值
      //1e-9: 纳秒到秒
      frame->cloud->push_back(point, static_cast<double>(pt.timestamp()) * 1e-9, FLT_MAX, i, 0);
    }
    */
    
    const double region_mid_time1 = zhito::common::time::Clock::NowInSeconds();//time1
    //执行RegionFilter()方法
    RegionFilter(message, frame);
    const double region_mid_time2 = zhito::common::time::Clock::NowInSeconds();//time1
    
    //PclOutlierFilter(frame);

    trailerDetection(frame);

    #if POINTCLOUD_PREPROCESSOR_VISUALIZATION
      publishTrailerMessage();
    #endif
    //获取当前时间
    const double region_mid_time3 = zhito::common::time::Clock::NowInSeconds();//time1
    //执行TransformCloud()方法
    TransformCloud(frame->cloud, frame->lidar2world_pose, frame->world_cloud);
    //获取当前时间
    const double region_mid_time4 = zhito::common::time::Clock::NowInSeconds();//time1

	//获取时间间隔，输出log
    const double time_cost_1 = (region_mid_time2 - region_mid_time1) * 1e3;
    const double time_cost_2 = (region_mid_time3 - region_mid_time2) * 1e3;
    const double time_cost_3 = (region_mid_time4 - region_mid_time3) * 1e3;
    const double time_cost_4 = (region_mid_time4 - region_mid_time1) * 1e3;
    AINFO << "Preprocess time;     Region filter and Downsample: " << time_cost_1 << "   Transform: " << time_cost_3 << "   Total:" << time_cost_4;
  }
  return true;
}
```