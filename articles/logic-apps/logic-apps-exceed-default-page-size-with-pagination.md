---
title: 通过分页获取更多项或记录-Azure 逻辑应用
description: 设置对 Azure 逻辑应用中的连接器操作的默认页面大小限制的分页
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: e86600312490c77ed492cb28a359add0fed90596
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679899"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用分页获取更多数据、项或记录

使用[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)中的连接器操作检索数据、项或记录时，可能会获得结果集，因此操作不会同时返回所有结果。 对于某些操作，结果数可能会超出连接器的默认页面大小。 在这种情况下，操作仅会返回结果的第一页。 例如，SQL Server 连接器的 "**获取行**" 操作的默认页面大小为2048，但可能因其他设置而异。

某些操作使您可以打开*分页*设置，以便逻辑应用可以检索更多结果，直到分页限制，但在操作完成时，将这些结果作为单个消息返回。 使用分页时，必须指定*阈值*，它是希望操作返回的结果的目标数目。 操作将检索结果，直到达到指定的阈值。 当项的总数小于指定的阈值时，操作将检索所有结果。

打开分页设置将基于连接器的页面大小检索结果页。 此行为意味着，有时可能会获得比指定阈值更多的结果。 例如，使用 SQL Server**获取行**"操作时，该操作支持分页设置：

* 操作的默认页面大小为每页2048条记录。
* 假设有10000条记录，并指定5000记录作为最小值。
* 分页获取记录页面，因此至少要获取指定的最小值，操作将返回6144记录（3页 x 2048 记录），而不是5000记录。

下面列出了一些连接器，你可以在其中超过特定操作的默认页面大小：

* [Azure Blob 存储](https://docs.microsoft.com/connectors/azureblob/)
* [Dynamics 365](https://docs.microsoft.com/connectors/dynamicscrmonline/)
* [Excel](https://docs.microsoft.com/connectors/excel/)
* [HTTP](https://docs.microsoft.com/azure/connectors/connectors-native-http)
* [IBM DB2](https://docs.microsoft.com/connectors/db2/)
* [Microsoft Teams](https://docs.microsoft.com/connectors/teams/)
* [Oracle Database](https://docs.microsoft.com/connectors/oracle/)
* [Salesforce](https://docs.microsoft.com/connectors/salesforce/)
* [SharePoint](https://docs.microsoft.com/connectors/sharepointonline/)
* [SQL Server](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 要启用分页的逻辑应用和操作。 如果没有逻辑应用，请参阅[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="turn-on-pagination"></a>启用分页

若要确定某个操作是否支持逻辑应用设计器中的分页，请检查该操作的设置以查看**分页**设置。 此示例演示如何在 SQL Server 的 "**获取行**" 操作中打开分页。

1. 在操作的右上角，选择省略号（ **...** ）按钮，然后选择 "**设置**"。

   ![打开操作的设置](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   如果操作支持分页，则操作将显示**分页**设置。

1. 将**分页**设置从**Off**更改为**On**。 在 "**阈值**" 属性中，为希望操作返回的结果的目标数指定一个整数值。

   ![指定要返回的结果的最小数目](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. 准备就绪后，选择“完成”。

## <a name="workflow-definition---pagination"></a>工作流定义-分页

为支持此功能的操作启用分页时，逻辑应用的工作流定义将包含 `"paginationPolicy"` 属性以及该操作的 `"runtimeConfiguration"` 属性中的 `"minimumItemCount"` 属性，例如：

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

有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
