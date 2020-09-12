---
title: 将 Microsoft Defender for Identity (以前的 Azure ATP) 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何在 Microsoft Defender 中将日志流式传输 (以前的 Azure 高级威胁防护，只需要单击一次即可将 ATP) 到 Azure Sentinel 中 () 。
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
ms.openlocfilehash: 64c1a7155d0cc4e80f97db138a0626d6e9fdc9e9
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89657811"
---
# <a name="connect-data-from-microsoft-defender-for-identity-formerly-azure-advanced-threat-protection"></a>将 Azure 高级威胁防护 (的数据从 Microsoft Defender 连接到身份验证) 

> [!IMPORTANT]
> Azure Sentinel 中的 Microsoft Defender for Identity 数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

只需要单击一次，即可将 [Microsoft Defender For Identity 的](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) 日志流式传输到 Azure Sentinel。

## <a name="prerequisites"></a>先决条件

- 具有全局管理员或安全管理员权限的用户
- 你必须是 Microsoft Defender for Identity 的预览版客户，并在 Microsoft Defender for Identity and Microsoft Cloud App Security 之间实现集成。 有关详细信息，请参阅 [用于标识集成的 Microsoft Defender](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

## <a name="connect-to-microsoft-defender-for-identity"></a>连接到 Microsoft Defender for Identity

请确保在 [你的网络上启用](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1)了 Microsoft Defender 身份预览版本。
如果已部署 Microsoft Defender for Identity 并引入你的数据，则可以轻松地将可疑警报流式传输到 Azure Sentinel。 可能需要长达24小时的时间才能开始向 Azure Sentinel 流式传输。


1. 若要将 Microsoft Defender for Identity 连接到 Azure Sentinel，你必须首先在 Microsoft Defender 之间启用用于标识和 Microsoft Cloud App Security 的集成。 有关如何执行此操作的信息，请参阅 [用于标识集成的 Microsoft Defender](https://docs.microsoft.com/cloud-app-security/aatp-integration)。

1. 在 Azure Sentinel 中，选择 " **数据连接器** "，然后单击 " **Microsoft Defender for Identity" (预览版) ** "磁贴。

1. 你可以选择是否想要自动在 Azure Sentinel 中自动生成事件。 在“创建事件”下选择“启用”， 以便启用默认的分析规则，这样，当连接的安全服务中生成警报时，就会自动创建事件。 然后，可以在“分析”下的“活动规则”中编辑此规则。 

1. 单击“连接”  。

1. 若要在 Microsoft Defender for Identity 警报 Log Analytics 中使用相关架构，请搜索 **SecurityAlert**。

> [!NOTE]
> 如果警报大于 30 KB，Azure Sentinel 将停止显示警报中的 "实体" 字段。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Microsoft Defender for Identity 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

