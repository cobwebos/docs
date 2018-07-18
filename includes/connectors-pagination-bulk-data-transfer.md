---
title: include 文件
description: include 文件
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/09/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: 524bc1d3a19ad8ecfc8d0d210e735d6a9ae0058b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34678179"
---
### <a name="set-up-pagination"></a>设置分页

对于某些连接器及其用于检索多项的操作，结果可能超过连接器的默认页面大小。 在这种情况下，操作仅会返回结果的第一页。 例如，SQL Server - Get rows 操作的默认页面大小为 2048，但是可能因其他设置而有所变化。 为确保获取所有记录，请对该操作启用“分页”设置。 此设置会让逻辑应用向该连接器请求剩余记录，但是操作结束时会将所有结果作为单个消息返回。 

以下仅列出部分可对特定操作开启分页的连接器： 

* <a href="https://docs.microsoft.com/connectors/db2/" target="_blank">DB2</a>
* <a href="https://docs.microsoft.com/connectors/dynamicscrmonline/" target="_blank">Dynamics 365 CRM Online</a>
* <a href="https://docs.microsoft.com/connectors/excel/" target="_blank">Excel</a>
* <a href="https://docs.microsoft.com/connectors/oracle/" target="_blank">Oracle Database</a>
* <a href="https://docs.microsoft.com/connectors/sharepointonline/" target="_blank">SharePoint</a>
* <a href="https://docs.microsoft.com/connectors/sql/" target="_blank">SQL Server</a> 

以下为“Get rows”操作的一个示例：

1. 如要查看该操作是否支持分页，请打开该操作的“设置”。 

   ![在该操作上打开“设置”](./media/connectors-pagination-bulk-data-transfer/sql-action-settings.png)

2. 如果该操作支持分页，将“分页”设置从“关闭”切换为“开启”。 若要确保该操作返回最小结果集，请对“限制”指定一个值。

   ![指定该操作返回最小结果数](./media/connectors-pagination-bulk-data-transfer/sql-action-settings-pagination.png)

3. 准备就绪后，选择“完成”。