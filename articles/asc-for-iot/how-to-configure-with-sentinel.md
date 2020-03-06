---
title: Azure 安全中心适用于 Azure Sentinel 配置的 IoT 指南（预览版） |Microsoft Docs
description: 介绍如何将 Azure Sentinel 配置为从 Azure 安全中心接收 IoT 解决方案的数据。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: 082b33332051fee9da2aebe63b0c41edb300afaf
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303539"
---
> [!IMPORTANT]
> Azure Sentinel 中用于 IoT 数据连接器的 Azure 安全中心目前以公共预览版提供。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

# <a name="connect-your-data-from-azure-security-center-for-iot-to-azure-sentinel-preview"></a>将数据从 Azure 安全中心连接到 Azure Sentinel （预览版） 

在本指南中，了解如何将 Azure 安全中心的 IoT 数据连接到 Azure Sentinel。  

> [!div class="checklist"]
> * 先决条件 
> * 连接设置
> * Log Analytics 警报视图 

将 Azure 安全中心的警报连接到 IoT，并将其直接流式传输到 Azure Sentinel。

## <a name="prerequisites"></a>先决条件

- 您必须具有工作区**读取**和**写入**权限。
- 必须在相关 IoT 中心**启用** **用于 Iot 的 Azure 安全中心**。
- 对于要连接的**Azure IoT 中心**，必须同时具有**读取**和**写入**权限。
- 你还必须具有**Azure IoT 中心资源组**的**读取**和**写入**权限。

> [!NOTE]
> 你必须在订阅上运行 Azure 安全中心标准层许可才能发送一般的 Azure 资源警报。 借助 Azure 安全中心的免费层许可，IoT 仅将 Azure 安全中心的警报转发到 Azure Sentinel。 

## <a name="connect-to-azure-security-center-for-iot"></a>连接到 Azure 安全中心以进行 IoT

1. 在 Azure Sentinel 中，选择 "**数据连接器**"，然后单击 "**用于 IoT 的 Azure 安全中心**" 磁贴。
1. 在右侧窗格的底部，单击 "**打开连接器" 页面**。 
1. 单击要流式传输到 Azure Sentinel 的每个 IoT 中心订阅旁边的 "**连接**"。 
    - 如果未在该集线器上启用 Azure 安全中心的 IoT，你将看到启用警告消息。 单击 "**启用**" 链接以启动和启用该服务。 
1. 你可以决定是否希望来自 Azure 安全中心的警报在 Azure Sentinel 中自动生成事件。 在 "**创建事件**" 下，选择 "**启用**" 以启用规则，以便根据生成的警报自动创建事件。  此规则可以在 "**分析** > **活动**规则" 下进行更改或编辑。

> [!NOTE]
>更改连接后，可能需要10秒钟或更长时间才能刷新中心列表。 

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

本文档介绍了如何将用于 IoT 的 Azure 安全中心连接到 Azure Sentinel。 若要了解有关威胁检测和安全数据访问的详细信息，请参阅以下文章：

- 了解如何使用 Azure Sentinel 来了解[你的数据和潜在威胁](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)。

- 了解如何[访问 IoT 安全数据](how-to-security-data-access.md)