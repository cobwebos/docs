---
title: 解决方案体系结构
description: 了解 Azure Defender for IoT 服务中的信息流。
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
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 3d26d9e3d686ad7c34e7493dc1413b7a9e7a2f6b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934855"
---
# <a name="azure-defender-for-iot-architecture"></a>用于 IoT 体系结构的 Azure Defender

本文介绍了适用于 IoT 解决方案的 Defender 的功能系统体系结构。

## <a name="defender-for-iot-components"></a>用于 IoT 组件的 Defender

用于 IoT 的 Defender 由以下组件组成：

- IoT 中心集成
- 设备代理 (可选) 
- 发送安全消息 SDK
- 分析管道

### <a name="defender-for-iot-workflows"></a>用于 IoT 工作流的 Defender

Defender for IoT 适用于以下两个功能工作流之一：内置和增强

### <a name="built-in"></a>内置

在 **内置** 模式下，在你选择启用 iot 中心中的 " **安全** " 选项时，会启用 "用于 iot 的 Defender"。 内置模式提供实时监视、建议和警报，提供单步设备可见性和无与伦比的安全性。 内置模式不需要在任何设备上安装代理，并对记录的活动使用高级分析来分析和保护字段设备。

### <a name="enhanced"></a>增强版

在 **增强** 模式下，在 IoT 中心启用 **安全** 选项并在设备上安装用于 IoT 设备代理的 Defender 后，代理会从设备收集、聚合和分析原始安全事件。 原始安全事件可能包括 IP 连接、进程创建、用户登录和其他安全相关信息。 用于 IoT 设备代理的 Defender 还处理事件聚合，以帮助避免高网络吞吐量。 代理具有高度可自定义性，使你可以将其用于特定的任务，例如，仅在最快的 SLA 中发送重要信息，或者将广泛的安全信息和上下文聚合到更大的段，以避免服务成本更高。

![用于 IoT 体系结构的 Defender](./media/architecture/azure-iot-security-architecture.png)

设备代理和其他应用程序使用 **azure 发送安全消息 SDK** 将安全信息发送到 Azure IoT 中心。 IoT 中心会提取此信息并将其转发到 IoT 服务的 Defender。

一旦启用了用于 IoT 服务的 Defender，IoT 中心还将发送其所有内部数据，供 Defender 用于分析。 此数据包括设备云操作日志、设备标识和中心配置。 所有这些信息都有助于创建用于 IoT 分析管道的 Defender。

用于 IoT 分析的 Defender 还可以从 Microsoft 和 Microsoft 合作伙伴的各种来源接收其他威胁情报流。 适用于 IoT 整个分析管道的 Defender 适用于在服务 (上进行的每个客户配置，例如自定义警报和发送安全消息 SDK) 的使用。

使用分析管道，将所有信息流结合起来，以生成可操作的建议和警报。 该管道包含由安全研究人员和专家创建的自定义规则，以及从标准设备行为和风险分析搜索偏差的机器学习模型。

用于 IoT 的 Defender 建议和警报 (分析管道输出) 写入每个客户的 Log Analytics 工作区。 如果工作区中包含原始事件以及警报和建议，则可以使用检测到的可疑活动的确切详细信息来深入探讨调查和查询。

## <a name="next-steps"></a>后续步骤

本文介绍了用于 IoT 解决方案的基本体系结构和工作流。 若要详细了解先决条件、如何开始和在 IoT 中心启用安全解决方案，请参阅以下文章：

- [服务先决条件](service-prerequisites.md)
- [入门](getting-started.md)
- [配置解决方案](quickstart-configure-your-solution.md)
- [启用 IoT 中心中的安全性](quickstart-onboard-iot-hub.md)
- [Defender for IoT 常见问题](resources-frequently-asked-questions.md)
- [Defender for IoT 安全警报](concept-security-alerts.md)
