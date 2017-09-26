---
title: "Power BI Embedded 中的新增功能"
description: "获取 Power BI Embedded 中的新增功能的最新信息"
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.assetid: 2794ae98-b9a7-45df-b6e1-962a395b91fa
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 03/11/2017
ms.author: asaxton
ms.translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 07c53a5d6b1881a4c207a2aefed9fcede0fa069e
ms.contentlocale: zh-cn
ms.lasthandoff: 03/14/2017

---
# <a name="whats-new-in-power-bi-embedded"></a>Power BI Embedded 中的新增功能

定期发布 **Power BI Embedded** 更新。 但是，并非每个版本都包括面向用户的新功能；某些版本聚焦于后端的服务功能。 此处将重点介绍面向用户的新功能。 请务必经常查看。

## <a name="march-2017"></a>2017 年 3 月

<iframe width="640" height="360" src="https://www.youtube.com/embed/ibuN4DzCl5c?showinfo=0" frameborder="0" allowfullscreen></iframe>

**自助服务功能**

* [新建报表](power-bi-embedded-create-report-from-dataset.md)
* [报表另存为](power-bi-embedded-save-reports.md)
* 在“读取/编辑/新建”模式下嵌入报表 
* [在“编辑/读取”模式之间切换报表](power-bi-embedded-toggle-mode.md)

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

* [使用 Power BI Embedded 和 R 的高级分析](https://powerbi.microsoft.com/blog/r-in-pbie/)

## <a name="august-31st-2016"></a>2016 年 8 月 31日
此版本中包括的新增功能：

* 支持[高级筛选和页面导航](power-bi-embedded-interact-with-reports.md)的全新 JavaScript SDK。
* 加拿大中央数据中心现在已支持 Power BI Embedded。 检查[数据中心状态](https://azure.microsoft.com/status/)。

## <a name="july-11th-2016"></a>2016 年 7 月 11日
此版本中包括的新增功能：

* **好消息！** Power BI Embedded 服务现在不再是预览版 - 它现在已是 GA 版（公开发布了）。  
* 所有 REST API 都已从 **/beta** 迁移到 **/v1.0**。
* .NET 和 JavaScript SDK 已更新为 **v1.0**。
* 现在可以通过使用 API 密钥直接对 Power BI API 调用进行身份验证。 只有对于嵌入请求，才需要使用应用令牌。 作为此更新的一部分，预配和开发令牌在 v1.0 API 中已被弃用，但它们在 beta 版本中将继续工作到 2016 年 12 月 30 日。 若要了解详细信息，请参阅[通过 Power BI Embedded 进行身份验证和授权](power-bi-embedded-app-token-flow.md)。
* 对应用令牌和嵌入的报表的行级别安全性 (RLS) 支持。 若要了解详细信息，请参阅 [Power BI Embedded 中的行级别安全性](power-bi-embedded-rls.md)。
* 为所有 **v1.0** API 调用更新了示例应用程序。
* 提供了对 Azure SDK、PowerShell 和 CLI 的 Power BI Embedded 支持。
* 用户可以将可视化数据导出到 **.csv** 文件。
* 现在，与 Microsoft Azure 相同的所有语言/区域设置都支持 Power BI Embedded。 若要了解详细信息，请参阅 [Azure - 语言](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx)。


