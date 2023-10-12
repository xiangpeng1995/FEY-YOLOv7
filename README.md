# FEY-YOLOv7：基于面部小目标动态追踪的驾驶员疲劳检测算法
+ [A Driver Fatigue Detection Algorithm Based on Dynamic Tracking of Small Facial Targets Using YOLOv7](https://search.ieice.org/bin/summary_advpub.php?id=2023EDP7093&category=D&lang=E&abst=)
+ Github: [FEY-YOLOv7](https://github.com/Arrowes/FEY-YOLOv7)

## 项目简介
在车辆安全技术中，驾驶员疲劳检测应用广泛，其准确性和实时性至关重要。在本文中，我们提出了一种基于人脸眼睛和嘴巴打哈欠动态追踪的YOLOv7驾驶员疲劳检测算法，其中YOLOv7针对眼睛和嘴巴小目标进行了优化，结合PERCLOS算法，称为FEY-YOLOv7。在YOLOv7中插入Coordinate Attention(CA)模块，将重点放在坐标信息上，以提高动态追踪的准确性；增加一个小目标检测头，使网络能够提取小目标特征，增强了对眼睛、嘴巴的检测性能，提高了检测的精度。对YOLOv7的网络架构进行了显著简化，以减少计算量，提高检测速度。通过提取视频中每一帧驾驶员睁眼、闭眼、张嘴、闭嘴四种面部行为状态，利用 PERYAWN 判定算法对驾驶员状态进行标注及检测。在RGB-infrared Datasets上使用Guided Image Filtering图像增强算法，并进行混合训练及验证，验证结果表明，FEY-YOLOv7的mAP达到了0.983，FPS达到101，说明FEY-YOLOv7在准确率和速度上都优于最先进的方法，为基于图像信息的驾驶员疲劳检测提供了一个有效和实用的方案。
## 技术点
### CA注意力机制
CA模块在空间维度上自适应地对不同位置的特征进行加权，从而使得模型更加关注重要的空间位置，不仅捕获跨通道信息，还捕获方向感知和位置敏感信息，这有助于模型更准确地定位和识别感兴趣的对象。 
具体来说，Coordinate Attention引入了一个全局自注意力模块，该模块可以对输入特征图的每个位置进行自适应的加权。该加权由两个步骤完成：Coordinate信息嵌入和Coordinate Attention生成。
1.	通过两个全局平均池化操作，分别计算输入特征图在通道维度上和空间维度上的均值。这两个均值分别表示了输入特征图在每个通道和每个空间位置的重要性。
2.	将通道维度上的均值与空间维度上的均值进行相乘，得到一个权重矩阵，该权重矩阵表示了每个位置在通道和空间维度上的重要性，并将其应用于输入特征图中。最终，每个位置的特征将与其在通道和空间维度上的重要性相关联，从而使得模型更加关注重要的空间位置。
<img alt="图 43" src="https://raw.sevencdn.com/Arrowes/Blog/main/images/Project-CA.png" width='38%'/>  

### PERYAWN疲劳评估算法
$PERYAWN = E/(N - w · M)$

其中，E是闭眼帧数，N是单位时间内总帧数，M代表“张嘴”的帧数，w是加权因子。为了量化四个驾驶员特征（睁眼、闭眼、张嘴和闭嘴）的检测结果，我们采用公式4和5，并将权重w设置为0.15。基于本研究中数据集标签的实际情况，我们确定当单位时间内的PERYAWN值超过0.20时，驾驶员处于疲劳状态。

为了更直观地评估该算法在检测驾驶员状态方面的有效性，我们使用提出的PERYAWN参数作为定量指标来评估多个10秒的测试视频（每秒24帧）。如下图所示，如果PERYAWN值超过0.2，则将驾驶员分类为疲劳状态。如果该值超过0.5，则认为驾驶员处于严重疲劳状态。我们将检测到的状态与驾驶员的实际状态进行比较，并计算准确性。除了一些视频检测结果出现偏差外，所有结果都能够准确检测到三种状态：'正常'，'疲劳'和'严重疲劳'。
<img alt="图 44" src="https://raw.sevencdn.com/Arrowes/Blog/main/images/Project-PERYAWN.png" width='80%'/>  

### 数据增强
+ 引导滤波算法
引导滤波就是基于局部线性回归，用引导图像的信息来指导输入图像的滤波过程，通常用于图像处理中的去噪、平滑、增强等任务。
引导滤波器的基本思想是，对于输入图像p中的每个像素，使用引导图像I中与该像素相关的信息来进行滤波, 引导滤波器将输入图像p的每个像素表示为一个线性组合, 利用线性岭回归模型对线性系数ak,bk进行求解；本文利用原图的灰度图实现了图像的细节增强，优化了对眼睛、嘴巴的目标检测效果，实现过程如下图：
<img alt="图 45" src="https://raw.sevencdn.com/Arrowes/Blog/main/images/Project-GuidedImageFilter.png" width='80%'/>  

+ Static Crop+Mosaic预处理
对图像进行35-59% Horizontal Region, 25-75% Vertical Region的拆分，并进行4合1的Mosaic拼接，间接实现了面部特征的放大，将数据集重点偏向眼睛、嘴巴这类小目标，优化了算法对小目标的检测性能和鲁棒性。
<img alt="图 46" src="https://raw.sevencdn.com/Arrowes/Blog/main/images/Project-Mosaic.png" width='80%'/>  


## 实现效果
<img alt="图 48" src="https://raw.sevencdn.com/Arrowes/Blog/main/images/Project-FEYdetectResult.png" width='80%'/>  

<img alt="图 47" src="https://raw.sevencdn.com/Arrowes/Blog/main/images/Project-FEYresult.png" width='80%'/>  