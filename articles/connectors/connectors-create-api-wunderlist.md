---
title: 从 Azure 逻辑应用连接到 Wunderlist | Microsoft Docs
description: 使用 Azure 逻辑应用自动执行任务和工作流，用以监视和管理你的 Wunderlist 帐户中的列表、任务、提醒和其他内容
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: d57ab4a7b655a7d49a7120d358dccc627099e5fd
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050667"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用监视和管理 Wunderlist

使用 Azure 逻辑应用和 Wunderlist 连接器，可以创建用于监视和管理 Wunderlist 帐户中的待办事项列表、任务、提醒和其他内容以及执行其他操作的自动化任务和工作流，例如：

* 监视新任务是何时创建的、任务何时到期或者提醒何时发生。
* 创建和管理列表、说明、任务、子任务和其他内容。
* 设置提醒。
* 获取列表、任务、子任务、提醒、文件、说明、注释和其他内容。

[Wunderlist](https://www.wunderlist.com/) 是一项服务，可以帮助你在任何位置在任何设备上计划、管理和完成项目、待办事项列表以及任务。 可以使用触发器从 Wunderlist 帐户获取响应，并使输出可用于其他操作。 可以使用通过 Wunderlist 帐户执行任务的操作。 还可以让其他操作使用来自 Wunderlist 操作的输出。 例如，当新任务到期时，可以使用 Slack 连接器发布消息。 如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 

* Wunderlist 帐户和用户凭据

   你的凭据授权逻辑应用创建连接并访问你的 Wunderlist 帐户。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 Yammer 帐户的逻辑应用。 若要使用 Wunderlist 触发器启动，请[创建空白的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 Wunderlist 操作，请使用其他触发器（例如**定期**触发器）启动逻辑应用。

## <a name="connect-to-wunderlist"></a>连接到 Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure门户](https://portal.azure.com)，然后在逻辑应用设计器中打开你的逻辑应用（如果尚未打开）。

1. 选择一个路径： 

   * 对于空白逻辑应用，请在搜索框中输入“wunderlist”作为筛选器。 
   在触发器列表下，选择所需的触发器。 

     或

   * 对于现有逻辑应用： 
   
     * 在要添加操作的最后一个步骤下，选择“新建步骤”。 

       或

     * 在要在其中添加操作的两个步骤之间，将鼠标指针移到步骤之间的箭头上。 
     选择出现的加号 ( **+** )，然后选择“添加操作”。
     
       在搜索框中，输入“wunderlist”作为筛选器。 
       在操作列表下，选择所需的操作。

1. 如果提示你登录到 Wunderlist，请立即登录以便你可以允许访问。

1. 为所选触发器或操作提供所需的详细信息，然后继续生成逻辑应用的工作流。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/wunderlist/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)