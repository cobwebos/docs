---
title: '为 Defender (预览版配置 Azure Sentinel) '
description: 介绍如何配置 Azure Sentinel 以便接收来自你的用于 IoT 的 Defender 解决方案的数据。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 12f65d0e7f9c380f77fe4189d26fdeafd426295b
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090788"
---
# <a name="connect-your-data-from-defender-for-iot-to-azure-sentinel-preview"></a>将数据从用于 IoT 的 Defender 连接到 Azure Sentinel (预览) 

Azure Sentinel 中的 Azure Defender IoT 数据连接器目前为公共预览版。 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/)。

在本指南中，了解如何将你的 Defender for IoT 数据连接到 Azure Sentinel。

> [!div class="checklist"]
> * 先决条件
> * 连接设置
> * Log Analytics 警报视图

从用于 IoT 的 Defender 连接警报并将其直接流式传输到 Azure Sentinel。

通过更严格地将 Azure Defender 与 Azure Sentinel 集成，第一个云本机 SIEM 和第一个具有本机 IoT 和安全性的 SIEM，Microsoft 提供了一种更简单的方法来实现 IT 和工业网络的统一安全性。 与 Azure Sentinel 机器学习结合时，这种集成使组织能够快速检测到经常跨越 IT 和边界的多阶段攻击。 此外，Azure Defender for IoT 与 Azure Sentinel 的安全业务流程、自动化和响应 (之忠诚度) 功能的集成，可使用内置的 

## <a name="prerequisites"></a>先决条件

- 您必须具有工作区 **读取** 和 **写入** 权限。
- 必须在相关 IoT 中心 (s) 上**启用****用于 iot 的 Defender** 。
- 对于要连接的**Azure IoT 中心**，必须同时具有**读取**和**写入**权限。
- 你还必须具有**Azure IoT 中心资源组**的**读取**和**写入**权限。


## <a name="connect-to-defender-for-iot"></a>连接到用于 IoT 的 Defender

1. 在 Azure Sentinel 中，选择 " **数据连接器** "，然后单击 " **用于 IoT 的 Defender** " 磁贴。
1. 在右侧窗格的底部，单击 " **打开连接器" 页面**。
1. 单击要流式传输到 Azure Sentinel 的每个 IoT 中心订阅旁边的 " **连接**"。
    - 如果在该集线器上没有启用 Defender for IoT，你会看到 "启用警告" 消息。 单击 " **启用** " 链接以启动和启用该服务。
1. 可以决定是否希望 IoT 中的警报在 Azure Sentinel 中自动生成事件。 在 " **创建事件**" 下，选择 " **启用** " 以启用规则，以便根据生成的警报自动创建事件。  可以在 "**分析**  >  **活动**规则" 下更改或编辑此规则。

> [!NOTE]
>更改连接后，可能需要10秒钟或更长时间才能刷新中心列表。

## <a name="using-log-analytics-for-alert-display"></a>使用 Log Analytics 进行警报显示

若要在 Log Analytics 中使用相关架构来显示适用于 IoT 警报的 Defender：

1. 打开**Logs**  >  **SecurityInsights**  >  **SecurityAlert**的日志，或搜索**SecurityAlert**。
1. 使用以下 kql 筛选器仅查看 IoT 生成的警报的 Defender：

```kusto
SecurityAlert | where ProductName == "Defender for IoT"
```

### <a name="service-notes"></a>服务说明

连接 IoT 中心后，在 Azure Sentinel 中约有15分钟的中心数据。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将用于 IoT 的 Defender 连接到 Azure Sentinel。 若要了解有关威胁检测和安全数据访问的详细信息，请参阅以下文章：

- 了解如何使用 Azure Sentinel 来了解 [你的数据和潜在威胁](https://docs.microsoft.com/azure/sentinel/quickstart-get-visibility)。

- 了解如何 [访问 IoT 安全数据](how-to-security-data-access.md)
