---
title: 从 Azure 逻辑应用连接到 Salesforce | Microsoft Docs
description: 使用 Azure 逻辑应用自动执行监视、创建和管理 Salesforce 记录和作业的任务和工作流
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 292d517f2c99974f4674a4c94472a0a320320ce4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106009"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用监视、创建和管理 Salesforce 资源

借助 Azure 逻辑应用和 Salesforce 连接器，可以为 Salesforce 资源（如记录、作业和对象）创建自动化任务和工作流，例如：

* 监视何时创建或更改记录。 
* 创建、获取和管理作业和记录，包括插入、更新和删除操作。

可以使用 Salesforce 触发器从 Salesforce 获取响应，并使输出可用于其他操作。 可以使用逻辑应用中的操作来执行 Salesforce 资源的任务。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* 一个 [Salesforce 帐户](https://salesforce.com/)

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 Salesforce 帐户的逻辑应用。 若要从 Salesforce 触发器开始，请[创建空白的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 Salesforce 操作，请使用其他触发器（例如**定期**触发器）启动逻辑应用。

## <a name="connect-to-salesforce"></a>连接到 Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录 [Azure门户](https://portal.azure.com)，然后在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 选择路径： 

   * 对于空白逻辑应用，请在搜索框中输入“salesforce”作为筛选器。 
   在触发器列表下，选择所需的触发器。 

     -或-

   * 对于现有逻辑应用，请在要添加操作的步骤下，选择“新建步骤”  。 在搜索框中，输入“salesforce”作为筛选器。 在操作列表下，选择所需的操作。

1. 如果系统提示你登录 Salesforce，请立即登录并允许访问。

   你的凭据授权逻辑应用创建与 Salesforce 的连接并访问你的数据。

1. 为所选触发器或操作提供所需的详细信息，然后继续生成逻辑应用的工作流。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/salesforce/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)