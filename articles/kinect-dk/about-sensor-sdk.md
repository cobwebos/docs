---
title: 关于 Azure Kinect 传感器 SDK
description: 概述 Azure Kinect 传感器软件开发工具包 (SDK) 及其功能和工具。
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: article
keywords: azure, kinect, rgb, IR, 录制, 传感器, sdk, 访问, 深度, 视频, 相机, imu, 运动, 传感器, 音频, 麦克风, matroska, 传感器 sdk, 下载
ms.openlocfilehash: 17c1b33120eacb5d0c6d3c02e692d1488ef474e6
ms.sourcegitcommit: fe6c9a35e75da8a0ec8cea979f9dec81ce308c0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2020
ms.locfileid: "73932226"
---
# <a name="about-azure-kinect-sensor-sdk"></a>关于 Azure Kinect 传感器 SDK

本文概述 Azure Kinect 传感器软件开发工具包 (SDK) 及其功能和工具。

## <a name="features"></a>功能

Azure Kinect 传感器 SDK 提供对 Azure Kinect 设备配置和硬件传感器流的跨平台低级访问，包括：

- 深度相机访问和模式控制（被动 IR 模式，以及宽视场和窄视场深度模式） 
- RGB 相机访问和控制（例如曝光和白平衡） 
- 运动传感器（陀螺仪和加速度传感器）访问 
- 同步的深度 RGB 相机流，相机之间的延迟可配置 
- 外部设备同步控制，设备之间的延迟偏移量可配置 
- 用于处理图像分辨率、时间戳等的相机帧元数据访问。 
- 设备校准数据访问 

## <a name="tools"></a>工具

- 用于监视设备数据流和配置不同模式的 [Azure Kinect 查看器](azure-kinect-viewer.md)。
- 使用 [Matroska 容器格式](record-file-format.md)的 [Azure Kinect 录制器](azure-kinect-recorder.md)和播放读取器 API。
- Azure Kinect DK [固件更新工具](azure-kinect-firmware-tool.md)。

## <a name="sensor-sdk"></a>传感器 SDK

- [下载传感器 SDK](sensor-sdk-download.md)。
- [GitHub 上已提供传感器 SDK 的开放源代码](https://github.com/microsoft/Azure-Kinect-Sensor-SDK)。
- 有关用法详细信息，请参阅[传感器 SDK API 文档](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/index.html)。

## <a name="next-steps"></a>后续步骤

你现已了解 Azure Kinect 传感器 SDK，接下来还可以：
>[!div class="nextstepaction"]
>[下载传感器 SDK 代码](sensor-sdk-download.md)

>[!div class="nextstepaction"]
>[查找和打开设备](find-then-open-device.md)
