---
title: Azure IoT Central 视频分析对象和动作检测 |Microsoft Docs
description: 了解如何使用 IoT Central 中的 "视频分析-对象" 和 "运动检测" 应用程序模板构建 IoT Central 应用程序。 此模板使用实时视频分析和连接相机。
author: KishorIoT
ms.author: nandab
ms.date: 07/27/2020
ms.topic: conceptual
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
ms.openlocfilehash: 48808f762536390287bae40e8af3849da20b81c2
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91874282"
---
# <a name="video-analytics---object-and-motion-detection-application-architecture"></a>视频分析-对象和动作检测应用程序体系结构

**视频分析-对象和动作检测**应用程序模板允许你构建 IoT 解决方案，其中包括实时视频分析功能。

:::image type="content" source="media/architecture-video-analytics/architecture.png" alt-text="视频分析对象和动作检测组件的图示概述。":::

视频分析解决方案的主要组件包括：

## <a name="live-video-analytics-lva"></a>实时视频分析 (LVA) 

LVA 为你提供了一个平台，用于构建跨越边缘和云的智能视频应用程序。 平台允许构建跨越边缘和云的智能视频应用程序。 此平台提供了捕获、记录和分析实时视频的功能，并且可以将结果（视频和/或视频分析）发布到 Azure 服务。 Azure 服务可以在云或边缘中运行。 该平台可用于通过视频分析增强 IoT 解决方案。

有关详细信息，请参阅 GitHub 上的 [实时视频分析](https://github.com/Azure/live-video-analytics) 。

## <a name="iot-edge-lva-gateway-module"></a>IoT Edge LVA 网关模块

IoT Edge LVA 关模块将照相机实例化为新设备，并使用 IoT 设备客户端 SDK 将它们直接连接到 IoT Central。

在此参考实现中，设备使用边缘的对称密钥连接到解决方案。 有关设备连接的详细信息，请参阅 [连接到 Azure IoT Central](../core/concepts-get-connected.md)

## <a name="media-graph"></a>媒体图

Media graph 允许您定义从何处捕获介质，如何处理介质以及在何处交付结果。 可以按所需方式连接组件或节点来配置 media graph。 有关详细信息，请参阅 GitHub 上的 [媒体关系图](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph) 。

## <a name="next-steps"></a>后续步骤

建议的下一步是了解如何 [使用视频分析-对象和运动检测应用程序模板部署 IoT Central 应用程序](tutorial-video-analytics-deploy.md)。
