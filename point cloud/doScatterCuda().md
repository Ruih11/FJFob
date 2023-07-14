---
tags: function
---
/modules/perception/lidar/lib/detection/lidar_point_pillars/scatter_cuda.cu
生成伪图像

```cpp
//pillar_count（pillar的数量）、x_coors（pillar的X索引）、y_coors（pillar的Y索引）、pfe_output（pfe的输出）和scattered_feature（散布后的特征数组）
void ScatterCuda::doScatterCuda(const int pillar_count, int *x_coors,
                                int *y_coors, float *pfe_output,
                                float *scattered_feature) {
//调用scatter_kernel函数，pillar_count是线程块(block)的数量，NUM_THREADS_是每个block中的线程数
  scatter_kernel<<<pillar_count, NUM_THREADS_>>>(
      x_coors, y_coors, pfe_output, scattered_feature, MAX_NUM_PILLARS_,
      GRID_X_SIZE_, GRID_Y_SIZE_);
}
```