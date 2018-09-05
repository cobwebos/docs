---
title: 从 Azure 逻辑应用连接到 Twitter | Microsoft Docs
description: 使用 Azure 逻辑应用自动执行任务和工作流，用以从你的 Twitter 帐户监视和管理推文，获取关于关注者、你关注的用户、其他用户、时间线和其他内容的数据
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: eea70d979a69a4855b6eeb892d1705ecadaa8434
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918639"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用监视和管理 Twitter

使用 Azure 逻辑应用和 Twitter 连接器，可以创建用于监视和管理 Twitter 中你关心的数据（例如推文、用户和关注的用户、时间线和其他内容）以及执行其他操作的自动化任务和工作流，例如：

* 监视、发布和搜索推文。
* 获取诸如关注者、关注的用户、时间线之类的数据和其他数据。

可以使用触发器从 Twitter 帐户获取响应，并使输出可用于其他操作。 可以使用通过 Twitter 帐户执行任务的操作。 还可以让其他操作使用来自 Twitter 操作的输出。 例如，当出现具有特定井号标签的新推文时，你可以使用 Slack 连接器发送消息。 如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* Twitter 帐户和用户凭据

   你的凭据授权逻辑应用创建连接并访问你的 Twitter 帐户。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 Twitter 帐户的逻辑应用。 若要使用 Twitter 触发器启动，请[创建空白的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 Twitter 操作，请使用其他触发器（例如**定期**触发器）启动逻辑应用。

## <a name="connect-to-twitter"></a>连接到 Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure门户](https://portal.azure.com)，然后在逻辑应用设计器中打开你的逻辑应用（如果尚未打开）。

1. 选择一个路径： 

   * 对于空白逻辑应用，请在搜索框中输入“twitter”作为筛选器。 
   在触发器列表下，选择所需的触发器。 

     -或-

   * 对于现有逻辑应用： 
   
     * 在要添加操作的最后一个步骤下，选择“新建步骤”。 

       -或-

     * 在要在其中添加操作的两个步骤之间，将鼠标指针移到步骤之间的箭头上。 
     选择出现的加号 (**+**)，然后选择“添加操作”。
     
       在搜索框中，输入“twitter”作为筛选器。 
       在操作列表下，选择所需的操作。

1. 如果提示你登录到 Twitter，请立即登录以便授权你的逻辑应用进行访问。

1. 为所选触发器或操作提供必要的详细信息，并继续构建逻辑应用的工作流。

## <a name="examples"></a>示例

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter 触发器：发布新推文时

当触发器检测到新推文（例如带有井号标签 #Seattle）时，触发器会启动一个逻辑应用工作流。 因此，例如，当发现这些推文时，你可以向存储中添加一个包含推文内容的文件，例如使用 Dropbox 连接器添加到 Dropbox 帐户中。 

（可选）可以包括一个条件来规定合格的推文必须来自至少具有指定数量的关注者的用户。

**企业示例**：可以使用此触发器监视有关你的公司的推文并将推文的内容上传到 SQL 数据库。

### <a name="twitter-action-post-a-tweet"></a>Twitter 操作：发布推文

此操作发布一篇推文，但你可以设置操作，使该推文包含前面所述的触发器发现的推文中的内容。 

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/twitterconnector/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
