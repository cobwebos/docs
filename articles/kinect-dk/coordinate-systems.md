---
title: Azure Kinect DK 坐标系
description: 与 Azure DK 传感器关联的 Azure Kinect DK 坐标系说明
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, 传感器, sdk, 深度相机, tof, 原理, 性能, 失效
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508527"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Azure Kinect DK 坐标系

本文介绍用于 2D 和 3D 坐标系的约定。  每个传感器设备有关联的独立坐标系，使用[校准函数](use-calibration-functions.md)可以转换这些坐标系之间的点。 [转换函数](use-image-transformation.md)转换坐标系之间的整个图像。  

## <a name="2d-coordinate-systems"></a>2D 坐标系

 深度相机和彩色相机与独立的 2D 坐标系相关联。 [x,y] 坐标以像素为单位，其中，*x* 的范围为 0 到宽度-1，*y* 的范围为 0 到高度-1。 宽度和高度取决于所选的深度相机和彩色相机工作模式。 像素坐标 `[0,0]` 对应于图像的左上角像素。 像素坐标可以是表示子像素坐标的小数。

2D 坐标系以 0 为中心，即，子像素坐标 `[0.0, 0.0]` 表示中心，`[0.5,0.5]` 表示像素的右下角，如下所示。

   ![2D 坐标系](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>3D 坐标系

每个相机、加速度传感器和陀螺仪都与独立的 3D 坐标空间系统相关联。

3D 坐标系中的点以指标 [X,Y,Z] 坐标三元组的形式表示，单位为毫米。

### <a name="depth-and-color-camera"></a>深度相机和彩色相机

原点 `[0,0,0]` 位于相机焦点处。 坐标系的方向是正 X 轴向右，正 Y 轴向下，正 Z 轴向上。

深度相机向下朝彩色相机倾斜 6 度，如下所示。 

深度相机使用两个照明器。 在窄视场 (NFOV) 模式下使用的照明器与深度相机的用例相符，因此照明器不倾斜。 在宽视场 (WFOV) 模式下使用的照明器相对于深度相机向下额外倾斜 1.3 度。

![3D 坐标约定](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>陀螺仪和加速度传感器

陀螺仪的原点 `[0,0,0]` 与深度相机的原点相同。 加速度传感器的原点与其物理位置相一致。 加速度传感器和陀螺仪的坐标系都位于右侧。 坐标系的正 X 轴向后，正 Y 轴向左，正 Z 轴向下，如下所示。

![IMU 坐标系](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>后续步骤

[了解 Azure Kinect 传感器 SDK](about-sensor-sdk.md)