---
title: 通过分页获取更多项或记录
description: 在 Azure 逻辑应用中设置分页以超出连接器操作的默认页面大小限制
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: d46bf711a46e27b81a1284b5fc55cf403b7da048
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87090257"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>通过在 Azure 逻辑应用中使用分页获取更多数据、项或记录

在 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)中使用连接器操作检索数据、项或记录时，得到的结果集可能很大，以致操作无法同时返回所有结果。 某些操作的结果数可能会超出连接器的默认页面大小。 在这种情况下，操作仅会返回结果的第一页。 例如，SQL Server 连接器的“获取行”操作的默认页面大小为 2048，但是可能因其他设置而有所不同。

某些操作允许启用分页设置，使逻辑应用能够检索更多的结果（最多不超过分页限制），但在操作完成时会以单个消息的形式返回这些结果。 使用分页时，必须指定阈值，即要让操作返回的目标结果数。 在达到指定的阈值之前，操作会不断地检索结果。 如果总项数小于指定的阈值，操作将检索所有结果。

启用分页设置会根据连接器的页面大小检索结果页面。 此行为意味着，获取的结果数有时会超过指定的阈值。 例如，使用 SQL Server 的支持分页设置的“获取行”操作时：

* 该操作的默认页面大小为每页 2048 条记录。
* 假设你有 10,000 条记录，并指定 5000 条记录作为最小值。
* 分页功能将获取记录页面，因此，为了至少获取指定的最小数目，操作将返回 6144 条记录（3 个页面 x 2048 条记录），而不是 5000 条记录。

下面仅仅列出了其特定操作可能会超出默认页面大小的一部分连接器：

* [Azure Blob 存储](/connectors/azureblob/)
* [Dynamics 365](/connectors/dynamicscrmonline/)
* [Excel](/connectors/excel/)
* [HTTP](../connectors/connectors-native-http.md)
* [IBM DB2](/connectors/db2/)
* [Microsoft Teams](/connectors/teams/)
* [Oracle Database](/connectors/oracle/)
* [Salesforce](/connectors/salesforce/)
* [SharePoint](/connectors/sharepointonline/)
* [SQL Server](/connectors/sql/)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要为其启用分页的逻辑应用和操作。 如果你没有逻辑应用，请参阅[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="turn-on-pagination"></a>启用分页

若要在逻辑应用设计器中确定某个操作是否支持分页，请在“分页”设置中检查该操作的设置。 此示例展示了如何在 SQL Server 的“获取行”操作中启用分页。

1. 在该操作的右上角选择省略号 ( **...** ) 按钮，然后选择“设置”。

   ![打开操作的设置](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   如果该操作支持分页，它会显示“分页”设置。

1. 将“分页”设置从“关”更改为“开”。   在“阈值”属性中，指定一个整数值，作为你希望操作返回的目标结果数。

   ![指定要返回的最小结果数](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. 准备就绪后，选择“完成”。

## <a name="workflow-definition---pagination"></a>工作流定义 - 分页

为支持分页功能的操作启用分页时，逻辑应用的工作流定义会在该操作的 `"runtimeConfiguration"` 属性中包含 `"paginationPolicy"` 属性以及 `"minimumItemCount"` 属性，例如：

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.testuri.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "paginationPolicy": {
            "minimumItemCount": 1000
         }
      },
      "type": "Http"
   }
},
```

## <a name="get-support"></a>获取支持

如有问题，请访问[有关 Azure 逻辑应用的 Microsoft 问答页](/answers/topics/azure-logic-apps.html)。
