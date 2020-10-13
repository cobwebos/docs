---
title: 如何部署视频分析-对象和运动检测 Azure IoT Central 应用程序模板
description: 本指南汇总了使用视频分析-对象和运动检测应用程序模板部署 Azure IoT Central 应用程序的步骤。
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: how-to
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: decfa7020be7778e8ca64a9fb0cb4aac1657da27
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873330"
---
# <a name="how-to-deploy-an-iot-central-application-using-the-video-analytics---object-and-motion-detection-application-template"></a>如何使用视频分析-对象和动作检测应用程序模板部署 IoT Central 应用程序

有关关键 *视频分析-对象和动作检测* 应用程序组件的概述，请参阅 [对象和动作检测视频分析应用程序体系结构](architecture-video-analytics.md)。

以下视频提供了有关如何使用 _视频分析-对象和动作检测应用程序模板_ 部署 IoT Central 解决方案的演练：

> [!VIDEO https://www.youtube.com/embed/Bo3FziU9bSA]

## <a name="deploy-the-application"></a>部署应用程序

完成以下步骤以使用视频分析应用程序模板部署 IoT Central 应用程序：

1. 完成在 [azure 中创建视频分析应用程序 IoT Central (YOLO v3) ](tutorial-video-analytics-create-app-yolo-v3.md) 或在 [azure 中创建视频分析 IoT Central (OpenVINO &trade;) ](tutorial-video-analytics-create-app-openvino.md) 教程来：
    - 创建 Azure 媒体服务帐户。
    - 从 "视频分析-对象和运动检测应用程序模板" 创建 IoT Central 应用程序。
    - 在 IoT Central 应用程序中配置网关设备。 通过网关，照相机设备可以连接到应用程序。

1. 完成 [创建视频分析 (LINUX VM 的 IoT Edge 实例) ](tutorial-video-analytics-iot-edge-vm.md) 或 [教程：创建视频分析的 IoT Edge 实例 (Intel NUC) ](tutorial-video-analytics-iot-edge-nuc.md) 教程来：
    - 创建 Azure IoT Edge 运行时安装的 Azure VM。-准备 IoT Edge 安装以托管视频分析模块。
    - 将 IoT Edge 设备连接到 IoT Central 应用程序。

1. 完成 [监视并管理视频分析应用程序](tutorial-video-analytics-manage.md) 教程，以：
    - 在 IoT Central 应用程序中将对象和动作检测相机添加到网关。
    - 开始处理照相机。
    - 安装本地 media player 以查看 AMS 中捕获的视频。
    - 查看显示检测到的对象的捕获视频。
    - 整理。

## <a name="next-steps"></a>后续步骤

现在，你已概述了部署和使用视频分析应用程序模板的步骤，请参阅 [在 azure 中创建视频分析应用程序 IoT Central (YOLO v3) ](tutorial-video-analytics-create-app-yolo-v3.md) 或 [在 azure 中创建视频分析 IoT Central (OpenVINO &trade;) ](tutorial-video-analytics-create-app-openvino.md) 开始使用。
