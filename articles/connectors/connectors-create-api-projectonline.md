---
title: 从 Azure 逻辑应用连接到 Project Online | Microsoft Docs
description: 使用 Azure 逻辑应用自动执行任务和工作流，用以监视、创建和管理 Project Online 项目、任务和资源
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 663363d05c1875d22a0ecc0478abcf7e0ec89c99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105624"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用管理 Project Online 项目、任务和资源

使用 Azure 逻辑应用和 Project Online 连接器，可以通过 Office 365 为 Project Online 中的项目、任务和资源创建自动化任务和工作流。 例如，你的工作流可以执行以下操作和其他操作：

* 监视何时创建了新项目、任务或资源。 或者，监视何时发布了新项目。
* 创建新项目、任务或资源。
* 列出现有的项目或任务。
* 签出、签入或发布项目

Project Online 提供了强大的项目管理功能，可以帮助你规划、管理项目和项目组合投资以及设置其优先级，这几乎可以从任何位置在任何设备上进行。 可以使用 Project Online 触发器从 Project Online 获取响应，并使输出可用于其他操作。 可以使用逻辑应用中的操作在 Project Online 中执行各种任务。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* Project Online，可通过 [Office 365 帐户](https://www.office.com/)获得 

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 Project Online 数据的逻辑应用。 若要使用 Project Online 触发器启动，请[创建空白的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 Project Online 操作，请使用其他触发器（例如**定期**触发器）启动逻辑应用。

## <a name="connect-to-project-online"></a>连接到 Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure门户](https://portal.azure.com)，然后在逻辑应用设计器中打开你的逻辑应用（如果尚未打开）。

1. 选择一个路径： 

   * 对于空白逻辑应用，请在搜索框中输入“Project Online”作为筛选器。 
   在触发器列表下，选择所需的触发器。 

     -或-

   * 对于现有逻辑应用，请在要添加操作的步骤下，选择“新建步骤”  。 在搜索框中输入“Project Online”作为筛选器。 在操作列表下，选择所需的操作。

1. 如果系统提示登录到 Project Online，请立即登录。

   你的凭据授权逻辑应用创建与 Project Online 的连接并访问你的数据。

1. 为所选触发器或操作提供所需的详细信息，然后继续生成逻辑应用的工作流。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/projectonline/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)