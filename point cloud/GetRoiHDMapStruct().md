---
tags: function
---
/modules/perception/map/hdmap/hdmap_input.cc
```cpp
bool HDMapInput::GetRoiHDMapStruct(
    const base::PointD& pointd, const double distance,
    std::shared_ptr<base::HdmapStruct> hdmap_struct_ptr) {
  lib::MutexLock lock(&mutex_);
  if (FLAGS_use_zmap_mode) {
    auto delay_time = std::abs(zhito::cyber::Time::Now().ToSecond() -
                               last_receive_zmap_timestamp_);
    if (delay_time >= kZmapMsgDelayTimeThreshold) {
      AERROR << std::fixed << "Do not recieve zmap data for " << delay_time
             << " seconds";
      return false;
    }
  }
  if (hdmap_.get() == nullptr) {
    AERROR << "hdmap is not available";
    return false;
  }
  // Get original road boundary and junction
  std::vector<RoadRoiPtr> road_boundary_vec;
  std::vector<JunctionInfoConstPtr> junctions_vec;
  std::vector<LaneInfoConstPtr> lanes;
  zhito::common::PointENU point;
  point.set_x(pointd.x);
  point.set_y(pointd.y);
  point.set_z(pointd.z);
  if (hdmap_->GetRoadBoundaries(point, distance, &road_boundary_vec,
                                &junctions_vec) != 0) {
    AERROR << "Failed to get road boundary, point: " << point.DebugString();
    return false;
  }
  if (hdmap_->GetLanes(point, distance, &lanes) != 0) {
    AERROR << "Failed to get lanes, point: " << point.DebugString();
    return false;
  }
  if (hdmap_struct_ptr == nullptr) {
    return false;
  }
  hdmap_struct_ptr->hole_polygons.clear();
  hdmap_struct_ptr->junction_polygons.clear();
  hdmap_struct_ptr->road_boundary.clear();
  hdmap_struct_ptr->road_polygons.clear();

  // Merge boundary and junction
  std::vector<RoadBoundary> road_boundaries;
  MergeBoundaryJunction(road_boundary_vec, junctions_vec, &road_boundaries,
                        &(hdmap_struct_ptr->road_polygons),
                        &(hdmap_struct_ptr->junction_polygons));
  // Filter road boundary by junction
  GetRoadBoundaryFilteredByJunctions(road_boundaries,
                                     hdmap_struct_ptr->junction_polygons,
                                     &(hdmap_struct_ptr->road_boundary));
  MergeLanes(lanes, &(hdmap_struct_ptr->lanes));
  return true;
}
```