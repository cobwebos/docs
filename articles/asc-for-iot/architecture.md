---
title: 解决方案体系结构
description: 了解 IoT 服务的 Azure 安全中心中的信息流。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4cc7d1982555f058a4ea23f7d8a6fdc2d83e484d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311752"
---
# <a name="azure-security-center-for-iot-architecture"></a>适用于 IoT 的 Azure 安全中心体系结构

本文介绍了适用于 IoT 解决方案的 Azure 安全中心的功能系统体系结构。

## <a name="azure-security-center-for-iot-components"></a>IoT 组件的 Azure 安全中心

IoT 的 Azure 安全中心由以下组件组成：

- IoT 中心集成
- 设备代理（可选）
- 发送安全消息 SDK
- 分析管道

### <a name="azure-security-center-for-iot-workflows"></a>IoT 工作流的 Azure 安全中心

IoT 的 Azure 安全中心在两个功能工作流之一中工作：内置和增强

### <a name="built-in"></a>内置

在**内置**模式下，当您选择打开 IoT 中心**中的安全**选项时，将启用 IoT 的 Azure 安全中心。 内置模式提供实时监控、建议和警报，提供单步设备可见性和无与伦比的安全性。 内置模式不需要在任何设备上安装代理，并且对记录的活动使用高级分析来分析和保护您的现场设备。

### <a name="enhanced"></a>增强版

在**增强**模式下，在打开 IoT 中心**中的"安全**"选项并在设备上安装 IoT 设备代理 Azure 安全中心后，代理会从设备收集、聚合和分析原始安全事件。 原始安全事件可以包括 IP 连接、进程创建、用户登录和其他安全相关信息。 IoT 设备代理的 Azure 安全中心还处理事件聚合，以帮助避免高网络吞吐量。 代理高度可自定义，允许您将其用于特定任务，例如仅以最快的 SLA 发送重要信息，或将大量安全信息和上下文聚合到更大的细分市场中，从而避免更高的服务成本。

![适用于 IoT 的 Azure 安全中心体系结构](./media/architecture/azure-iot-security-architecture.png)

设备代理和其他应用程序使用 Azure**发送安全消息 SDK**将安全信息发送到 Azure IoT 中心。 IoT 中心拾取此信息并将其转发到 Azure IoT 服务安全中心。

启用 IoT 服务的 Azure 安全中心后，除了转发的数据外，IoT 中心还会发送其所有内部数据，供 Azure IoT 安全中心进行分析。 此数据包括设备云操作日志、设备标识和集线器配置。 所有这些信息都有助于创建适用于 IoT 分析管道的 Azure 安全中心。

用于 IoT 分析管道的 Azure 安全中心还接收来自 Microsoft 和 Microsoft 合作伙伴内部各种来源的其他威胁情报流。 适用于 IoT 整个分析管道的 Azure 安全中心适用于服务上所做的每个客户配置（例如自定义警报和使用发送安全消息 SDK）。

使用分析管道，IoT Azure 安全中心将所有信息流合并，以生成可操作的建议和警报。 管道包含由安全研究人员和专家创建的自定义规则，以及机器学习模型，用于查找偏离标准设备行为和风险分析。

用于 IoT 建议和警报的 Azure 安全中心（分析管道输出）将写入每个客户的日志分析工作区。 包括工作区中的原始事件以及警报和建议，可以使用检测到的可疑活动的确切详细信息进行深入的潜水调查和查询。

## <a name="next-steps"></a>后续步骤

在本文中，您了解了适用于 IoT 解决方案的 Azure 安全中心的基本体系结构和工作流。 要了解有关先决条件以及如何在 IoT 中心入门和启用安全解决方案的更多信息，请参阅以下文章：

- [服务先决条件](service-prerequisites.md)
- [开始](getting-started.md)
- [配置解决方案](quickstart-configure-your-solution.md)
- [在 IoT 中心中启用安全性](quickstart-onboard-iot-hub.md)
- [适用于 IoT 常见问题解答的 Azure 安全中心](resources-frequently-asked-questions.md)
- [用于 IoT 安全警报的 Azure 安全中心](concept-security-alerts.md)
