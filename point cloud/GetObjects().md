---
tags: function
---
/modules/perception/lidar/lib/detection/lidar_point_pillars/point_pillars_detection.cc
```cpp
void PointPillarsDetection::GetObjects(std::vector<std::shared_ptr<Object>>* objects, const Eigen::Affine3d& pose,
                                       std::vector<float>* detections) {
  Timer timer;
  int num_objects = detections->size() / kOutputNumBoxFeature;

  objects->clear();
  base::ObjectPool::Instance().BatchGet(num_objects, objects);

  for (int i = 0; i < num_objects; ++i) {
    auto& object = objects->at(i);
    object->id = i;

    // read params of bounding box
    float x = detections->at(i * kOutputNumBoxFeature + 0);
    float y = detections->at(i * kOutputNumBoxFeature + 1);
    float z = detections->at(i * kOutputNumBoxFeature + 2);
    float dx = detections->at(i * kOutputNumBoxFeature + 4);
    float dy = detections->at(i * kOutputNumBoxFeature + 3);
    float dz = detections->at(i * kOutputNumBoxFeature + 5);
    float yaw = detections->at(i * kOutputNumBoxFeature + 6);
    yaw += M_PI / 2;
    yaw = std::atan2(sinf(yaw), cosf(yaw));
    yaw = -yaw;

    // directions
    object->theta = yaw;
    object->direction[0] = cosf(yaw);
    object->direction[1] = sinf(yaw);
    object->direction[2] = 0;
    object->lidar_supplement.is_orientation_ready = true;

    // compute vertexes of bounding box and transform to world coordinate
    float dx2cos = dx * cosf(yaw) / 2;
    float dy2sin = dy * sinf(yaw) / 2;
    float dx2sin = dx * sinf(yaw) / 2;
    float dy2cos = dy * cosf(yaw) / 2;
    object->lidar_supplement.num_points_in_roi = 8;
    object->lidar_supplement.on_use = true;
    object->lidar_supplement.is_background = false;
    for (int j = 0; j < 2; ++j) {
      PointF point0, point1, point2, point3;
      float vz = z + (j == 0 ? 0 : dz);
      point0.x = x + dx2cos + dy2sin;
      point0.y = y + dx2sin - dy2cos;
      point0.z = vz;
      point1.x = x + dx2cos - dy2sin;
      point1.y = y + dx2sin + dy2cos;
      point1.z = vz;
      point2.x = x - dx2cos - dy2sin;
      point2.y = y - dx2sin + dy2cos;
      point2.z = vz;
      point3.x = x - dx2cos + dy2sin;
      point3.y = y - dx2sin - dy2cos;
      point3.z = vz;
      object->lidar_supplement.cloud.push_back(point0);
      object->lidar_supplement.cloud.push_back(point1);
      object->lidar_supplement.cloud.push_back(point2);
      object->lidar_supplement.cloud.push_back(point3);
    }
    for (auto& pt : object->lidar_supplement.cloud) {
      Eigen::Vector3d trans_point(pt.x, pt.y, pt.z);
      trans_point = pose * trans_point;
      PointD world_point;
      world_point.x = trans_point(0);
      world_point.y = trans_point(1);
      world_point.z = trans_point(2);
      object->lidar_supplement.cloud_world.push_back(world_point);
    }

    // classification (only detect vehicles so far)
    // TODO(chenjiahao): Fill object types completely
    object->lidar_supplement.raw_probs.push_back(std::vector<float>(static_cast<int>(base::ObjectType::MAX_OBJECT_TYPE), 0.f));
    object->lidar_supplement.raw_classification_methods.push_back(Name());
    object->lidar_supplement.raw_probs.back()[static_cast<int>(base::ObjectType::VEHICLE)] = 1.0f;
    // copy to type
    object->type_probs.assign(object->lidar_supplement.raw_probs.back().begin(), object->lidar_supplement.raw_probs.back().end());
    object->type = static_cast<base::ObjectType>(
        std::distance(object->type_probs.begin(), std::max_element(object->type_probs.begin(), object->type_probs.end())));
  }

  collect_time_ = timer.toc(true);
}
```