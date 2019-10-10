---
title: 将 Microsoft Defender ATP 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Microsoft Defender 高级威胁防护数据连接到 Azure Sentinel。
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
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 19d496ebb61a3ceb47f69f661e30ab529dc64f3d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256742"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>连接来自 Microsoft Defender 高级威胁防护的警报 


> [!IMPORTANT]
> Microsoft Defender 高级威胁防护日志的引入目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 

只需要单击一次即可将警报从[Microsoft Defender 高级威胁防护](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)传输到 Azure Sentinel。 通过此连接，可将警报从 Microsoft Defender 高级威胁防护流式传输到 Azure Sentinel。 

## <a name="prerequisites"></a>先决条件

- 按照[验证授权预配和完成 Microsoft Defender 高级威胁防护设置](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)中所述启用的 Microsoft Defender 高级威胁防护的有效许可证。 
- 你必须是 Azure Sentinel 租户的管理员或安全管理员。


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>连接到 Microsoft Defender 高级威胁防护

如果已部署 Microsoft Defender 高级威胁防护并引入你的数据，则可以轻松地将警报流式传输到 Azure Sentinel。


1. 在 Azure Sentinel 中，选择 "**数据连接器**"，单击 " **Microsoft Defender 高级威胁防护**" 磁贴，然后选择 "**打开连接器" 页面**。
1. 单击“连接”。 
1. 若要在 Log Analytics 中使用有关 Defender ATP 警报的相关架构，请搜索**SecurityAlert** ，**提供程序名称**为**MDATP**。




## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Microsoft Defender ATP 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何了解[你的数据以及潜在的威胁](quickstart-get-visibility.md)。
- 开始[通过 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
