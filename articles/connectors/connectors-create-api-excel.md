---
title: 连接到 Excel Onlin - Azure 逻辑应用 | Microsoft Docs
description: 使用 Excel Online REST API 和 Azure 逻辑应用管理数据
ms.service: logic-apps
services: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.topic: article
ms.date: 08/23/2018
ms.openlocfilehash: 917b9abd4a32b7951313c5555f4111dff990078c
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230945"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>使用 Azure 逻辑应用管理 Excel Online 数据

使用 Azure 逻辑应用和 Excel Online 连接器，可以基于 Excel Online for Business 或 Excel Online for OneDrive 中的数据创建自动化任务和工作流。 此连接器提供了可以帮助你处理数据以及管理电子表格的操作，例如： 

* 创建新的工作表和表。
* 获取和管理工作表、表和行。
* 添加单个行和键列。

然后，你可以将来自这些操作的输出用于其他服务的操作。 例如，如果你使用一个每周创建工作表的操作，则可以使用另一个操作通过 Office 365 Outlook 连接器发送确认电子邮件。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> [Excel Online for Business](/connectors/excelonlinebusiness/) 和 [Excel Online for OneDrive](/connectors/excelonline/) 连接器可以与 Azure 逻辑应用一起工作，不同于[适用于 PowerApps 的 Excel 连接器](/connectors/excel/)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* 用于你的工作帐户或个人 Microsoft 帐户的一个 [Office 365 帐户](https://www.office.com/) 

  你的 Excel 数据可以作为以逗号分隔的值 (CSV) 文件存在于存储文件夹中，例如在 OneDrive 中。 
  还可以将同一 CSV 文件与[平面文件连接器](../logic-apps/logic-apps-enterprise-integration-flatfile.md)结合使用。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 Excel Online 数据的逻辑应用。 此连接器仅提供操作，因此，若要启动逻辑应用，请选择单独的触发器（例如“重复”触发器）。

## <a name="add-excel-action"></a>添加 Excel 操作

1. 在 [Azure 门户](https://portal.azure.com)中，在逻辑应用设计器中打开你的逻辑应用（如果尚未打开）。

1. 在触发器下，选择“新建步骤”。

1. 在搜索框中，输入“excel”作为筛选器。 在操作列表下，选择所需的操作。

1. 如果系统提示登录到 Office 365 帐户，请选择“登录”。 

   你的凭据授权逻辑应用创建与 Excel Online 的连接并访问你的数据。

1. 继续为所选操作提供必要的详细信息并构建逻辑应用的工作流。

## <a name="connector-reference"></a>连接器参考

有关技术详细信息（例如操作和限制，如连接器的 Swagger 文件所述），请查看以下连接器参考页：

* [Excel Online for Business](/connectors/excelonlinebusiness/) 
* [Excel Online for OneDrive](/connectors/excelonline/) 

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
