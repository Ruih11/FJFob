
/modules/perception/lidar/lib/pointcloud_preprocessor/pointcloud_preprocessor.cc
```cpp
void PointCloudPreprocessor::trailerDetection(LidarFrame* frame) const
{
  if (trailer_existed_) {
    Timer timer;
    frame->trailer_existed = true;
    if (trailerTheta(trailer_, frame->cloud)) {
      trailer_.first_trailer = false;
      trailer_.trailer_failure_number = 0;
      // for (int i=0; i<4; i++) {
      //   AINFO << "Frame trailer_polygon[" << i << "] = (" \
      //   <<  frame->trailer.trailer_polygon[i].x << ", " \
      //   << frame->trailer.trailer_polygon[i].y << ")";
      // }
      // trailer_.trailer_theta_ = trailer_theta_1;
      // AINFO << "1. trailer_theta is: " << trailer_.trailer_theta;
      // AINFO << "1. trailer_theta degree is: " << trailer_.trailer_theta / M_PI * 180;
    } else {
      // AINFO << "1. trailer_theta can not be extracted.";
      if (trailer_.trailer_failure_number > trailer_.max_trailer_failure_number) {
        trailer_.first_trailer = true;
        trailer_.trailer_failure_number = 0;
      } else {
        trailer_.trailer_failure_number++;
      }
      // getchar();
    }
    // no filtered
    // for (int i=0; i<4; i++) {
    //   frame->trailer.trailer_polygon[i].x = trailer_.trailer_polygon[i].x;
    //   frame->trailer.trailer_polygon[i].y = trailer_.trailer_polygon[i].y;
    // }
    // frame->trailer.trailer_theta = trailer_.trailer_theta;
    // removeTrailer(trailer_, frame->cloud);
    // no filtered

    // trailer theta filtered
    for (int i=0; i<4; i++) {
      frame->trailer.trailer_polygon[i].x = trailer_.filtered_trailer_polygon[i].x;
      frame->trailer.trailer_polygon[i].y = trailer_.filtered_trailer_polygon[i].y;
    }
    frame->trailer.trailer_theta = trailer_.filtered_trailer_theta;
    removeFilteredTrailer(trailer_, frame->cloud);
    // trailer theta filtered

    double trailer_time = timer.toc(true);
  } else {
    frame->trailer_existed = false;
  }
  // AINFO << "Trailer time is: " << trailer_time;
}
```