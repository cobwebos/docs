---
title: Azure Kinect DK 深度相机
description: Azure Kinect DK 深度相机的概念
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 传感器, sdk, 深度相机, tof, 原理, 性能, 失效
ms.openlocfilehash: ec3e2aed0c0ec191e5b69dfd2db767f13eb16ecd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453016"
---
# <a name="azure-kinect-dk-depth-camera"></a>Azure Kinect DK 深度相机

本页介绍如何在 Azure Kinect DK 中使用深度相机。 深度相机是两个相机中的第二个。 如前面的部分中所述，另一个相机是 RGB 相机。  

## <a name="operating-principles"></a>工作原理

Azure Kinect DK 深度相机实现调幅连续波 (AMCW) 时差测距 (ToF) 原理。 该相机将近红外 (NIR) 频谱中的调制光投射到场景中。 然后，它会记录光线从相机传播到场景，然后从场景返回到相机所花费的间接时间测量值。

处理这些测量值可以生成深度图。 深度图是图像每个像素的一组 Z 坐标值，以毫米为单位。

连同深度图一起，我们还可以获得所谓的清晰 IR 读数。 清晰 IR 读数中的像素值与从场景返回的光线量成正比。 图像类似于普通的 IR 图像。 下图显示了示例深度图（左）的对应的清晰 IR 图像（右）。

![深度图和 IR 图](./media/concepts/depth-camera-depth-ir.png)

## <a name="key-features"></a>主要功能

深度相机的技术特征包括：

- 配备高级像素技术的 1 兆像素 ToF 成像芯片，实现更高的调制频率和深度精度。
- 两个 NIR 激光二极管实现近距和宽视场 (FoV) 深度模式。
- 全球最小的 3.5μm x 3.5μm ToF 像素。
- 自动像素增益选择支持较大的动态范围，允许捕获清晰的近距和远距对象。
- 全局快门可帮助改善日光下的拍摄性能。
- 多相位深度计算方法能够实现可靠的准确度，即使芯片、激光和电源存在差异。
- 较低的系统误差和随机误差。

![深度模块](./media/concepts/depth-camera-depth-module.jpg)

深度相机将原始的调制 IR 图像传输到电脑主机。 在电脑上，GPU 加速的深度引擎软件会将原始信号转换为深度图。 深度相机支持多种模式。 **窄视场 (FoV)** 模式非常适合 X、Y 维度范围较小，但 Z 维度范围较大的场景。 如果场景中的 X、Y 范围较大，但 Z 范围较小，则**宽 FoV 模式**更合适。

深度相机支持 **2x2 装箱模式**，这种模式与**非装箱模式**相比，可以扩展 Z 范围。 装箱的代价是降低图像分辨率。 所有模式都能够以高达 30 帧/秒 (fps) 的速率运行，但 1 兆象素 (MP) 模式除外，它的最大运行帧速率为 15 fps。 深度相机还提供**被动 IR 模式**。 在此模式下，照像机上的照明器不会激活，只能观测到环境光。

## <a name="camera-performance"></a>相机性能

相机的性能以系统误差和随机误差来衡量。

### <a name="systematic-error"></a>系统误差

系统误差定义为消噪后测得的深度与正确（真实）深度之差。 我们会根据静态场景的许多帧计算时态平均值，以消除尽可能多的深度噪声。 更确切地说，系统误差定义为：

![深度系统误差](./media/concepts/depth-camera-systematic-error.png)

其中，*d<sub>t</sub>* 表示时间 *t* 处的测量深度，*N* 是求平均过程使用的帧数，*d<sub>gt</sub>* 是真实深度。

深度相机的系统误差规范不包括多路径干扰 (MPI)。 当某个传感器像素集成了多个对象反射的光时，就会发生 MPI。 使用较高的调制频率以及稍后将会介绍的深度失效可以在深度相机中部分缓解 MPI。

### <a name="random-error"></a>随机误差

假设我们在没有移动相机的情况下拍摄了同一对象的 100 张图像。 在这 100 张图像中，每张图像的对象深度略有不同。 这种差异是散粒噪声造成的。 发生散粒噪声的原因是，在一段时间内，进入传感器的光子数因某种随机因素而有变化。 我们将静态场景中的这种随机误差定义为一段时间内的深度标准偏差，其计算公式为：

![深度随机误差](./media/concepts/depth-camera-random-error.png)

其中，*N* 表示深度测量值数，*d<sub>t</sub>* 表示时间 *t* 处的深度测量值，*d* 表示基于所有深度测量值 *d<sub>t</sub>* 计算出的平均值。

## <a name="invalidation"></a>失效

在某些情况下，深度相机可能无法提供某些像素的正确值。 在这种情况下，深度像素将会失效。 无效的像素由深度值 0 表示。 深度引擎无法生成正确值的原因包括：

- 超出活动 IR 照明遮罩范围
- IR 信号饱和
- IR 信号强度低
- 滤波异常
- 多路径干扰

### <a name="illumination-mask"></a>照明遮罩

当像素超出活动 IR 光照的遮罩范围时，它们将会失效。 我们不建议使用此类像素的信号来计算深度。 下图显示了超出照明遮罩范围而导致像素失效的示例。 失效的像素包括宽 FoV 模式的圆圈（左）和窄 FoV 模式的六边形（右）外部的黑色像素。

![超出照明遮罩范围时失效](./media/concepts/depth-camera-invalidation-illumination-mask.png)

### <a name="signal-strength"></a>信号强度

当像素包含饱和的 IR 信号时，它们将会失效。 像素饱和后，相位信息将会丢失。 下图显示了 IR 信号饱和导致像素失效的示例。 请查看指向深度图像和 IR 图像中的示例像素的箭头。

![饱和时失效](./media/concepts/depth-camera-invalidation-saturation.png)

如果 IR 信号强度不够，以致无法生成深度，则也可能会发生像素失效。 下图显示了 IR 信号强度低导致像素失效的示例。 请查看指向深度图像和 IR 图像中的示例像素的箭头。

![信号强度低时失效](./media/concepts/depth-camera-invalidation-low-signal.png)

### <a name="ambiguous-depth"></a>歧义深度

如果像素从场景中的多个对象收到了信号，则它们也可能会失效。 在角落中经常会看到这种像素失效的情况。  由于场景的几何结构，照相发出的 IR 光会从一堵墙反射到另一堵墙。 这种反射光会导致测得的像素深度出现歧义。 深度算法中的滤波器会检测这些有歧义的信号，并使像素失效。

下图显示了多路径检测导致像素失效的示例。 你还可能会发现，在一个相机视图中失效的表面区域（上排）可能会在不同的相机视图中重新出现（下排）。 此图演示在一个透视图中失效的表面可能会在另一个透视图中显示。

![多路径失效 - 角落](./media/concepts/depth-camera-invalidation-multipath.png)

多路径的另一种常见情况是像素包含来自前景和背景的混合信号（例如围绕对象边缘）。 在快动作场景中，你可能会看到边缘的四周有更多失效的像素。 更多像素失效的原因在于原始深度捕获的曝光间隔。

![多路径失效 - 边缘](./media/concepts/depth-camera-invalidation-edge.png)

## <a name="next-steps"></a>后续步骤

[坐标系](coordinate-systems.md)
