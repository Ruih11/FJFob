---
tags: function
---
/modules/perception/lidar/lib/map_manager/map_manager.cc
地图更新
```cpp
bool MapManager::Update(const MapManagerOptions& options, LidarFrame* frame) {
//检查输入frame是否为空，若空，返回false
  if (!frame) {
    AINFO << "Frame is nullptr.";
    return false;
  }
//如果frame中不存在hdmap_struct，则创建一个hdmap_struct
//struct alignas(16) HdmapStruct {std::vector<RoadBoundary> road_boundary;std::vector<PointCloud<PointD>> road_polygons;std::vector<PointCloud<PointD>> hole_polygons;std::vector<PointCloud<PointD>> junction_polygons;std::vector<RoadBoundary> lanes;};
  if (!(frame->hdmap_struct)) {
    frame->hdmap_struct.reset(new base::HdmapStruct);
  }
//检查高精地图输入是否为空，若空，返回false
  if (!hdmap_input_) {
    AINFO << "Hdmap input is nullptr";
    return false;
  }
//空  
  if (update_pose_) {
    if (!QueryPose(&(frame->lidar2world_pose))) {
      AINFO << "Failed to query updated pose.";
    }
  }

//创建base::PointD对象，将frame中位姿信息的平移部分赋值给point
  base::PointD point;
  point.x = frame->lidar2world_pose.translation()(0);
  point.y = frame->lidar2world_pose.translation()(1);
  point.z = frame->lidar2world_pose.translation()(2);
//调用GetRoiHDMapStruct函数，从高精度地图hdmap中根据位置点获取ROI：获取道路轮廓、车道以及路口信息,并进行过滤与合并,最终更新到hdmap_struct_ptr中
  if (!hdmap_input_->GetRoiHDMapStruct(point, roi_search_distance_, frame->hdmap_struct)) {
    frame->hdmap_struct->road_polygons.clear();
    frame->hdmap_struct->road_boundary.clear();//道路轮廓
    frame->hdmap_struct->hole_polygons.clear();
    frame->hdmap_struct->junction_polygons.clear();//路口
    AINFO << "Failed to get roi from hdmap.";
  }
  return true;
}
```