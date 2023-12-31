---
tags: 名词解释
alias: LTS回归
---

Least Trimmed Squares Regression (LTSR) 
?
最小截断平方和回归，是稳健回归技术的一种，目标函数是小的[[残差和]]
是一种回归方法，它在最小二乘回归的基础上，通过去除一些数据点的影响来提高模型的鲁棒性。
在路缘识别中，LTSR可以应用于拟合道路边缘的模型。因为道路边缘通常是由一些局部特征点（如道路边缘的拐角点）组成的，而这些点可能会被异常点（如离群值）干扰。通过采用LTSR方法，可以去除这些异常点的影响，提高模型的鲁棒性。
具体来说，对于道路边缘识别任务，可以通过收集车辆行驶时的激光雷达或摄像头数据来获取道路边缘点的坐标信息。然后，将这些坐标点作为LTSR方法的输入数据，拟合出一条道路边缘线的模型。在拟合过程中，采用LTSR方法可以去除掉离群值的干扰，提高模型的精度和鲁棒性。
总的来说，LTSR方法可以在道路边缘识别等任务中起到很好的作用，帮助提高模型的鲁棒性和精度。
<!--SR:!2023-05-19,1,230-->
