---
title: 将 Microsoft Defender ATP 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Microsoft Defender 高级威胁防护数据连接到 Azure Sentinel。
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
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 831baabcc26130d9b2c730afd85f2ed4b3842690
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80756355"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>连接来自 Microsoft Defender 高级威胁防护的警报 


> [!IMPORTANT]
> 引入 Microsoft Defender 高级威胁防护警报目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 

利用[Microsoft Defender 高级威胁防护](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)连接器，你可以将 Microsoft Defender 高级威胁防护中的警报流式传输到 Azure Sentinel。 这样，你就可以更全面地分析组织中的安全事件，并构建行动手册以实现有效且即时的响应。

## <a name="prerequisites"></a>必备条件

- 你必须具有 Microsoft Defender 高级威胁防护的有效许可证，如[设置 Microsoft DEFENDER ATP 部署](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)中所述。 
- 你必须是 Azure Sentinel 租户的管理员或安全管理员。


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>连接到 Microsoft Defender 高级威胁防护

如果已部署 Microsoft Defender 高级威胁防护并引入你的数据，则可以轻松地将警报流式传输到 Azure Sentinel。


1. 在 Azure Sentinel 中，选择 "**数据连接器**"，单击 " **Microsoft Defender 高级威胁防护**" 磁贴，然后选择 "**打开连接器" 页面**。
1. 单击“连接”  。 
1. 若要在 Log Analytics 中使用有关 Defender ATP 警报的相关架构，请搜索**SecurityAlert** ，**提供程序名称**为**MDATP**。




## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Microsoft Defender ATP 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
