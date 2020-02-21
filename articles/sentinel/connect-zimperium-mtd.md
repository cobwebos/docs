---
title: 将 Zimperium 移动威胁防御连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Zimperium 移动威胁防御连接到 Azure Sentinel。
services: sentinel
author: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: rkarlin
ms.openlocfilehash: a11d4602882973a24e09c62c12a9dc1dcdc8246d
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77501246"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>将 Zimperium 移动威胁防御连接到 Azure Sentinel


> [!IMPORTANT]
> Azure Sentinel 中的 Zimperium 移动威胁防御数据连接器目前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



Zimperium 移动威胁防御连接器使你能够将 Zimperium 威胁日志与 Azure Sentinel 连接起来，以查看仪表板、创建自定义警报和改进调查。 这使你可以更深入地了解组织的移动威胁，并增强安全操作功能。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>配置并连接 Zimperium 移动威胁防御

Zimperium 移动威胁防御可将日志直接集成到**Azure Sentinel**。

1. 在 Azure Sentinel 门户中，单击 "数据连接器"，并选择 " **Zimperium 移动威胁防御**"。
2. 选择 "**打开连接器" 页面**。
3. 按照**Zimperium 移动威胁防御**连接器页面上的说明进行总结，如下所示。
 1. 在 zConsole 中，在导航栏上单击 "**管理**"。
 2. 单击“集成”选项卡。
 3. 单击 "**威胁报告**" 按钮，然后单击 "**添加集成**" 按钮。
 4. 通过从 "可用集成" 中选择**Microsoft Azure Sentinel** ，并输入 "工作区 ID" 和 "主密钥" 来连接到 Azure Sentinel 来创建集成。
 5. 也可以选择将威胁数据推送到 Azure Sentinel 的筛选级别。 

4. 有关其他信息，请参阅[Zimperium 客户支持门户](https://support.zimperium.com)。


## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 "CustomLogs" 下的 "Log Analytics" ZimperiumThreatLog_CL "和" ZimperiumMitigationLog_CL "。

若要在 Zimperium 移动威胁防御 Log Analytics 中使用相关架构，请搜索 ZimperiumThreatLog_CL 和 ZimperiumMitigationLog_CL。


## <a name="validate-connectivity"></a>验证连接

可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Zimperium 移动威胁防御连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。

- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

若要了解有关 Zimperium 的详细信息，请参阅以下内容：

- [Zimperium](https://zimperium.com)

- [Zimperium Mobile Security 博客](https://blog.zimperium.com)

- [Zimperium 客户支持门户](https://support.zimperium.com)

