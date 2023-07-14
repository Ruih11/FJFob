---
tags: function
---
/modules/perception/lidar/lib/object_builder/object_builder.cc
```cpp
bool ObjectBuilder::Build(const ObjectBuilderOptions& options, LidarFrame* frame) {
	//
  if (frame == nullptr) {
    return false;
  }
  std::vector<ObjectPtr>* objects = &(frame->segmented_objects);
  
  for (size_t i = 0; i < objects->size(); ++i) {
    if (objects->at(i)) {
      objects->at(i)->id = static_cast<int>(i);
      ComputePolygon2D(objects->at(i));
      ComputePolygonSizeCenter(objects->at(i));
      ComputeOtherObjectInformation(objects->at(i));
    }
  }

  //filter objects with distance
  const int filter_dist_x = 50;
  const int filter_dist_y = 30;
  size_t count = 0;
  for (size_t i = 0; i < objects->size(); ++i) {
    AINFO << objects->at(i)->anchor_point[0];
    if (objects->at(i)->anchor_point[0] < filter_dist_x 
         && objects->at(i)->anchor_point[0] > -filter_dist_x
         && objects->at(i)->anchor_point[1] < filter_dist_y
         && objects->at(i)->anchor_point[1] > -filter_dist_y) {
      if (count != i) {
        objects->at(count) = objects->at(i);
      }
      ++count;
    }
  }
  objects->resize(count);

  return true;
}
```