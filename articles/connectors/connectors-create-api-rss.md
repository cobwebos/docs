---
title: 从 Azure 逻辑应用连接到 RSS 源 | Microsoft Docs
description: 使用 Azure 逻辑应用自动执行监视和管理 RSS 源的任务和工作流
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 01573871700bbeeb653ce3efdbf6c6aca88fd454
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104866"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用管理 RSS 源

使用 Azure 逻辑应用和 RSS 连接器，可以为任何 RSS 源创建自动化任务和工作流，例如：

* 监视何时发布 RSS 源项。
* 列出所有 RSS 源项。

RSS（极具特色的网站摘要），也称为“真正简单的整合”，是一种流行的 Web 联合格式，用于发布经常更新的内容，例如博客文章和新闻标题。 许多内容发布者都提供 RSS 源，以便用户可以订阅该内容。 

可以使用 RSS 触发器从 RSS 源获取响应，并使输出可用于其他操作。 可以在逻辑应用中使用 RSS 操作来执行 RSS 源的任务。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* RSS 源的 URL

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 RSS 源的逻辑应用。 若要从 RSS 触发器开始，请[创建空白的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 RSS 操作，请使用其他触发器（例如**定期**触发器）启动逻辑应用。

## <a name="connect-to-an-rss-feed"></a>连接到 RSS 源

1. 登录 [Azure门户](https://portal.azure.com)，然后在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 选择路径： 

   * 对于空白逻辑应用，请在搜索框中输入“rss”作为筛选器。 在触发器列表下，选择所需的触发器。 

     -或-

   * 对于现有逻辑应用，请在要添加操作的步骤下，选择“新建步骤”  。 在搜索框中，输入“rss”作为筛选器。 在操作列表下，选择所需的操作。

1. 为所选触发器或操作提供所需的详细信息，然后继续生成逻辑应用的工作流。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/rss/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)