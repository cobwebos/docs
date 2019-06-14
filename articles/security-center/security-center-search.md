---
title: Azure 安全中心搜索 | Microsoft Docs
description: 了解 Azure 安全中心如何使用 Azure Monitor 日志搜索来检索和分析安全数据。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: 90dbb33fa516d3a831d4e60969ac6b6c8312d539
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64574138"
---
# <a name="azure-security-center-search"></a>Azure 安全中心搜索

> [!NOTE]
> 安全中心搜索仪表板将在 2019 年 7 月 31 日停用。 有关详细信息和备用服务，请参阅用[安全中心功能的停用（2019 年 7 月）](security-center-features-retirement-july2019.md#menu_search)。

Azure 安全中心使用[Azure Monitor 日志搜索](../log-analytics/log-analytics-log-searches.md)来检索和分析安全数据。 Azure Monitor 日志提供用于快速检索和整合数据的查询语言。 从安全中心，您可以利用 Azure Monitor 日志搜索来构造查询并分析收集的数据。

安全中心的免费层和标准层均提供了此搜索功能。  日志搜索提供的数据取决于应用到工作区的层级别。  请参阅安全中心[定价页](../security-center/security-center-pricing.md)了解详细信息。


> [!NOTE]
> 安全中心不会为处于免费层下的工作区保存安全数据。 可以将各种日志发送到处于免费层下的工作区，然后搜索该数据，但搜索结果不会包含来自安全中心的数据。 安全中心仅将数据保存到处于标准层下的工作区。
>
>

## <a name="access-search"></a>访问“搜索”
1. 在“安全中心”主菜单下，选择“搜索”  。

   ![选择“日志搜索”][1]

2. 安全中心将列出 Azure 订阅下的所有工作区。 选择工作区。 （如果只有一个工作区，将不会出现此工作区选择器。）

   ![选择工作区][2]

3. “日志搜索”  将打开。 若要在所选工作区下查询更多数据，请输入以下查询示例：

   SecurityEvent | where EventID == 4625 | summarize count() by TargetAccount

   结果显示了失败登录 （事件 4625） 的所有帐户。

   ![搜索结果][3]

请参阅[Kusto 查询语言](../log-analytics/log-analytics-search-reference.md)有关如何对所选工作区下查询数据的详细信息。

## <a name="next-steps"></a>后续步骤
在本文中，你已了解如何在安全中心中访问搜索功能。 安全中心使用 Azure Monitor 日志搜索。 若要了解有关 Azure Monitor 日志搜索的详细信息，请参阅：

- [什么是 Azure Monitor 日志？](../log-analytics/log-analytics-overview.md) – 在 Azure Monitor 日志概述
- [了解日志搜索中 Azure Monitor 日志](../log-analytics/log-analytics-log-search-new.md)-介绍如何在 Azure Monitor 日志中使用日志搜索，并提供创建日志搜索之前应了解的概念
- [在 Azure Monitor 日志中使用日志搜索查找数据](../log-analytics/log-analytics-log-searches.md)– 有关使用日志搜索的教程
- [Kusto 搜索引用](../log-analytics/log-analytics-search-reference.md)– 介绍了 Azure Monitor 日志中的查询语言

若要了解有关安全中心的详细信息，请参阅：

- [安全中心概述](security-center-intro.md) – 介绍安全中心的主要功能

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
