---
tags: function
---
/modules/perception/lidar/lib/object_builder/object_builder.cc
```cpp
bool ObjectBuilder::Build(const ObjectBuilderOptions& options, LidarFrame* frame) {
	//当Lidar frame为空，返回false
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

cpp
// ObjectBuilder类的Build方法,用于构建检测到的对象

// 参数:
// options - ObjectBuilder的配置选项
// frame - 输入的Lidar帧数据

// 返回值:
// 构建是否成功的bool值

bool ObjectBuilder::Build(const ObjectBuilderOptions& options, LidarFrame* frame) {

  // 1. 检查Lidar帧是否为空,如果为空直接返回false
  if (frame == nullptr) { 
    return false;
  }
  
  // 2. 获取帧中的分割对象列表
  std::vector<ObjectPtr>& objects = frame->segmented_objects;

  // 3. 遍历对象,为每个对象赋id和计算相关几何特征
  for (size_t i = 0; i < objects.size(); ++i) {
    if (objects[i]) {
      objects[i]->id = static_cast<int>(i); // 赋予对象id
      ComputePolygon2D(objects[i]); // 计算2D多边形
      ComputePolygonSizeCenter(objects[i]); // 计算大小和中心点
      ComputeOtherObjectInformation(objects[i]); // 其他信息
    }
  }

  // 4. 根据距离对对象进行过滤
  const int filter_dist_x = 50; 
  const int filter_dist_y = 30;
  
  size_t count = 0;
  for (size_t i = 0; i < objects.size(); ++i) {
    
    // 只保留距离范围内的对象
    if (objects[i]->anchor_point[0] < filter_dist_x  
        && objects[i]->anchor_point[0] > -filter_dist_x
        && objects[i]->anchor_point[1] < filter_dist_y
        && objects[i]->anchor_point[1] > -filter_dist_y) {
        
      // 直接覆盖不符合条件的对象  
      if (count != i) {
        objects[count] = objects[i]; 
      }
      ++count;
    }
  }
  
  // 缩容容器
  objects.resize(count);

  // 5. 返回构建成功
  return true; 
}