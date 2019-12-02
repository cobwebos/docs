---
title: Azure Kinect DK Windows 版的比较
description: Azure Kinect DK 与 Kinect for Windows v2 的硬件和软件差别
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: Kinect, Windows, v2, Azure Kinect, 比较, SDK, 差别, 硬件, 软件
ms.openlocfilehash: ec105cef0c52b02c763487fbe5b2d8c018315f4c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452509"
---
# <a name="azure-kinect-and-kinect-windows-v2-comparison"></a>Azure Kinect 与 Kinect Windows v2 的比较

Azure Kinect DK 硬件和软件开发工具包与 Kinect for Windows v2 之间有差别。 任何现有的 Kinect for Windows v2 应用程序不能直接与 Azure Kinect DK 配合工作，需要移植到新的 SDK。  

## <a name="hardware"></a>硬件

下表列出了 Azure Kinect 开发工具包与 Kinect for Windows v2 之间的大致差别。

|    |      | Azure Kinect DK |  Kinect for Windows v2 |
|----------|---------------|--------| ------------|
| **音频** | 详细信息  | 7 麦克风环形阵列 | 4 麦克风线性相控阵列 |
| **运动传感器** | 详细信息 | 3 轴加速度传感器，3 轴陀螺仪 | 3 轴加速度传感器 |
| **RGB 相机**    | 详细信息 | 3840 x 2160 像素 @30 fps | 1920 x 1080 像素 @30 fps |
| **深度相机**  | 方法   | 时差测距 | 时差测距 |
|                   | 解决方法 | 640 x 576 像素 @30 fps | 512 x 424 像素 @ 30 fps |
|                   |            | 512 x 512 像素 @30 fps |                       |
|                   |            | 1024x1024 像素 @15 fps |                       |
| **连接** | 数据 | USB3.1 Gen 1，附带 USB Type-C  | USB 3.1 Gen 1|
|  | 电源 | 外部 PSU 或 USB-C | 外部 PSU |
|  | 同步 | RGB 和深度内部同步，外部设备到设备同步| 仅限 RGB 和深度内部同步 |
| **物理规格** | 维度 | 103 x 39 x 126 毫米 | 249 x 66 x 67 毫米 |
|  | 重量 | 440 克 | 970 克 |
| | 安装 | 一颗 ¼-20 UNC 螺丝。 四个内部螺丝固定点 | 一颗 ¼-20 UNC 螺丝 |

在 [Azure Kinect DK 硬件](hardware-specification.md)文档中查找更多详细信息。

## <a name="sensor-access"></a>传感器访问

下表提供了低级别设备传感器访问功能的比较。

| **功能**| **Azure Kinect** | **Kinect for Windows** | **说明** |
|---------|---------|------------|---------|
| **深度** | ✔️ | ✔️ |    |   |
| **IR** | ✔️ | ✔️ |  |
| **彩色** | ✔️ | ✔️ | 彩色格式支持差异，Azure Kinect DK 支持以下相机控件：曝光、白平衡、亮度、对比度、饱和度、清晰度和增益控制 |
| **音频** | ✔️ | ✔️ | 通过语音 SDK 或 Windows 本机 API 访问 Azure Kinect DK 麦克风 |
| **IMU** | ✔️ |  | Azure Kinect DK 有完整的 6 轴 IMU，Kinect for Windows 仅提供单轴 IMU |
| **校准数据** | ✔️ | ✔️ | OpenCV 兼容的相机型号校准 |
| **深度 RGB 内部同步** | ✔️ | ✔️ |  |
| **外部同步**| ✔️|  | Azure Kinect DK 允许对外部同步延迟进行编程 |
| **与多个客户端共享访问** | | ✔️ | Azure Kinect 传感器 SDK 依赖于使用 WinUSB/libUSB 来访问设备，不会实现某个服务来启用与多个进程共享设备访问 |
| **流录制/播放工具** | ✔️ | ✔️ | Azure Kinect DK 使用基于 Matroska 容器的开源实现 |

## <a name="features"></a>功能

Azure Kinect SDK 的功能集与 Kinect for Windows v2 不同，详述如下：

| **Kinect v2 功能** | **Kinect v2 数据类型** | **Azure Kinect SDK/服务** |
|--------|--------|------|
| 传感器数据访问 |DepthFrame| [传感器 SDK - 检索图像](retrieve-images.md) 
| |InfraredFrame | [传感器 SDK - 检索图像](retrieve-images.md) 
| | ColorFrame | [传感器 SDK - 检索图像](retrieve-images.md) | 
| | AudioBeamFrame |目前不支持 
| 人体跟踪 | BodyFrame | 人体跟踪 SDK |
| | BodyIndexFrame | 人体跟踪 SDK  |
| 协调映射|CoordinateMapper| [传感器 SDK - 图像转换](use-image-transformation.md) |
|人脸跟踪 | FaceFrame | [认知服务：人脸](https://azure.microsoft.com/services/cognitive-services/face/)       |
|    语音识别    |    不适用                      |    [认知服务：语音](https://azure.microsoft.com/services/cognitive-services/directory/speech/)     |

## <a name="next-steps"></a>后续步骤

[Kinect for Windows 开发人员页](https://developer.microsoft.com/windows/kinect)
