most LiDAR sensors can be categorized into the following three schemes: **pulsed time of flight (TOF), amplitude-modulated continuous wave (AMCW) TOF, and frequency-modulated continuous wave (FMCW)**.

There is a growing demand for compact ranging systems, and **solid-state LiDAR** provides an alternative for traditional LiDAR using mechanical rotator

Traditional LiDAR sensors make use of a **mechanical rotation mechanism** to achieve wide **field-of-view (FOV)** scanning, which places limitations on reliability, size, and cost. These limitations can be overcome by using a solid-state approach.


Depending on the mapping/illumination method, solid-state LiDARs are commonly categorized into the following three types: [[flash-based LiDAR]], microelectromechanical system [[MEMS-based LiDAR]],and optical phased array [[OPA-based LiDAR]].


https://onlinelibrary.wiley.com/doi/full/10.1002/lpor.202100511



根据激光映射/照明方法,固态LiDAR常分为三种类型:
1. 基于闪光灯的LiDAR。短时间直接发射出一大片覆盖探测区域的激光，再以高度灵敏的接收器，来完成对环境周围图像的绘制.
2. 基于MEMS的LiDAR。利用微电子机械系统(MEMS)的微镜阵列来扫描和定向激光光束,无需大型机械设备,体积小且成本低。但MEMS芯片的制造难度较大,其镜面扫描性能也难以达到机械LiDAR的水平,限制了探测距离。
3. 基于OPA的LiDAR。采用光相控阵列(OPA)技术,采用多个光源组成阵列，通过控制各光源发光相位时间差，合成具有特定方向的主光束。然后再加以控制，主光束便可以实现对不同方向的扫描。其优点是具有无机械和快速扫描的特点,但OPA芯片的制造也面临较大难度,成本较高,限制其应用。
综上,三种固态LiDAR各有优点与不足:
基于闪光灯的LiDAR具有结构最简单和成本较低的优点,但受限于激光与探测技术,其探测性能还难以达到机械LiDAR的水平。
基于MEMS的LiDAR体积最小且成本较低,但MEMS技术的难度大,其扫描与探测性能也难以比肩机械LiDAR。
基于OPA的LiDAR可以实现无机械和高速扫描,但OPA芯片技术难度大、成本高,限制了其实用化。
