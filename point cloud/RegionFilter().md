---
tags: function
---
/modules/perception/lidar/lib/pointcloud_preprocessor/pointcloud_preprocessor.cc
```cpp
bool PointCloudPreprocessor::RegionFilter(const std::shared_ptr<zhito::drivers::PointCloud const>& message, LidarFrame* frame) const {

//声明pcl格式的点云对象
  pcl::PointCloud<pcl::PointXYZI>::Ptr cloud_original(new pcl::PointCloud<pcl::PointXYZI>);
  pcl::PointCloud<pcl::PointXYZI>::Ptr cloud_after_voxel(new pcl::PointCloud<pcl::PointXYZI>);
  pcl::PointCloud<pcl::PointXYZI>::Ptr cloud_after_condition_range(new pcl::PointCloud<pcl::PointXYZI>);
  pcl::PointCloud<pcl::PointXYZI>::Ptr cloud_after_condition_truck(new pcl::PointCloud<pcl::PointXYZI>);

  const double region_filter_mid_time1 = zhito::common::time::Clock::NowInSeconds();//time1
  
  // cloud_original->points.resize(message->point_size());
  // for (int i = 0; i < message->point_size(); ++i) {
  //   const zhito::drivers::PointXYZIT& pt = message->point(i);
  //   pcl::PointXYZI point;
  //   point.x = pt.x();
  //   point.y = pt.y();
  //   point.z = pt.z();
  //   point.intensity = pt.intensity();
  //   cloud_original->push_back(point);
  // }

//将原始点云的width和height属性赋值给cloud_original
  cloud_original->width = message->width();
  cloud_original->height = message->height();
  cloud_original->is_dense = false;

//在PCL中,一个点云是由width, height和points属性来表示的:width:点云的宽度,即一个扫描线内点的个数。height:点云的高度,即扫描线的个数。points:点云中的点列表,大小为width * height。
//当点云数据中weight*height的值和size不匹配时，重置weight为1，height为点云的size
  if (cloud_original->width * cloud_original->height != static_cast<unsigned int>(message->point_size())) {
    cloud_original->width = 1;
    cloud_original->height = message->point_size();
  }
  cloud_original->points.resize(message->point_size());

//把message中的点转换为pcl格式
  for (size_t i = 0; i < cloud_original->points.size(); ++i) {
    const zhito::drivers::PointXYZIT& pt = message->point(i);
    cloud_original->points[i].x = pt.x();
    cloud_original->points[i].y = pt.y();
    cloud_original->points[i].z = pt.z();
    cloud_original->points[i].intensity = pt.intensity();
  }

  const double region_filter_mid_time2 = zhito::common::time::Clock::NowInSeconds();//time1

//噪声滤波
  pcl::VoxelGrid<pcl::PointXYZI> vg;
  vg.setInputCloud(cloud_original);
  vg.setLeafSize((float)0.1f, (float)0.1f, (float)0.1f);
  vg.filter(*cloud_after_voxel);
  AINFO << "cloud_after_voxelgrid size: " << cloud_original->points.size() << "  " << cloud_after_voxel->points.size();
  const double region_filter_mid_time3 = zhito::common::time::Clock::NowInSeconds();//time1

//条件滤波，设置点云xyz范围
//ConditionAnd 
  pcl::ConditionAnd<pcl::PointXYZI>::Ptr range_cond(new pcl::ConditionAnd<pcl::PointXYZI>());

  range_cond->addComparison(pcl::FieldComparison<pcl::PointXYZI>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZI>("x", pcl::ComparisonOps::GT, range_backward_x_)));
  range_cond->addComparison(pcl::FieldComparison<pcl::PointXYZI>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZI>("x", pcl::ComparisonOps::LT, range_forward_x_)));

  range_cond->addComparison(pcl::FieldComparison<pcl::PointXYZI>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZI>("y", pcl::ComparisonOps::GT, range_backward_y_)));
  range_cond->addComparison(pcl::FieldComparison<pcl::PointXYZI>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZI>("y", pcl::ComparisonOps::LT, range_forward_y_)));

  // z值 大于-5.0
  range_cond->addComparison(pcl::FieldComparison<pcl::PointXYZI>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZI>("z", pcl::ComparisonOps::GT, -3.0)));
  // z值 小于2.0
  range_cond->addComparison(pcl::FieldComparison<pcl::PointXYZI>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZI>("z", pcl::ComparisonOps::LT, z_threshold_)));

//对于以条件分割的区域做去除处理
  pcl::ConditionalRemoval<pcl::PointXYZI> condrem;
  condrem.setCondition(range_cond);
  condrem.setInputCloud(cloud_after_voxel);
  condrem.setKeepOrganized(false);
  condrem.filter(*cloud_after_condition_range);
  
//条件滤波：ConditionOr
  pcl::ConditionOr<pcl::PointXYZI>::Ptr truck_range_cond(new pcl::ConditionOr<pcl::PointXYZI>());

  truck_range_cond->addComparison(pcl::FieldComparison<pcl::PointXYZI>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZI>("x", pcl::ComparisonOps::GT, box_forward_x_)));
  truck_range_cond->addComparison(pcl::FieldComparison<pcl::PointXYZI>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZI>("x", pcl::ComparisonOps::LT, box_backward_x_)));

  truck_range_cond->addComparison(pcl::FieldComparison<pcl::PointXYZI>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZI>("y", pcl::ComparisonOps::GT, box_forward_y_)));
  truck_range_cond->addComparison(pcl::FieldComparison<pcl::PointXYZI>::ConstPtr(new pcl::FieldComparison<pcl::PointXYZI>("y", pcl::ComparisonOps::LT, box_backward_x_)));

//去除自车点云
  pcl::ConditionalRemoval<pcl::PointXYZI> condrem_truck;
  condrem_truck.setCondition(truck_range_cond);
  condrem_truck.setInputCloud(cloud_after_condition_range);
  condrem_truck.setKeepOrganized(false);
  condrem_truck.filter(*cloud_after_condition_truck);

//输出log：过滤后点云中点的数量（size）
  AINFO << "cloud_after_condition size: " << cloud_after_condition_range->points.size() << "  " << cloud_after_condition_truck->points.size();
  const double region_filter_mid_time4 = zhito::common::time::Clock::NowInSeconds();//time1


  frame->cloud->clear();
  frame->cloud->reserve(cloud_after_condition_truck->size());
  for (unsigned int i = 0; i < cloud_after_condition_truck->points.size(); i++)
  {
    base::PointF point;
    pcl::PointXYZI pt = cloud_after_condition_truck->points[i];
    point.x = pt.x;
    point.y = pt.y;
    point.z = pt.z;
    point.intensity = static_cast<float>(pt.intensity);
    frame->cloud->push_back(point, 0.0,
                            FLT_MAX, i, 0);
  }
  const double region_filter_mid_time5 = zhito::common::time::Clock::NowInSeconds();//time1

  // const double time_cost_1 = (region_filter_mid_time2 - region_filter_mid_time1) * 1e3;
  // const double time_cost_2 = (region_filter_mid_time3 - region_filter_mid_time2) * 1e3;
  // const double time_cost_3 = (region_filter_mid_time4 - region_filter_mid_time3) * 1e3;
  // const double time_cost_4 = (region_filter_mid_time5 - region_filter_mid_time4) * 1e3;
  //AINFO << "Region filter process cost time:" << time_cost_1 << "  " << time_cost_2 << "  " << time_cost_3 << "  " << time_cost_4;

  return true;
}
```