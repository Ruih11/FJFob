---
tags: function
---
/modules/perception/lidar/lib/detection/lidar_point_pillars/scatter_cuda.cu
```cpp
__global__ void scatter_kernel(int *x_coors, int *y_coors, float *pfe_output,
                               float *scattered_feature,
                               const int MAX_NUM_PILLARS_,
                               const int GRID_X_SIZE, const int GRID_Y_SIZE) {
 //将线程块的索引值赋给i_pillar
  int i_pillar = blockIdx.x;
  //将当前线程在线程块中的索引赋值i_feature
  int i_feature = threadIdx.x;
  //获取柱的x坐标
  int x_ind = x_coors[i_pillar];
  //获取柱的y坐标
  int y_ind = y_coors[i_pillar];
  //获取当前特征值
  float feature = pfe_output[i_feature * MAX_NUM_PILLARS_ + i_pillar];
  //计算特征值的索引位置并将数值存储
  scattered_feature[i_feature * GRID_Y_SIZE * GRID_X_SIZE +
                    y_ind * GRID_X_SIZE + x_ind] = feature;
}
```