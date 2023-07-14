#LiDar #笔记

# 待办
- [ ] 下午用图例展示pointpillars的过程[[2023-05-23]]
- [x] 上午补充这篇文章的背景，研究问题，实现场景[[2023-05-22]]
- [ ] 把目标识别和可行驶区域识别联系起来[[2023-05-23]]

# Background
Deploying autonomous vehicles (AVs) in urban environments.
AVs need to detect and track moving objects such as vehicles, pedestrians, and cyclists in realtime.
城镇环境里车辆的障碍物检测（车辆、行人、自行车）
lidar laser scanner to measure the distance to the environment, thus generating a sparse point cloud representation.
雷达通过测量车辆与环境的距离生成点云

https://www.youtube.com/watch?v=_oFTKDwsbQ0


# Problem
针对点云的目标识别
改善[VoxelNet](https://arxiv.org/pdf/1711.06396.pdf)由于3D卷积导致计算量太大，因而推理速度太慢的问题。


# Method
## PointPillars
a method for object detection in 3D that enables end-to-end learning with only 2D convolutional layers.
PointPillars是一种基于3D场景的目标检测方法，实现了使用2D卷积层的[[端到端学习]]

pillars are highly efficient because all key operations can be formulated as 2D convolutions which are extremely efficient to compute on a GPU.

## Feature encoder 特征编码器网络
在俯视图的平面划分 $H \times W$ 的网格，每个网格形成一个柱子，对每个柱子中的点取特征值 $(x,y,z,r,x_c,y_c,z_c,x_p,y_p)$ ， 其中$x_c,y_c,z_c$ 是柱子质心的位置，$x_p,y_p$ 是点相对于质心的偏移。$r$ 是一个点的反射强度，也称为从激光传感器返回的信号强度。反射强度可以被视为一种度量物体表面反射性质的指标，可以提供关于点的表面材质、颜色、形状等方面的信息。反射强度是点云数据中一个重要的特征之一，可以被广泛应用于激光雷达点云的分割、分类、目标检测等任务中。

参数
此时维度$D=9$。整个空间中用最小精度量化出P个pillars，每个pillars最多采用N个点，不足N用0进行填充。
(D,P,N)
a linear layer is applied followed by [[BatchNorm]] and [[ReLU]] to generate a (C, P, N) sized tensor.


>为了应用2D卷积架构,我们首先将点云转换为伪图像。我们用l表示点云中的一个点,坐标为x,y,z和反射率r。第一步,点云在x-y平面上被离散化为等距网格,生成一组立柱P,其中jPj=B。请注意,在z维度上不需要超参数来控制分箱。然后每个立柱的点被增强为xc,yc,zc,xp和yp,其中c下标表示到立柱中所有点算术平均值的距离,p下标表示到立柱x和y中心的偏移。增强后的激光雷达点l现在是9维的。由于点云的稀疏性,立柱集合中大多数为空,一般非空立柱中也只有几个点。例如,在0.162 m2分箱中,Velodyne HDL-64E激光雷达的点云在KITTI中通常使用的范围内有6k-9k个非空立柱,空隙率为97%。这种稀疏性通过限制每个样本的非空立柱数(P)和每个立柱中的点数(N)来利用,以创建一个大小为(D;P;N)的密集张量。如果一个样本或立柱的数据太多无法装入此张量,则进行随机采样。相反,如果一个样本或立柱的数据太少无法填充张量,则进行零填充。 接下来,我们使用PointNet的简化版本,对每个点应用线性层,然后是Batch Norm [10]和ReLU [19],生成大小为(C;P;N)的张量。然后在通道上进行最大值操作,生成大小为(C;P)的输出张量。请注意,线性层可以表述为在张量上进行1x1卷积,这导致了非常高效的计算。编码后,特征被散布回原始立柱位置,创建大小为(C;H;W)的伪图像,其中H和W表示画布的高度和宽度。

>PointNet编码。对Step 3得到的每个体素点,应用一个线性层(等价于1×1卷积)、BatchNormalization和ReLU,得到(C, P, N)的特征张量。然后在通道维度对该张量进行最大值池化,得到(C, P)的特征表示。Step 5: 特征散点图。将Step 4编码得到的(C, P)特征表示散布回体素在原始点云几何结构中的对应位置,得到一个(C, H, W)大小的散点图,H和W对应原始点云的高度与宽度。

## Backbone主干网络（2D-CNN）
The backbone has two sub-networks: one [[top-down network]] that [[produces features]] at increasingly small [[spatial resolution]] and a second network that performs [[upsampling]] and [[concatenation]] of the top-down features.

主干网络包括两个子网络，其中一个子网络自顶向下地针对逐渐降低的[[spatial resolution|空间分辨率]]做特征提取，另一个子网络通过[[upsampling|上采样]]和[[concatenation|串联]]的方式生成更准确的输出。

自顶向下网络可以被描述为一系列的Block，每个块的参数包括（S步长,L卷积核的大小,F特征通道数）



## [[Detection head]] 
[[Single Shot Detector]] (SSD)
利用2D交并比（IoU）匹配先验框与实际框，不使用[[高度]]和[[高程]]匹配。将高度和高程作为额外的回归目标。



# Evaluation
## Dataset
KITTI

## Settings








# Discussion


 




# Reference
https://arxiv.org/pdf/1812.05784.pdf


1、采用Pillar编码方式编码PointCloud：在点云的俯视图的平面进行投影使之变成伪2D图，对这种投影进行编码用的是Pillar方法，即在投影幕上划分为 H * W 的网格，然后对于每个网格所对应的柱子中的每一个点取原特征（x,y,z,r,x_c,y_c,z_c,x_p,y_p）共9个，再然后每个柱子中点多于N的进行采样，少于N的进行填充0，形成了（9，N，H * W）的特征图。
2、使用2D Convolution进行处理：这一部分算得上是网络中的backbone，backbone包含两个子网络一个是top-down网络，另一个是second网络。其中top-down网络结构为了捕获不同尺度下的特征信息，主要是由卷积层、归一化、非线性层构成的，second网络用于将不同尺度特征信息融合，主要由反卷积来实现。
3、使用SSD的检测头对目标进行检测。
————————————————
版权声明：本文为CSDN博主「等到天蓝再看海1」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_44103355/article/details/121124895



对于点云空间的xy平面进行网格划分，这里不对z轴进行切分不产生voxel，而这里的每个网格区域就是一个pillars。对于每个pillars中点具有位置特征xyz以及反射特征r，现在使用pillars的质心位置xc,yc,zc以及每个点离pillars的中心点偏移xp,yp进行特征扩充，所有原本每个点的特征dim=4，现在扩充后的特征维度dim=9。对于整个空间中用最小精度可以量化出P个pillars，每个pillars最多采用N个点，不足N用0进行填充，那么可以组成一个(D,P,N)的数据特征。为了对点特征进行编码，后续会使用MLP-BN-[[ReLU]]对D编码成C，生成(C,P,N)大小的张量，随后提取每个Pillars的局部特征，采用max pooling处理获得(C,P)大小的张量，获取到pillars-wise的特征表示。完成编码后，这些特征就会被散射回到原始的柱pillars位置来创建生成(C,H,W)的伪图像。此时的P=HxW，H和W分别表示高宽。
————————————————
版权声明：本文为CSDN博主「Clichong」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/weixin_44751294/article/details/128044974


![[Pasted image 20230506090546.png]]

![[Pasted image 20230506144554.png]]

"performs [[upsampling]] and [[concatenation]] of the top-down features" 是指在一些神经网络中的一个操作。这个操作通常出现在一些上下文编码-解码网络中，用于将高层语义信息与低层详细信息结合起来，从而生成更加准确的输出。

具体而言，"[[upsampling]]"（[[upsampling|上采样]]）是指将低分辨率的特征图恢复到高分辨率的操作，通常采用反卷积或者插值等方法实现。而 "[[concatenation]]"（[[concatenation|串联]]）则是指将多个特征图按照某种方式连接在一起的操作，通常采用拼接、堆叠等方式实现。

在上下文编码-解码网络中，"performs [[upsampling]] and [[concatenation]] of the top-down features" 操作通常发生在解码器部分，用于将编码器中提取的高层语义特征传递到解码器中，同时将解码器中的低层特征与高层特征结合起来，从而生成更加准确的输出。这个操作通常被称为 "skip connection"（跳跃连接），可以有效地提高神经网络的性能和准确度。


