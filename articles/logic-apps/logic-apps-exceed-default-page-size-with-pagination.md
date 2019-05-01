---
title: 获取更多数据，项或记录分页-Azure 逻辑应用
description: 将分页设置为超过 Azure 逻辑应用中的连接器操作的默认页大小限制
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 04/11/2019
ms.openlocfilehash: 2d1bcf2cf83fab106f79120c3caacc424f839836
ms.sourcegitcommit: daf6538427ea6effef898f2ee3d857e5fa2dccbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64476536"
---
# <a name="get-more-data-items-or-records-by-using-pagination-in-azure-logic-apps"></a>在 Azure 逻辑应用中使用分页获取更多数据、 项或记录

当使用中的连接器操作中检索数据、 项或记录[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)，您可能会得到的结果集太大的操作不会同时返回所有结果。 与某些操作的结果数可能会超过连接器的默认页面大小。 在这种情况下，操作仅会返回结果的第一页。 例如的默认页面大小的 SQL Server 连接器**获取行**操作为 2048，但可能根据其他设置而有所不同。

某些操作，可开启*分页*设置，以便逻辑应用可以检索更多结果最大的分页限制，但在操作结束时返回这些结果作为单个消息。 当你使用的分页时，必须指定*阈值*值，该值是想要返回的操作结果的目标数。 直至到达指定的阈值，该操作检索结果。 当你的项的总数小于指定的阈值时，操作将检索所有结果。

启用分页设置检索页基于连接器的页面大小的结果。 此行为意味着，有时，可能会收到比您指定的阈值的更多结果。 例如，在使用 SQL Server**获取行**操作，是否支持分页设置：

* 操作的默认页大小为 2048年每页的记录。
* 假设拥有 10,000 条记录，并指定最小值为 5000 条记录。
* 分页获取页的记录，因此，若要获取至少指定最小值，则该操作返回 6144 记录 （3 个页面 x 2048 记录） 不 5000 条记录。

下面是一些可以在其中超过特定操作的默认页面大小的连接器的列表：

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

* 逻辑应用，并想要启用分页的操作。 如果您没有逻辑应用，请参阅[快速入门：创建第一个逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="turn-on-pagination"></a>启用分页

若要确定操作是否支持分页逻辑应用设计器中，检查操作的设置**分页**设置。 此示例演示如何启用 SQL Server 中的分页**获取行**操作。

1. 在操作的右上角，选择省略号 (**...**) 按钮，然后选择**设置**。

   ![打开操作的设置](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings.png)

   如果操作支持分页，操作会显示**分页**设置。

1. 更改**分页**设置从**Off**到**上**。 在中**阈值**属性，指定你想要返回的操作的结果的目标数的整数值。

   ![指定要返回结果的最小的数目](./media/logic-apps-exceed-default-page-size-with-pagination/sql-action-settings-pagination.png)

1. 准备就绪后，选择“完成”。

## <a name="workflow-definition---pagination"></a>工作流定义的分页

当您打开分页支持此功能的操作时，在逻辑应用的工作流定义包括`"paginationPolicy"`属性连同`"minimumItemCount"`中该操作的属性`"runtimeConfiguration"`属性，例如：

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
