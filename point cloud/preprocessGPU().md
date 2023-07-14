---
tags: function
---
/modules/perception/lidar/lib/detection/lidar_point_pillars/point_pillars.cc
```cpp
void PointPillars::preprocessGPU(const float* in_points_array, const int in_num_points) {
  float* dev_points;
  //cudaMalloc ( void** devPtr, size_t size )
  //Allocate memory on the device.
  //给dev_points分配内存空间
  GPU_CHECK(cudaMalloc(reinterpret_cast<void**>(&dev_points), in_num_points * NUM_BOX_CORNERS_ * sizeof(float)));
  //把in_points_array拷贝到dev_points
  GPU_CHECK(cudaMemcpy(dev_points, in_points_array, in_num_points * NUM_BOX_CORNERS_ * sizeof(float), cudaMemcpyHostToDevice));
  //初始化dev_pillar_count_histo_、dev_sparse_pillar_map_、dev_pillar_x_、dev_pillar_y_、dev_pillar_z_、dev_pillar_i_、dev_x_coors_、dev_y_coors_、dev_num_points_per_pillar_、dev_anchor_mask_
  GPU_CHECK(cudaMemset(dev_pillar_count_histo_, 0, GRID_Y_SIZE_ * GRID_X_SIZE_ * sizeof(int)));
  GPU_CHECK(cudaMemset(dev_sparse_pillar_map_, 0, NUM_INDS_FOR_SCAN_ * NUM_INDS_FOR_SCAN_ * sizeof(int)));
  GPU_CHECK(cudaMemset(dev_pillar_x_, 0, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float)));
  GPU_CHECK(cudaMemset(dev_pillar_y_, 0, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float)));
  GPU_CHECK(cudaMemset(dev_pillar_z_, 0, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float)));
  GPU_CHECK(cudaMemset(dev_pillar_i_, 0, MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_ * sizeof(float)));
  GPU_CHECK(cudaMemset(dev_x_coors_, 0, MAX_NUM_PILLARS_ * sizeof(int)));
  GPU_CHECK(cudaMemset(dev_y_coors_, 0, MAX_NUM_PILLARS_ * sizeof(int)));
  GPU_CHECK(cudaMemset(dev_num_points_per_pillar_, 0, MAX_NUM_PILLARS_ * sizeof(float)));
  GPU_CHECK(cudaMemset(dev_anchor_mask_, 0, NUM_ANCHOR_ * sizeof(int)));

  preprocess_points_cuda_ptr_->doPreprocessPointsCuda(dev_points, in_num_points, dev_x_coors_, dev_y_coors_, dev_num_points_per_pillar_,
                                                      dev_pillar_x_, dev_pillar_y_, dev_pillar_z_, dev_pillar_i_,
                                                      dev_x_coors_for_sub_shaped_, dev_y_coors_for_sub_shaped_, dev_pillar_feature_mask_,
                                                      dev_sparse_pillar_map_, host_pillar_count_);
  //cudaFree ( void* devPtr )
  //Frees memory on the device.
  GPU_CHECK(cudaFree(dev_points));
  
}
```