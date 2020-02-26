---
title: 将适用于 IoT 的 Azure 安全中心连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将用于 IoT 数据的 Azure 安全中心连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 3af51110a4c4604444573f62be65077c786db606
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588631"
---
# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel"></a>将数据从 Azure 安全中心连接到 Azure Sentinel 


> [!IMPORTANT]
> 用于 IoT 数据连接器的 Azure 安全中心目前以公共预览版提供。 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用适用于 IoT 连接器的 Azure 安全中心将 IoT 事件的所有 Azure 安全中心流式传输到 Azure Sentinel。 

## <a name="prerequisites"></a>必备条件

- 对部署了 Azure Sentinel 的工作区的**读取**和**写入**权限
- 必须在相关 IoT 中心**启用** **用于 IoT 的 Azure 安全中心**
- 要连接的**Azure IoT 中心**的**读取**和**写入**权限
- **Azure IoT 中心资源组**的**读** **写**权限

> [!NOTE]
> 尽管你必须在订阅上启用 Azure 安全中心**标准**层许可证才能将 IoT 资源警报流式传输到 azure sentinel，但你只需在订阅上启用 Azure 安全中心**免费**层许可证即可在 Azure sentinel 中查看 IoT 警报的 azure 安全中心。 

## <a name="connect-to-azure-security-center-for-iot"></a>连接到 Azure 安全中心以进行 IoT

1. 在 Azure Sentinel 中，选择 "**数据连接器**"，然后单击 "**用于 IoT 的 Azure 安全中心**" 磁贴。
1. 在右下方的窗格中，单击 "**打开连接器页面**"。 
1. 单击要流式传输到 Azure Sentinel 的每个 IoT 中心订阅旁边的 "**连接**"。 
    - 如果未在该集线器上启用 Azure 安全中心的 IoT，你会看到 "**启用**警告" 消息。 单击 "**启用**" 链接以启动该服务。 
1. 你可以决定是否希望来自 Azure 安全中心的警报在 Azure Sentinel 中自动生成事件。 在 "**创建事件**" 下，选择 "**启用**" 以启用默认分析规则，以便根据连接的安全服务中生成的警报自动创建事件。此规则可以在 "**分析** > **活动**规则" 下进行更改或编辑。

> [!NOTE]
> 更改连接后，会需要一段时间才能刷新中心列表。 

## <a name="log-analytics-alert-display"></a>Log Analytics 警报显示

若要使用 Log Analytics 中的相关架构来显示 Azure 安全中心的 IoT 警报，请执行以下操作：

1. 打开**SecurityInsights** > **SecurityAlert** > **日志**，或搜索**SecurityAlert**。 
2. 使用以下 kql 筛选器，筛选为仅查看 IoT 生成的警报的 Azure 安全中心：

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>服务说明

连接 IoT 中心后，在 Azure Sentinel 中约有15分钟的中心数据。


## <a name="next-steps"></a>后续步骤

本文档介绍了如何将用于 IoT 数据的 Azure 安全中心连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
