#LiDar #笔记


# 待办
- [ ] 补充文章的研究问题、实现场景[[2023-05-24]]
- [ ] 补充了解全卷积网络等general的神经网络[[2023-05-25]]

# Background
对于一些模棱两可的区域，本文中将其称为灰色区域。这一区域理论上来讲是有驾驶条件的，但现实中由于其不适宜的特点，通常不会选择在这一区域驾驶。
We propose a **LiDAR-based deep learning framework** specific to the **ambiguities** in this task. To reduce the demand for human-annotated datasets, we also propose **weakly supervised and semi-supervised methods to learn features from auto-generated labels**.



# Problem
few studies have focused on offroad environments. In off-road environments, there are no structured features such as traffic lanes, paved road surfaces or guardrails.
很少有研究关注乡间小路环境的识别，小路的场景不具备一些结构化的特征


# Method
autonomously learn features of the drivable zone from the labelled data. Additionally, it is suitable for weakly supervised and semi-supervised learning.
从标注数据汇总自动学习可驾驶区域的特征，这一方法同样适用于几乎未监督学习与半监督学习


The key idea of our proposed method is learning two classification surfaces to separate the grey zone samples. One classification surface is used to separate the drivable zone samples from the other samples. The other classification surface is used to separate the obstacle zone samples.
关键思想是学习两个分类曲面来分离灰色区域的样本。其中一个分类曲面用于将可行驶区域的样本与其他样本分开，另一个分类曲面用于将障碍区域的样本与其他样本分开。通过学习这两个分类曲面，我们能够更好地区分不同的区域，并更准确地识别道路上的障碍物和可行驶区域。这种方法可以有效地提高自动驾驶车辆的识别和行驶能力。

![[Pasted image 20230511170353.png]]

the proposed network has two branches for learning the two classification surfaces mentioned above. Both of these branches are designed according to the common VGG-based fully convolutional network.

所提出的神经网络架构具有两个分支，专门设计用于学习前文提到的两个分类面。这些分支基于 VGG (Visual Geometry Group) 架构，这是一种用于图像分类任务的流行卷积神经网络 (CNN) 架构。

换句话说，所提出的网络具有一个特定的设计，使其能够使用两个独立的分支学习可驾驶区域和障碍区域之间的边界，每个分支都基于 VGG 架构。这个设计选择可能是因为 [[VGG]] 架构在各种计算机视觉任务中表现出了很强的性能，尤其是在图像分类和目标检测方面。

与传统分类网络最后一层输出离散标签不同，该网络的最后一层输出的是最后一个softmax层的概率预测。也就是说，传统的分类网络最后一层是一个全连接层，输出的是某一类别的离散标签，而这个网络的最后一层是一个softmax层，输出的是每个类别的**概率预测**。因此，该网络的输出是一个概率分布，可以表征每个类别的概率大小
每个分支都通过[[交叉熵损失函数]]训练



每个分支都使用以下交叉熵损失函数进行端到端训练:
$$
L_{br}(X, \Theta_{br}) = -\frac{1}{P}\sum_{i} y_{br,i} \log(P(y_{br,i}|\Theta_{br})), br \in \{dri,obs\} \tag{2}
$$

其中$br \in \{dri,obs\}$是网络分支的名称。$P(y_{br,i}|\Theta_{br})$是像素i被预测为标签$y_{br,i}$的概率，其中$\Theta_{br}$是网络参数。我们使用$dri$，$obs$和$gre$来表示可驾驶区域，障碍物区域和灰色区域。

在训练网络时，两个分支使用不同的标签$y_{br,i}$。
$$
y_{br,i}=
\begin{cases}
Ψ(\vec{br}) & gi = gre\\
\vec{gi} & gi \in \{dri, obs\}
\end{cases}\tag{3}
$$

其中$\vec{br}$是标签$br \in \{dri, obs\}$的[[one-hot向量]]。具体而言，在训练可行驶分支时，我们将所有满足$g_i = gre$的像素都替换为标签$obs$，以获得$y_{dri,i}$。在训练障碍物分支，我们将所有满足$g_i = gre$的像素都替换为标签$dri$，以获得$y_{obs,i}$。

我们使用以下规则来计算遍历成本图$C$和离散标签：
$$
C=
\begin{cases}
S1, & S1>\alpha_1 \text{ and } S2<\alpha_2 \\
1-S2, & S2>\alpha_2 \text{ and } S1<\alpha_1 \\
\frac{1-S2}{1-S1+1-S2}, & \text{otherwise}
\end{cases}\tag{4}
$$

其中$\alpha_1$和$\alpha_2$是[[超参数]]，满足第一个条件的像素标记为$y_{j,k} = dri$，第二个条件为$y_{j,k} = obs$，最后一个条件为$y_{j,k} = gre$。


半监督学习方法用于减少高成本的人工标注数据需求。作者提出的方法可以分为弱监督和半监督两种，弱监督方法不需要使用人工标注数据进行训练，半监督方法则可以将自动生成的标签和少量的人工标注标签结合使用进行网络训练。除了标注数据之外，这个框架几乎与完全监督的框架相同。作者提出的网络以基于LiDAR的高度图为输入，并输出每个像素的可穿越成本地图。为了方便评估，成本地图被离散化为三类结果，并在图像上进行可视化投影。



# Evaluation
对照组
[[RG-FCN]]



本文提出了一个针对越野环境中含糊不清的灰色区域的深度学习框架，用于越野可行驶区域的提取。我们还提出了一种自动标注方法，从车辆驾驶数据收集中生成大量的弱标签。我们的方法可以显著减少对人工标注数据的需求，用于弱监督和半监督网络训练。重要的是，实验证明所提出的半监督方法即使使用更少的人工标注标签也可以比完全监督方法获得更好的性能。在本工作中，相机图像仅用于可视化，但它们实际上包含许多用于提取可行驶区域的有用特征，如颜色和纹理。我们计划将相机信息融合到我们的框架中，增强越野环境中远场可行驶区域提取的鲁棒性。

## Fully supervised

## Semi supervised 
基于半监督学习的框架，该框架结合了判别器和生成器两个部分。生成器将LiDAR高度图输入，并生成一个可穿越成本地图。判别器则判断输入数据的标签是否真实。在训练过程中，生成器使用少量的真实标签和大量的伪标签进行监督学习，而判别器则使用少量的真实标签和一部分伪标签进行监督学习。

我们使用数据采集车记录下的数据来生成自动生成的标签。我们遵循基于规则的区域生长算法（见算法1）
这段代码是一个算法的伪代码，它描述了一种基于规则的区域生长方法，用于从LiDAR数据中生成垂直障碍物区域标签。算法输入是高度图X、高度阈值Th、角度阈值Ta和初始路面高度区间[Tr, T0r]。算法输出是可驾驶区域集合Sd和障碍物区域集合So。
算法首先将高度在[Tr, T0r]之间的像素标记为等待列表Q和可驾驶区域集合Sd。然后，对于每个像素xi，算法检查其邻域集合中的像素xj是否满足高度差∆H(xi, xj)小于阈值Th和角度差∆A(xi, xj)小于阈值Ta的条件。如果是，那么像素xj被添加到等待列表Q和可驾驶区域集合Sd中；否则，像素xj被添加到障碍物区域集合So中。在每一次循环中，像素xi从等待列表Q中删除，并重复执行该过程，直到等待列表Q为空。最终，可驾驶区域集合Sd和障碍物区域集合So将作为算法输出。

来从LiDAR数据中生成垂直障碍物作为弱障碍区域标签。我们只需要设置一个较宽松的区域生长阈值，就可以获得一个相对严格的垂直障碍区域。

此外，我们假设由人类驾驶员选择的车辆路径必须属于可行驶区域。因此，我们将数据采集车的GPS轨迹投影到与车宽相同的输入高度图上，并将其标记为弱可行驶区域。

半监督学习阶段。在这个阶段中，作者利用了自动生成的标签和少量的人工标注标签来训练网络。自动生成的标签是通过第一个阶段中训练的弱监督模型生成的，它们可能存在噪声和不准确性，但是它们可以作为半监督学习的训练数据，以减少对人工标注数据的需求。

>在半监督学习阶段，作者采用了一种称为 "confidence-aware consistency regularization" 的正则化方法来利用自动生成的标签。该方法旨在鼓励模型对同一图像中的不同视角生成的标签之间保持一致性。具体地，该方法使用一个置信度值来衡量每个像素的标签的可靠性，然后使用这些置信度值来计算一个正则化项，以鼓励模型对置信度高的像素生成一致的标签。这个正则化项与交叉熵损失相结合来训练网络，以实现高质量的标签预测。

>在半监督学习阶段，作者还引入了一种称为 "mixup" 的数据增强方法，该方法可以进一步增加训练数据的多样性。mixup 方法基于对训练图像进行线性插值来生成新的训练数据。这种插值可以使模型更好地泛化到不同的场景，并有助于减轻过拟合的问题。

在半监督学习阶段的结束，模型已经学会了对输入图像生成高质量的可穿越成本地图，并可以在自动驾驶等应用中进行使用。


## Weakly superviseed
使用无监督学习方法，例如自编码器或聚类方法，来对大量的未标记数据进行预处理
在这个阶段，作者使用了一种基于相似性度量的聚类方法对未标记的数据进行聚类，从而生成大量的伪标签。具体来说，作者首先使用K-means算法将未标记数据聚成k个簇。然后，将聚类中心作为类代表，并对未标记数据进行分类，即将它们分配到最近的聚类中心。这样，每个未标记数据都被赋予了一个伪标签，这个标签代表了其所属的聚类中心。作者注意到由于聚类是基于数据的特征向量进行的，所以聚类结果也会被用于半监督学习的第二个阶段中，即网络训练过程中。通过这种方式，作者实现了不需要大量人工标注数据，而可以使用自动生成的伪标签来训练网络的半监督学习方法。






# Dataset 
off-road dataset using our data collection vehicle, which is equipped with a Velodyne HDL64 LiDAR, a front-view monocular camera and a GPS/IMU system.



在这篇论文中，作者提出了一种处理灰色区域的方法，即将灰色区域样本视为位于特征空间的可行驶样本和障碍样本之间，并通过学习两个分类曲面来分离这些样本。

具体来说，灰色区域在特征空间中与可行驶区域和障碍区域之间存在一些重叠和交叉。为了解决这个问题，作者提出了一种通过学习两个分类曲面来对灰色区域样本进行分类的方法。其中，一个分类曲面用于将可行驶区域的样本与其他样本分开，另一个分类曲面用于将障碍区域的样本与其他样本分开。这样，灰色区域的样本就可以被分为与可行驶区域更接近的样本和与障碍区域更接近的样本。

为了学习这两个分类曲面，作者使用了两个分支的深度学习模型。每个分支都是基于VGG网络的全卷积网络，但最后一层输出的不是离散的标签，而是来自最后的softmax层的概率预测。这些概率预测被用来确定一个样本在特征空间中到两个分类曲面的距离，进而判断它是否属于可行驶区域、障碍区域还是灰色区域。

通过这种方法，可以更好地对不同区域进行分类，并提高自动驾驶车辆的识别和行驶能力。


# Evaluation

Precision（精确率）：指分类器预测为正例的样本中有多少是真正的正例。公式为：TP / (TP + FP)，其中 TP 表示真正的正例，FP 表示假正例。

Recall（召回率）：指分类器正确预测为正例的样本占真正的正例样本总数的比例。公式为：TP / (TP + FN)，其中 TP 表示真正的正例，FN 表示假反例。

FPR（假正例率）：指分类器错误预测为正例的负样本占所有负样本的比例。公式为：FP / (FP + TN)，其中 FP 表示假正例，TN 表示真反例。

FNR（假反例率）：指分类器错误预测为[[负样本]]的正样本占所有正样本的比例。公式为：FN / (TP + FN)，其中 FN 表示假反例，TP 表示真正的正例。

Accuracy（准确率）：指分类器正确预测的样本占所有样本的比例。公式为：(TP + TN) / (TP + TN + FP + FN)，其中 TP、TN、FP、FN 的含义同上。

这些指标可以帮助我们评估分类器在不同方面的性能表现，如精度、召回率、假警报率、漏警率等。通常需要根据具体的应用场景和需求来选择相应的指标来评估模型性能。



























































































































































### 
标签值：$LabelSet = \{unknown, drivable zone, obstacle zone, grey zone\}$
真实值：$G = \{g_{j,k}\}_{0≤j<H,0≤k<W}$ ，其中 $gj,k ∈ LabelSet$
代价地图：$C = \{c_{j,k}\}_{0≤j<H,0≤k<W}$ $c_{j,k}\in[0,1]$
俯视高程图：$X=\{x_{j,k}\}_{0 \leq j<h,0\leq k<w}$











