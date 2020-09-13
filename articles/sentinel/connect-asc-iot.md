---
title: 将用于 IoT 的 Azure Defender 连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Azure Defender (以前的 Azure 安全中心) 用于 IoT 数据备份到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2020
ms.author: yelevin
ms.openlocfilehash: 822d0c742bbd54b5bab0c69e82652743584a0696
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89659618"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>将数据从 Azure Defender (以前的 Azure 安全中心连接到 Azure Sentinel)  


> [!IMPORTANT]
> Azure Defender for IoT 数据连接器目前为公共预览版。 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Azure Defender for IoT 连接器将所有 Azure Defender for IoT 事件流式传输到 Azure Sentinel。 

## <a name="prerequisites"></a>先决条件

- 对部署了 Azure Sentinel 的工作区的**读取**和**写入**权限
- 必须在相关 IoT 中心 (s**启用** **Azure Defender IoT**) 
- 要连接的**Azure IoT 中心**的**读取**和**写入**权限
- **Azure IoT 中心资源组**的**读****写**权限

## <a name="connect-to-azure-defender-for-iot"></a>连接到用于 IoT 的 Azure Defender

1. 在 Azure Sentinel 中，选择 " **数据连接器** "，然后选择 " **Azure Defender for iot** " (可能仍被从库中称为 "Iot) 的 azure 安全中心"。
1. 在右下方的窗格中，单击 " **打开连接器页面**"。 
1. 单击要流式传输到 Azure Sentinel 的每个 IoT 中心订阅旁边的 " **连接**"。 
    - 如果未在该集线器上启用 Azure Defender for IoT，你会看到 " **启用** 警告" 消息。 单击 " **启用** " 链接以启动该服务。 
1. 你可以决定是否希望 Azure Defender 中的警报在 Azure Sentinel 中自动生成事件。 在 "**创建事件**" 下，选择 "**启用**" 以启用默认分析规则，以便根据连接的安全服务中生成的警报自动创建事件。可以在 "**分析**  >  **活动**规则" 下更改或编辑此规则。

> [!NOTE]
> 更改连接后，会需要一段时间才能刷新中心列表。 

## <a name="log-analytics-alert-display"></a>Log Analytics 警报显示

若要使用 Log Analytics 中的相关架构显示 Azure Defender for IoT 警报，请执行以下操作：

1. 打开**Logs**  >  **SecurityInsights**  >  **SecurityAlert**的日志，或搜索**SecurityAlert**。 
2. 使用以下 kql 筛选器仅查看用于 IoT 的 Azure Defender 生成的警报：

```kusto
SecurityAlert | where ProductName == "Azure Defender for IoT"
``` 

### <a name="service-notes"></a>服务说明

连接 IoT 中心后，在 Azure Sentinel 中约有15分钟的中心数据。


## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Azure Defender for IoT 数据连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
