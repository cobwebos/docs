---
title: 从 Azure 逻辑应用连接到 SharePoint | Microsoft Docs
description: 使用 Azure 逻辑应用自动完成用于在 SharePoint Online 或本地 SharePoint Server 中监视和管理资源的任务与工作流
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e636b2bb08477e6c56c6ae41f08983fc5bfa2a9b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60450737"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>使用 Azure 逻辑应用监视和管理 SharePoint 资源

使用 Azure 逻辑应用和 SharePoint 连接器可以创建自动化的任务和工作流，用于在 SharePoint Online 或本地 SharePoint 服务器中监视和管理文件、文件夹、列表、项、人员等资源，例如：

* 创建、更改或删除文件或项时进行监视。
* 创建、获取、更新或删除项。
* 添加、获取或删除附件。 获取附件中的内容。
* 创建、复制、更新或删除文件。 
* 更新文件属性。 获取文件的内容、元数据或属性。
* 列出或提取文件夹。
* 获取列表或列表视图。
* 设置内容审批状态。
* 解析人员。
* 将 HTTP 请求发送到 SharePoint。
* 获取实体值。

可以使用触发器从 SharePoint 获取响应，并使输出可用于其他操作。 可以使用逻辑应用中的操作在 SharePoint 中执行任务。 还可以让其他操作使用 SharePoint 操作的输出。 例如，如果你定期从 SharePoint 提取文件，可以使用 Slack 连接器向团队发送消息。
如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* SharePoint 站点地址和用户凭据

  你的凭据授权逻辑应用创建连接并访问你的 SharePoint 帐户。 

* 将逻辑应用连接到本地系统（例如 SharePoint 服务器）之前，需要[安装并设置本地数据网关](../logic-apps/logic-apps-gateway-install.md)。 这样，便可以指定在创建逻辑应用的 SharePoint 服务器连接时要使用网关安装。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 SharePoint 帐户的逻辑应用。 若要使用 SharePoint 触发器启动，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用 SharePoint 操作，请使用触发器启动逻辑应用，例如，如果你有 Salesforce 帐户，则可使用 Salesforce 触发器。

  例如，可以使用“创建记录时”Salesforce 触发器来启动逻辑应用。  
  每当在 Salesforce 中创建新记录（例如潜在顾客）时，此触发器都会激发。 
  然后，可以使用 SharePoint 的“创建文件”操作来跟踪此触发器。  这样，在创建新记录时，逻辑应用就会在 SharePoint 中创建一个包含有关该新记录的信息的文件。

## <a name="connect-to-sharepoint"></a>连接到 SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 对于空白逻辑应用，请在搜索框中输入“sharepoint”作为筛选器。 在触发器列表下，选择所需的触发器。 

   -或-

   对于现有逻辑应用，请在要添加 SharePoint 操作的最后一个步骤下，选择“新建步骤”  。 
   在搜索框中，输入“sharepoint”作为筛选器。 
   在操作列表下，选择所需的操作。

   若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 
   选择出现的加号 ( **+** )，然后选择“添加操作”。 

1. 系统提示登录时，请提供所需的连接信息。 如果您使用 SharePoint Server，请确保选择**通过本地数据网关连接**。 完成后，选择“创建”  。

1. 为所选触发器或操作提供所需的详细信息，然后继续生成逻辑应用的工作流。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/sharepoint/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
