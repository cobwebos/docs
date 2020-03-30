---
title: 将 Azure ATP 数据连接到 Azure 哨兵*微软文档
description: 了解如何将 Azure ATP 数据连接到 Azure 哨兵。
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
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 387d04cbbb125006efcc4efc53a02015fe3f5919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588580"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>连接来自 Azure 高级威胁防护 （ATP） 的数据

> [!IMPORTANT]
> Azure 哨兵中的 Azure 高级威胁保护数据连接器当前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

只需单击一下，即可将 Azure[高级威胁保护中的](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp)日志流式传输到 Azure 哨兵中。

## <a name="prerequisites"></a>先决条件

- 具有全局管理员或安全管理员权限的用户
- 您必须是 Azure ATP 的预览客户，并在 Azure ATP 和 Microsoft 云应用安全性之间启用集成。 有关详细信息，请参阅[Azure 高级保护集成](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

## <a name="connect-to-azure-atp"></a>连接到 Azure ATP

确保[在网络上启用](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)Azure ATP 预览版本。
如果部署 Azure ATP 并引入数据，则可疑警报可以轻松地流式传输到 Azure Sentinel 中。 警报可能需要长达 24 小时才能开始流式传输到 Azure Sentinel 中。


1. 要将 Azure ATP 连接到 Azure 哨兵，必须首先启用 Azure ATP 和 Microsoft 云应用安全性之间的集成。 有关如何执行此操作的信息，请参阅[Azure 高级威胁保护集成](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

1. 在 Azure 哨兵中，选择**数据连接器**，然后单击**Azure 高级威胁保护（预览）** 磁贴。

1. 您可以选择是否希望 Azure ATP 中的警报在 Azure Sentinel 中自动生成事件。 在“创建事件”下选择“启用”，******** 以便启用默认的分析规则，这样，当连接的安全服务中生成警报时，就会自动创建事件。 然后，可以在“分析”下的“活动规则”中编辑此规则。********

1. 单击“连接”。

1. 要在日志分析中为 Azure ATP 警报使用相关架构，请搜索**安全警报**。

> [!NOTE]
> 如果警报大于 30 KB，Azure Sentinel 将停止在警报中显示"实体"字段。

## <a name="next-steps"></a>后续步骤
在本文中，您学习了如何将 Azure 高级威胁保护连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

