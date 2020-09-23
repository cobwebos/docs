---
title: Azure RTO 安全模块基础知识的概念说明
description: 了解有关 Azure RTO 概念和工作流的安全模块的基本知识。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 9f816b35fd79d3f158ea12fd2c7e1d811823dc8a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934477"
---
# <a name="security-module-for-azure-rtos-preview"></a>Azure RTO (预览的安全模块) 

使用本文更好地了解 Azure RTO 的安全模块，其中包括功能和优势，以及指向相关配置和引用资源的链接。 

## <a name="azure-rtos-iot-security-module"></a>Azure RTO IoT 安全模块

Azure RTO 安全模块为 Azure RTO 设备提供了一个全面的安全解决方案，作为 NetX 双核产品/服务的一部分。 在 NetX 的产品/服务中，Azure RTO 随附了 Azure IoT 安全模块，并对实时操作系统设备上的常见威胁进行了激活。 

Azure RTO 安全模块在后台运行，提供无缝的用户体验，同时使用每个客户的 IoT 中心的唯一连接发送安全消息。 默认情况下，Azure RTO 的安全模块处于启用状态。  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTO NetX 双核是一种高级的、行业级 TCP/IP 网络堆栈，专门用于深度嵌入的实时和 IoT 应用程序。 Azure RTO NetX 双核是一种双 IPv4 和 IPv6 网络堆栈，提供一组丰富的协议，包括安全和云。 了解有关 [AZURE Rto NetX 双核](https://aka.ms/netxduo) 解决方案的详细信息。

该模块提供了以下功能：

- **检测恶意网络活动**
- **基于自定义警报的设备行为基线**
- **改善设备安全卫生**

## <a name="security-module-for-azure-rtos-architecture"></a>Azure RTO 体系结构的安全模块

Azure RTO 安全模块由 Azure IoT 中间件平台初始化，并使用 IoT 中心客户端向中心发送安全遥测。

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT 安全模块状态图和信息流":::

Azure RTO 安全模块使用三个收集器监视以下设备活动和信息：
- 设备网络活动 **TCP**、 **UDP**和 **ICM**
- 系统信息为 **Threadx** 和 **NetX 双核** 版本
- 检测信号事件

每个收集器都链接到一个优先级组，每个优先级组都有其自己的间隔，其中的可能值为 **Low**、 **Medium**和 **High**。 间隔影响收集和发送数据的时间间隔。

每个时间间隔都是可配置的，并且可以启用和禁用 IoT 连接器，以便进一步 [自定义解决方案](how-to-azure-rtos-security-module.md)。 

## <a name="supported-security-alerts-and-recommendations"></a>支持的安全警报和建议

Azure RTO 安全模块支持特定的安全警报和建议。 完成初始配置后，请确保 [查看并自定义服务的相关警报和建议值](concept-rtos-security-alerts-recommendations.md) 。

## <a name="ready-to-begin"></a>准备好开始了吗？

为 IoT 设备免费下载提供 Azure RTO 安全模块。 每个 Azure 订阅30天试用版提供 IoT 云服务的 Defender。 [立即下载安全模块](https://github.com/azure-rtos/azure-iot-preview/releases) ，让我们开始吧。 

## <a name="next-steps"></a>后续步骤

- 了解 Azure RTO [先决条件和设置](quickstart-azure-rtos-security-module.md)的安全模块入门。
- 详细了解 Azure RTO [安全警报和建议支持](concept-rtos-security-alerts-recommendations.md)的安全模块。 
- 使用 Azure RTO [参考 API](azure-rtos-security-module-api.md)安全模块。

