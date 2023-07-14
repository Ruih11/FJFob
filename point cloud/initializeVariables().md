---
tags: function
---
/modules/perception/lidar/lib/detection/lidar_point_pillars/preprocess_points.cc
```cpp
void PreprocessPoints::initializeVariables(int* coor_to_pillaridx, float* sparse_pillar_map, float* pillar_x, float* pillar_y,
                                           float* pillar_z, float* pillar_i, float* x_coors_for_sub_shaped, float* y_coors_for_sub_shaped) {
  //coor_to_pillaridx代表grid到pillar的映射关系
  //初始化coor_to_pillaridx为-1，代表栅格中没有pillar
  for (int i = 0; i < GRID_Y_SIZE_; i++) {
    for (int j = 0; j < GRID_X_SIZE_; j++) {
      coor_to_pillaridx[i * GRID_X_SIZE_ + j] = -1;
    }
  }

  for (int i = 0; i < NUM_INDS_FOR_SCAN_; i++) {
    for (int j = 0; j < NUM_INDS_FOR_SCAN_; j++) {
      sparse_pillar_map[i * NUM_INDS_FOR_SCAN_ + j] = 0;
    }
  }

  for (int i = 0; i < MAX_NUM_PILLARS_ * MAX_NUM_POINTS_PER_PILLAR_; i++) {
    pillar_x[i] = 0;
    pillar_y[i] = 0;
    pillar_z[i] = 0;
    pillar_i[i] = 0;
    x_coors_for_sub_shaped[i] = 0;
    y_coors_for_sub_shaped[i] = 0;
  }
}
```