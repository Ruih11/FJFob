---
tags: function
---
将out_message转换为pub_message发布到其他模块
```cpp
bool DetectionComponent::TransformMessage(const std::shared_ptr<const LidarFrameMessage>& out_message,
                                          const std::shared_ptr<PerceptionObstacles>& pub_message) {
//pub_message的类型是PerceptionObstacles，obstacle作为pub_message的指针，可以修改pub_message的内容
//header作为pub_message头部信息的指针，可修改pub_message的头部信息
  PerceptionObstacles* obstacles = pub_message.get();
  zhito::common::Header* header = obstacles->mutable_header();
//从out_message中读取时间戳、激光雷达时间戳、序列号等信息加入到pub_message的头部
  header->set_timestamp_sec(out_message->timestamp_);
  header->set_lidar_timestamp(out_message->lidar_timestamp_);
  header->set_sequence_num(out_message->seq_num_);
  header->set_module_name("perception_segmentation");
  header->set_camera_timestamp(0);
  header->set_radar_timestamp(0);
//从out_message中读取错误码
  obstacles->set_error_code(out_message->error_code_);
//读取障碍物信息
//遍历out_message中的障碍物列表object，调用MsgSerializer::ConvertObjectToPb()把障碍物obj转换为PerceptionObstacles，并设置id
  const std::vector<base::ObjectPtr>& objects = out_message->lidar_frame_->segmented_objects;
  int id_count = 0;
  for (const auto& obj : objects) {
    PerceptionObstacle* obstacle = obstacles->add_perception_obstacle();
//返回转换结果
    if (!MsgSerializer::ConvertObjectToPb(obj, obstacle)) {
      AERROR << "segementation component ConvertObjectToPb failed, Object:" << obj->ToString();
      return false;
    }
    obstacle->set_id(id_count++);
    // std::cout<<"out_message_id:"<<obj->id<<"/n"
    //          <<"pub_message_id:"<<obstacle->id()<<std::endl;
  }
  // std::cout<<"out_message_size: "<<objects.size()<<"\n"
  //          <<"pub_message_size:
  //          "<<obstacles->perception_obstacle().size()<<std::endl;
  return true;
}
```