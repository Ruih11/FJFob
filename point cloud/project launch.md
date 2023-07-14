
cd /L4_2022_Perception-master/docker/scripts
./zhito_start.sh
./zhito_into.sh

启动
cyber_launch start /zhito/modules/perception/launcher_perception/launch/obstacle_perception_test_offline.launch

提取数据包
ctrl+shift+t: new terminal
./zhito_into.sh
cyber_recorder play -f /zhito/data/bag/*recordbag.record.00000* -c /tf /zhito/localization/pose /zhito/sensor/fusion/PointCloud2

数据传输
ctrl+shift+t: new terminal
./zhito_into.sh
cyber2zmq

可视化：启动ros & rviz
cd /L4_2022_Perception-master/modules/perception/tool/visualization
source devel/setup.bash
roslaunch pb2ros pb2ros.launch

可视化：dreamview
ctrl+shift+t: new terminal
./zhito_into.sh
bash /zhito/scripts/bootstrap.sh
localhost:8888

