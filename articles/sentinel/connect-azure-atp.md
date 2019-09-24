---
title: 将 Azure ATP 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Azure ATP 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: ebb727055296ba7886a9307ada113ab5a6e0c9e0
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240193"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp---preview"></a>连接 Azure 高级威胁防护（ATP）的数据-预览




只需要单击一次即可将日志从[Azure 高级威胁防护](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp)流式传输到 azure Sentinel。

## <a name="prerequisites"></a>先决条件

- 具有全局管理员或安全管理员权限的用户
- 你必须是 Azure ATP 的预览版客户

## <a name="connect-to-azure-atp"></a>连接到 Azure ATP

请确保[网络上已启用](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Azure ATP 预览版本。
如果部署了 Azure ATP 并引入数据，则可以轻松地将可疑警报流式传输到 Azure Sentinel。 可能需要长达24小时的时间才能开始向 Azure Sentinel 流式传输。


1. 若要将 Azure ATP 连接到 Azure Sentinel，必须先在 Azure ATP 与 Microsoft Cloud App Security 之间启用集成。 有关如何执行此操作的信息，请参阅[Azure 高级威胁防护集成](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

1. 在 Azure Sentinel 中，选择 "**数据连接器**"，然后单击 " **Azure ATP** " 磁贴。

1. 你可以选择是否希望 Azure ATP 中的警报自动在 Azure Sentinel 中自动生成事件。 在 "**创建事件**" 下，选择 "**启用**" 以启用从连接的安全服务中生成的警报自动创建事件的默认分析规则。 然后，你可以在 "**分析**" 和 "**活动规则**" 下编辑此规则。

1. 单击“连接”。

1. 若要在 Azure ATP 警报 Log Analytics 中使用相关架构，请搜索**SecurityAlert**。

> [!NOTE]
> 如果警报大于 30 KB，Azure Sentinel 将停止显示警报中的 "实体" 字段。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure 高级威胁防护连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

