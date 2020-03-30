---
title: 将 Zimperium 移动威胁防御连接到 Azure 哨兵*微软文档
description: 了解如何将 Zimperium 移动威胁防御连接到 Azure 哨兵。
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 86854fa22a49f09e5d3d2fc5fdb53c245850fbac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587934"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>将 Zimperium 移动威胁防御连接到 Azure 哨兵


> [!IMPORTANT]
> Azure Sentinel 中的 Zimperium 移动威胁防御数据连接器当前处于公共预览版中。
> 此功能在没有服务级别协议的情况下提供，不建议用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



Zimperium 移动威胁防御连接器使您能够将 Zimperium 威胁日志与 Azure Sentinel 连接，以查看仪表板、创建自定义警报并改进调查。 这使您可以更深入地了解组织的移动威胁环境，并增强您的安全操作功能。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置中。

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>配置和连接 Zimperium 移动威胁防御

Zimperium 移动威胁防御可以直接集成日志并将其导出到**Azure Sentinel。**

1. 在 Azure Sentinel 门户中，单击"数据连接器"并选择 **"Zimperium 移动威胁防御**"。
2. 选择 **"打开连接器"页**。
3. 按照**Zimperium 移动威胁防御**连接器页面上的说明进行操作，摘要如下。
 1. 在 zConsole 中，单击导航栏上的 **"管理**"。
 2. 单击“集成”选项卡。****
 3. 单击**威胁报告**按钮，然后单击 **"添加集成"** 按钮。
 4. 通过从可用集成中选择**Microsoft Azure 哨兵**来创建集成，并输入工作区 ID 和主键以连接到 Azure Sentinel。
 5. 也可以选择用于将威胁数据推送到 Azure Sentinel 的筛选器级别的选项。 

4. 有关详细信息，请参阅[Zimperium 客户支持门户](https://support.zimperium.com)。


## <a name="find-your-data"></a>查找您的数据

成功建立连接后，数据将显示在自定义日志ZimperiumThreatLog_CL和ZimperiumMitigationLog_CL下的日志分析中。

要在日志分析中使用相关架构进行 Zimperium 移动威胁防御，请搜索ZimperiumThreatLog_CL并ZimperiumMitigationLog_CL。


## <a name="validate-connectivity"></a>验证连接

可能需要 20 分钟以上，直到日志开始出现在日志分析中。

## <a name="next-steps"></a>后续步骤

在本文中，您学习了如何将 Zimperium 移动威胁防御连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。

- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。

- [使用工作簿](tutorial-monitor-your-data.md)监视数据。

要了解有关 Zimperium 的更多，请参阅以下内容：

- [Zimperium](https://zimperium.com)

- [Zimperium 移动安全博客](https://blog.zimperium.com)

- [Zimperium 客户支持门户](https://support.zimperium.com)

