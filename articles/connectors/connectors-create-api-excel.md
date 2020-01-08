---
title: 管理 Excel Online 中的数据、工作表和表
description: 使用 Azure 逻辑应用在 Excel Online for business 或 Excel Online 中管理工作表和表中的数据
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 08/23/2018
tags: connectors
ms.openlocfilehash: b1df1219590aa94331a4404b1315eb7231c59699
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445880"
---
# <a name="manage-excel-online-data-with-azure-logic-apps"></a>使用 Azure 逻辑应用管理 Excel Online 数据

通过[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[Excel online](/connectors/excelonlinebusiness/) For Business 连接器或[excel online for onedrive](/connectors/excelonline/)连接器，你可以基于 Excel online for business 或 onedrive 中的数据创建自动任务和工作流。 此连接器提供了可以帮助你处理数据以及管理电子表格的操作，例如：

* 创建新的工作表和表。
* 获取和管理工作表、表和行。
* 添加单个行和键列。

然后，你可以将来自这些操作的输出用于其他服务的操作。 例如，如果你使用一个每周创建工作表的操作，则可以使用另一个操作通过 Office 365 Outlook 连接器发送确认电子邮件。

如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

> [!NOTE]
> [Excel Online for Business](/connectors/excelonlinebusiness/) 和 [Excel Online for OneDrive](/connectors/excelonline/) 连接器可以与 Azure 逻辑应用一起工作，不同于[适用于 PowerApps 的 Excel 连接器](/connectors/excel/)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 用于你的工作帐户或个人 Microsoft 帐户的一个 [Office 365 帐户](https://www.office.com/)

  你的 Excel 数据可以作为以逗号分隔的值 (CSV) 文件存在于存储文件夹中，例如在 OneDrive 中。 
  还可以将同一 CSV 文件与[平面文件连接器](../logic-apps/logic-apps-enterprise-integration-flatfile.md)结合使用。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中访问 Excel Online 数据的逻辑应用。 此连接器仅提供操作，因此，若要启动逻辑应用，请选择单独的触发器（例如“重复”触发器）。

## <a name="add-excel-action"></a>添加 Excel 操作

1. 在 [Azure 门户](https://portal.azure.com)中，在逻辑应用设计器中打开你的逻辑应用（如果尚未打开）。

1. 在触发器下，选择“新建步骤”。

1. 在搜索框中，输入“excel”作为筛选器。 在操作列表下，选择所需的操作。

   > [!NOTE]
   > 逻辑应用设计器无法加载包含100列或更多列的表。 如果可能，减少所选表中的列数，使设计器可以加载表。

1. 如果系统提示，请登录到 Office 365 帐户。

   你的凭据授权逻辑应用创建与 Excel Online 的连接并访问你的数据。

1. 继续为所选操作提供必要的详细信息并构建逻辑应用的工作流。

## <a name="connector-reference"></a>连接器参考

如连接器的 OpenAPI （以前的 Swagger）文件所述的技术详细信息（如触发器、操作和限制），请参阅以下连接器参考页面：

* [Excel Online for Business](/connectors/excelonlinebusiness/)
* [Excel Online for OneDrive](/connectors/excelonline/)

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
