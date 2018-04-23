---
title: Power BI 工作区集合中的新增功能
description: 获取有关 Power BI 工作区集合中的新增功能的最新信息
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 647b571fc05dca12ab9d4db45236962e4794ef4b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="whats-new-in-power-bi-workspace-collections"></a>Power BI 工作区集合中的新增功能

定期发布 **Power BI 工作区集合**更新。 但是，并非每个版本都包括面向用户的新功能；某些版本聚焦于后端的服务功能。 此处将重点介绍面向用户的新功能。

> [!IMPORTANT]
> Power BI 工作区集合已弃用，到 2018 年 6 月或合同指示时可用。 建议你规划到 Power BI Embedded 的迁移以避免应用程序中断。 有关如何将数据迁移到 Power BI Embedded 的信息，请参阅[如何将 Power BI 工作区集合内容迁移到 Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)。

## <a name="march-2017"></a>2017 年 3 月

**自助服务功能**

* [新建报表](create-report-from-dataset.md)
* [报表另存为](save-reports.md)
* 在“读取/编辑/新建”模式下嵌入报表 
* [在“编辑/读取”模式之间切换报表](toggle-mode.md)

**使用 REST API 进行数据连接**

* [创建数据集](https://msdn.microsoft.com/library/azure/mt778875.aspx)
* 推送数据 

**管理 API**

* 克隆报表和数据集
* 将报表绑定到不同数据集

**示例**

* 更新的 [JavaScript 报表嵌入示例](https://microsoft.github.io/PowerBI-JavaScript/demo)

## <a name="december-2016"></a>2016 年 12 月

* [新的 JavaScript 嵌入式示例](https://microsoft.github.io/PowerBI-JavaScript/demo/)

## <a name="october-2016"></a>2016 年 10 月

* [使用 Power BI 工作区集合和 R 进行的高级分析](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31-2016"></a>2016 年 8 月 31 日
此版本中包括的新增功能：

* 支持[高级筛选和页面导航](interact-with-reports.md)的全新 JavaScript SDK。
* 加拿大中央数据中心现在已支持 Power BI 工作区集合。 检查[数据中心状态](https://azure.microsoft.com/status/)。

## <a name="july-11-2016"></a>2016 年 7 月 11 日
此版本中包括的新增功能：

* **好消息！** Power BI 工作区集合服务现在不再是预览版 - 它现在已是 GA 版（公开发布）。  
* 所有 REST API 都已从 **/beta** 迁移到 **/v1.0**。
* .NET 和 JavaScript SDK 已更新为 **v1.0**。
* 现在可以通过使用 API 密钥直接对 Power BI API 调用进行身份验证。 只有对于嵌入请求，才需要使用应用令牌。 作为此更新的一部分，预配和开发令牌在 v1.0 API 中已被弃用，但它们在 beta 版本中将继续工作到 2016 年 12 月 30 日。 若要了解详细信息，请参阅[通过 Power BI 工作区集合进行身份验证和授权](app-token-flow.md)。
* 对应用令牌和嵌入的报表的行级别安全性 (RLS) 支持。 若要了解详细信息，请参阅[将行级别安全性用于 Power BI 工作区集合](row-level-security.md)。
* 为所有 **v1.0** API 调用更新了示例应用程序。
* 提供了对 Azure SDK、PowerShell 和 CLI 的 Power BI 工作区集合支持。
* 用户可以将可视化数据导出到 **.csv** 文件。
* 现在，与 Microsoft Azure 相同的所有语言/区域设置都支持 Power BI 工作区集合。 若要了解详细信息，请参阅 [Azure - 语言](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx)。

