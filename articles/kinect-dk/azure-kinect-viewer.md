---
title: Azure Kinect 查看器
description: Kinect for Azure 传感器数据流查看器主页
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: azure, kinect, 传感器, 查看器, 可视化, 深度, rgb, 颜色, imu, 音频, 麦克风, 点云
ms.openlocfilehash: 6d7acda9c72c995e789ac620e4e1f3b08d3666ec
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453458"
---
# <a name="azure-kinect-viewer"></a>Azure Kinect 查看器

使用 Azure Kinect 查看器（可在工具的安装目录中找到，其文件名为 `k4aviewer.exe`，例如，其路径为 `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`，其中 `X.Y.Z` 是安装的 SDK 版本）可将所有设备数据流可视化，以便：

* 验证传感器是否正常工作。
* 帮助定位设备。
* 体验不同的相机设置。
* 读取设备配置。
* 播放使用 [Azure Kinect 录制器](azure-kinect-recorder.md)录制的内容。

Azure Kinect 查看器是[开源的](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/tools/k4aviewer)，可作为一个例子来演示如何使用 API。

## <a name="use-viewer"></a>使用查看器

该查看器能够以两种模式运行：结合传感器发送的实时数据，或者结合录制的数据（[Azure Kinect 录制器](azure-kinect-recorder.md)）。

### <a name="start-application"></a>启动应用程序

运行 `k4aviewer.exe` 启动应用程序。

![使用查看器检查设备固件版本](./media/how-to-guides/open-viewer.png)

### <a name="use-the-viewer-with-live-data"></a>结合实时数据使用查看器

1. 在“打开设备”部分，选择设备的“序列号”将其打开。   如果该设备未列出，请选择“刷新”。 
2. 选择“打开设备”按钮。 
3. 选择“启动”，开始使用默认设置流式传输数据。 

### <a name="use-the-viewer-with-recorded-data"></a>结合录制的数据使用查看器

在“打开录制内容”部分，导航到录制的文件并将其选中。 

## <a name="check-device-firmware-version"></a>检查设备固件版本

如下图所示，在配置窗口中访问设备固件版本。

![使用查看器检查设备固件版本](./media/how-to-guides/check-firmware-update.png)

例如，在本例中，深度相机 ISP 运行的是 FW 1.5.63。

## <a name="depth-camera"></a>深度相机

深度相机查看器将显示两个窗口：

* 一个窗口的名称为“有效亮度”，即，显示 IR 亮度的灰度图像。 
* 另一个窗口的名称为“深度”，以不同的颜色表示深度数据。 

将鼠标悬停在深度窗口中的像素上可以查看深度传感器的值，如下所示。

![深度视图](./media/how-to-guides/depth-camera.png)

## <a name="rgb-camera"></a>RGB 相机

下图显示了彩色相机视图。

![RGB 视图](./media/how-to-guides/viewer-rgb-camera.png)

在流式传输期间，可以通过配置窗口控制 RGB 相机设置。

![RGB 相机控件](./media/how-to-guides/rgb-camera-settings.png)

## <a name="inertial-measurement-unit-imu"></a>惯性测量单元 (IMU)

IMU 窗口包含两个组件：加速度传感器和陀螺仪。

上半部分是加速度传感器，以米/秒<sup>2</sup> 为单位显示线性加速度。  它包含了重力加速度，因此，如果将它平稳地放在桌面上，Z 轴可能会显示大约 -9.8 米/秒<sup>2</sup>。

下半部分是陀螺仪，以弧度/秒为单位显示旋转运动

![运动传感器视图](./media/how-to-guides/viewer-mu-settings.png)

## <a name="microphone-input"></a>麦克风输入

麦克风视图显示在每个麦克风中听到的声音的表示形式。 如果没有声音，图形是空白的；否则，你会看到深蓝色的波形，其顶部叠加了浅蓝色的波形。

深色波形表示麦克风在该时间切片内观测到的最小值和最大值。 浅色波形表示麦克风在该时间切片内观测到的值的均方根。

![麦克风输入视图](./media/how-to-guides/microphone-data.png)

## <a name="point-cloud-visualization"></a>点云可视化

借助 3D 中可视化的深度，可以使用指示的键在图像中移动。

![深度点云](./media/how-to-guides/depth-point-cloud.png)

## <a name="synchronization-control"></a>同步控制

配置多设备同步时，可以使用查看器将设备配置为独立（默认）、主控或从属模式。
更改配置或插入/移除同步线缆时，请选择“刷新”进行更新。 

![外部同步控制](./media/how-to-guides/sync-control.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[外部同步设置指南](https://support.microsoft.com/help/4494429/sync-multiple-azure-kinect-dk-devices)
