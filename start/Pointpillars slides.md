# 背景
该研究提出了一项基于点云的目标识别特征编码方法。目的是帮助解决为实现城市环境内车辆自动驾驶而遇到的一系列复杂问题，比如实时识别车辆、行人、骑自行车的人等。

# 研究问题
研究致力于仅使用2D卷积层实现对于3D目标物的端到端识别。相对于端到端的目标识别方法，过往研究面对3D目标识别问题，采用将俯视面划分为$10_{cm} \times 10_{cm}$ 网格单元，并手动设计网格单元内的特征编码的方法。该类方法的问题在于泛化使用的成本较高。而过往端到端3D目标识别方法，例如Voxelnet，通过将点云在水平和竖直方向划分为离散的体素，用Pointnet架构提取每个体素内的点云特征，通过3D卷积进行特征融合与抽象,最后通过2D卷积实现目标检测，尽管VoxelNet的性能很强,但其推理速度只有4.4 Hz,无法实时运行与部署，SECOND提高了VoxelNet的运行速度,但3D卷积运算仍然限制了其推理效率。


## 方法
![[Pasted image 20230525082857.png]]
### 点云的俯视视角处理
俯视视角相对于正视视角的优势在于可以较好的保留目标比例以及减少遮挡。
俯视视角的问题是易由于点密度稀疏导致分辨率不足，无法提供足够的信息量以供分析识别。

### 点云到伪图像
点云在俯视平面上被离散化为等距网格,生成一组立柱。用$l$表示坐标中的一个点，坐标表示$x,y,z$和反射强度$r$。
1. 点云在俯视平面上被离散化成为等距网格，生成一组立柱$P$。
2. 将每个立柱中的点的特征增强为$x_c,y_c,z_c,x_p,y_p$，其中下标为c的值代表三个维度上某点到立柱内所有点的算数平均值的距离，下标为p的值代表点到立柱$x$与$y$中心的偏移量
3. 由于点云的稀疏性，大部分立柱为空。为得到更加稠密的特征表达，选取P个非空立柱，每个立柱中点的数量为N，以创建一个大小为$(D,P,N)$的密集张量。
4. 对每一个点应用一个线性层，得到$(C,P,N)$特征张量


