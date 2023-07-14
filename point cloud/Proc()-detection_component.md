---
tags: function
---
实现消息的输入和输出
```cpp
bool DetectionComponent::Proc(const std::shared_ptr<drivers::PointCloud>& message) {
//输出log，包括消息时间戳和当前时间戳
  AINFO << "\n---------------------------------------------------------------------";
  AINFO << "Enter detection component, message timestamp: " << message->measurement_time()
        << " current timestamp: " << zhito::common::time::Clock::NowInSeconds();

//创建智能指针：输出消息（检测结果）和发布消息
  std::shared_ptr<LidarFrameMessage> out_message(new (std::nothrow) LidarFrameMessage);
  std::shared_ptr<PerceptionObstacles> pub_message(new (std::nothrow) PerceptionObstacles);

//调用Internalproc()函数
  bool status = InternalProc(message, out_message);

//发送消息和返回值
//如果Internalproc()执行成功，则发送out_message消息
  if (status) {
    writer_->Write(out_message);
    //输出log并打印障碍物数量
    AINFO << "Send lidar detect output message. " << out_message->lidar_frame_->segmented_objects.size();
    for (int i = 0; i < out_message->lidar_frame_->segmented_objects.size(); i++)
    //输出每个障碍物的中心信息
      AINFO << out_message->lidar_frame_->segmented_objects[i]->center;
  }

//如果tranformMessage()执行成功，则发送pub_message消息 
#ifdef PUB_LIDAR_DETECTION
  bool trans_succ = TransformMessage(out_message, pub_message);
  if (trans_succ) {
    pub_writer_->Write(pub_message);
  }
#endif

//返回Internalproc()的执行结果
  return status;
}
```