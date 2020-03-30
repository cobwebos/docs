---
title: 将微软防御者 ATP 数据连接到 Azure 哨兵*微软文档
description: 了解如何将 Microsoft 防御者高级威胁保护数据连接到 Azure 哨兵。
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
ms.openlocfilehash: ee2c917d02a442ebabc0e1b40bc245982af794f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588206"
---
# <a name="connect-alerts-from-microsoft-defender-advanced-threat-protection"></a>连接来自 Microsoft 防御者高级威胁防护的警报 


> [!IMPORTANT]
> 微软防御者高级威胁保护日志的引入目前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 

只需单击一下，即可将来自[Microsoft 防御者高级威胁防护](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)的警报流式传输到 Azure 哨兵中。 此连接使您能够将来自 Microsoft 防御器高级威胁保护的警报流式传输到 Azure Sentinel 中。 

## <a name="prerequisites"></a>先决条件

- 微软防御者高级威胁保护的有效许可证，如[验证许可预配中所述，并完成为 Microsoft 防御者高级威胁保护设置](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/licensing)。 
- 您必须是 Azure Sentinel 租户上的管理员或安全管理员。


## <a name="connect-to-microsoft-defender-advanced-threat-protection"></a>连接到微软防御者高级威胁保护

如果部署 Microsoft 防御器高级威胁保护并引入您的数据，则警报可以轻松地流式传输到 Azure Sentinel 中。


1. 在 Azure 哨兵中，选择 **"数据连接器**"，单击**Microsoft 防御者高级威胁保护**磁贴并选择 **"打开连接器"页**。
1. 单击“连接”。 
1. 要在日志分析中为防御者 ATP 警报使用相关架构，请搜索**安全警报**，**提供商名称**为**MDATP**。




## <a name="next-steps"></a>后续步骤
在本文档中，您学习了如何将 Microsoft 防御者 ATP 连接到 Azure 哨兵。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats.md)。
