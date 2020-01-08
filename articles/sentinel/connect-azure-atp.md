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
ms.date: 12/30/2019
ms.author: rkarlin
ms.openlocfilehash: 6f099a0eb6501b6576c4560e0b397d3105bb15f0
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610703"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>连接来自 Azure 高级威胁防护（ATP）的数据

> [!IMPORTANT]
> Azure Sentinel 中的 Azure 高级威胁防护数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

只需要单击一次即可将日志从[Azure 高级威胁防护](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp)流式传输到 azure Sentinel。

## <a name="prerequisites"></a>必备组件

- 具有全局管理员或安全管理员权限的用户
- 你必须是 Azure ATP 的预览客户，并支持 Azure ATP 与 Microsoft Cloud App Security 之间的集成。 有关详细信息，请参阅[Azure 高级保护集成](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

## <a name="connect-to-azure-atp"></a>连接到 Azure ATP

请确保[网络上已启用](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Azure ATP 预览版本。
如果部署了 Azure ATP 并引入数据，则可以轻松地将可疑警报流式传输到 Azure Sentinel。 可能需要长达24小时的时间才能开始向 Azure Sentinel 流式传输。


1. 若要将 Azure ATP 连接到 Azure Sentinel，必须先在 Azure ATP 与 Microsoft Cloud App Security 之间启用集成。 有关如何执行此操作的信息，请参阅[Azure 高级威胁防护集成](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

1. 在 Azure Sentinel 中，选择 "**数据连接器**"，然后单击 " **Azure 高级威胁防护（预览版）** " 磁贴。

1. 你可以选择是否希望 Azure ATP 中的警报自动在 Azure Sentinel 中自动生成事件。 在“创建事件”下选择“启用”，以便启用默认的分析规则，这样，当连接的安全服务中生成警报时，就会自动创建事件。 然后，可以在“分析”下的“活动规则”中编辑此规则。

1. 单击“连接”。

1. 若要在 Azure ATP 警报 Log Analytics 中使用相关架构，请搜索**SecurityAlert**。

> [!NOTE]
> 如果警报大于 30 KB，Azure Sentinel 将停止显示警报中的 "实体" 字段。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Azure 高级威胁防护连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

