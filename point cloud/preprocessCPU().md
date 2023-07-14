---
tags: function
---
/modules/perception/lidar/lib/detection/lidar_point_pillars/point_pillars.cc
```cpp
void PointPillars::preprocessCPU(const float* in_points_array, const int in_num_points) {
  //每个pillar的x坐标
  int x_coors[MAX_NUM_PILLARS_];
  x_coors[0] = 0;
  //每个pillar的y坐标
  int y_coors[MAX_NUM_PILLARS_];
  y_coors[0] = 0;
  //每个pillar中点的数量
  float num_points_per_pillar[MAX_NUM_PILLARS_];
  num_points_per_pillar[0] = 0;
  //存储pillar中每个点的x坐标
  float* pillar_x = new float[MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_];
  //存储pillar中每个点的y坐标
  float* pillar_y = new float[MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_];
  //存储pillar中每个点的z坐标
  float* pillar_z = new float[MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_];
  //存储pillar中每个点的强度
  float* pillar_i = new float[MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_];
  //存储每个grid的x坐标
  float* x_coors_for_sub_shaped = new float[MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_];
  //存储每个grid的y坐标
  float* y_coors_for_sub_shaped = new float[MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_];
  //pillar的特征掩码
  float* pillar_feature_mask = new float[MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_];
  //grid map representation for pilar-occupancy
  //存储存在pillar的grid位置
  float* sparse_pillar_map = new float[NUM_INDS_FOR_SCAN_ * NUM_INDS_FOR_SCAN_];
//调用PreprocessPoints::preprocess() 
//输入in_points_array、in_num_points，预处理后返回结果存储在 x_coors, y_coors, num_points_per_pillar, pillar_x, pillar_y, pillar_z,pillar_i, x_coors_for_sub_shaped, y_coors_for_sub_shaped, pillar_feature_mask, sparse_pillar_map数组中
  preprocess_points_ptr_->preprocess(in_points_array, in_num_points, x_coors, y_coors, num_points_per_pillar, pillar_x, pillar_y, pillar_z,
                                     pillar_i, x_coors_for_sub_shaped, y_coors_for_sub_shaped, pillar_feature_mask, sparse_pillar_map,
                                     host_pillar_count_);
//__host__​cudaError_t cudaMemcpy ( void* dst, const void* src, size_t count, cudaMemcpyKind kind )
//Copies data between host and device.
//初始化GPU上的内存空间为零
  GPU_CHECK(cudaMemset(dev_x_coors_, 0, MAX_NUM_PILLARS_ * sizeof(int)));
  GPU_CHECK(cudaMemset(dev_y_coors_, 0, MAX_NUM_PILLARS_ * sizeof(int)));
  GPU_CHECK(cudaMemset(dev_pillar_x_, 0, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float)));
  GPU_CHECK(cudaMemset(dev_pillar_y_, 0, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float)));
  GPU_CHECK(cudaMemset(dev_pillar_z_, 0, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float)));
  GPU_CHECK(cudaMemset(dev_pillar_i_, 0, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float)));
  GPU_CHECK(cudaMemset(dev_x_coors_for_sub_shaped_, 0, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float)));
  GPU_CHECK(cudaMemset(dev_y_coors_for_sub_shaped_, 0, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float)));
  GPU_CHECK(cudaMemset(dev_num_points_per_pillar_, 0, MAX_NUM_PILLARS_ * sizeof(float)));
  GPU_CHECK(cudaMemset(dev_sparse_pillar_map_, 0, NUM_INDS_FOR_SCAN_ * NUM_INDS_FOR_SCAN_ * sizeof(int)));
//将cpu(host)上的预处理结果传输到GPU(device)
  GPU_CHECK(cudaMemcpy(dev_x_coors_, x_coors, MAX_NUM_PILLARS_ * sizeof(int), cudaMemcpyHostToDevice));
  GPU_CHECK(cudaMemcpy(dev_y_coors_, y_coors, MAX_NUM_PILLARS_ * sizeof(int), cudaMemcpyHostToDevice));
  GPU_CHECK(cudaMemcpy(dev_pillar_x_, pillar_x, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float), cudaMemcpyHostToDevice));
  GPU_CHECK(cudaMemcpy(dev_pillar_y_, pillar_y, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float), cudaMemcpyHostToDevice));
  GPU_CHECK(cudaMemcpy(dev_pillar_z_, pillar_z, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float), cudaMemcpyHostToDevice));
  GPU_CHECK(cudaMemcpy(dev_pillar_i_, pillar_i, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float), cudaMemcpyHostToDevice));
  GPU_CHECK(cudaMemcpy(dev_x_coors_for_sub_shaped_, x_coors_for_sub_shaped, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float),
                       cudaMemcpyHostToDevice));
  GPU_CHECK(cudaMemcpy(dev_y_coors_for_sub_shaped_, y_coors_for_sub_shaped, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float),
                       cudaMemcpyHostToDevice));
  GPU_CHECK(cudaMemcpy(dev_num_points_per_pillar_, num_points_per_pillar, MAX_NUM_PILLARS_ * sizeof(float), cudaMemcpyHostToDevice));
  GPU_CHECK(cudaMemcpy(dev_pillar_feature_mask_, pillar_feature_mask, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float),
                       cudaMemcpyHostToDevice));
  GPU_CHECK(cudaMemcpy(dev_sparse_pillar_map_, sparse_pillar_map, NUM_INDS_FOR_SCAN_ * NUM_INDS_FOR_SCAN_ * sizeof(float),
                       cudaMemcpyHostToDevice));
//释放cpu内存空间
  delete[] pillar_x;
  delete[] pillar_y;
  delete[] pillar_z;
  delete[] pillar_i;
  delete[] x_coors_for_sub_shaped;
  delete[] y_coors_for_sub_shaped;
  delete[] pillar_feature_mask;
  delete[] sparse_pillar_map;
}
```