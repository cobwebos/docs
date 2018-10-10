---
title: include 文件
description: IoT Edge
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: kgremban
ms.openlocfilehash: a8160e677fa99d8cb691db39d7f29ba6eddbd261
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004673"
---
## <a name="enabling-extended-offline-operation-preview"></a>启用扩展的脱机操作（预览）
从 Edge 运行时的 [v1.0.2 版本](https://aka.ms/edge102)起，可配置 Edge 设备和与之连接的下游设备，以处理扩展脱机操作。 

借助此功能，本地模块或下游设备可根据需要向 Edge 设备重新进行身份验证，即使从 IoT 中心断开连接也可使用消息和方法相互进行通信。 有关详细信息和此功能的范围，请参阅此[博客文章](https://aka.ms/iot-edge-offline)和[概念文章](../articles/iot-edge/offline-capabilities.md)。

在网关中启用扩展脱机方案，可在边缘设备和将与之连接的下游设备之间建立父子节点关系。

1. 从 IoT 中心门户的“边缘设备详细信息”选项卡，单击顶部命令栏中的“管理子设备(预览)”按钮。

1. 单击“**+ 添加**”按钮。

1. 从设备列表中，选择子设备并使用向右键选择要作为子级添加的设备。

1. 单击“确定”以确认。

1. 从“边缘设备详细信息”的“设置模块”屏幕，单击“配置高级边缘运行时设置”，在“边缘中心”环境变量下，添加条目 UpstreamProtocol 和值 MQTT。 同时为“边缘代理”也添加相同的环境变量和值。 

1. 单击两次“下一步”之后，单击“保存”，确保提交所做更改。

现在已启用边缘设备及其子设备处理扩展脱机操作。  