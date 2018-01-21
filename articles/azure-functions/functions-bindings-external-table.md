---
title: "Azure Functions 的外部表绑定（实验性）"
description: "在 Azure Functions 中使用外部表绑定"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.openlocfilehash: 8a4358fa67e45d0b7a2df1519d649099b5ef5850
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="external-table-binding-for-azure-functions-experimental"></a>Azure Functions 的外部表绑定（实验性）

本文介绍如何在 Azure Functions 中处理 SaaS 提供程序（如 Sharepoint 和 Dynamics）中的表格数据。 Azure Functions 支持外部表的输入和输出绑定。

> [!IMPORTANT]
> 外部表绑定是实验性的，可能永远不会达到正式发布 (GA) 状态。 该表绑定仅包括在 Azure Functions 1.x 中，没有计划将它添加到 Azure Functions 2.x。 对于需要访问 SaaS 提供程序中数据的方案，请考虑使用[调用函数的逻辑应用](functions-twitter-email.md)。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>API 连接

表绑定利用外部 API 连接使用第三方 SaaS 提供程序进行身份验证。 

分配绑定时，可以创建新的 API 连接，也可以使用同一资源组中的现有 API 连接。

### <a name="available-api-connections-tables"></a>可用的 API 连接（表）

|连接器|触发器|输入|输出|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||x|x
|[Dynamics 365 for Operations](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||x|x
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||x|x
|[Google Sheets](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||x|x
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||x|x
|[Dynamics 365 for Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||x|x
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||x|x
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||x|x
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||x|x
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||x|x
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||x|x
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||x|x
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||x|x
|UserVoice||x|x
|Zendesk||x|x

> [!NOTE]
> 还可以在 [Azure 逻辑应用](https://docs.microsoft.com/azure/connectors/apis-list)中使用外部表连接。

## <a name="creating-an-api-connection-step-by-step"></a>创建 API 连接：分步说明

1. 在函数应用的 Azure 门户页中，选择加号 (**+**) 创建一个函数。

1. 在“方案”框中，选择“实验性”。

1. 选择“外部表”。

1. 选择一种语言。

2. 在“外部表连接”下，选择一个现有连接或选择“新建”。

1. 对于新连接，配置设置，然后选择“授权”。

1. 选择“创建”创建该函数。

1. 选择“集成”>“外部表”。

1. 配置要使用目标表的连接。 这些设置将因 SaaS 提供程序而异。 以下部分中提供示例。

## <a name="example"></a>示例

此示例连接到具有 Id、LastName 和 FirstName 列的名为“Contact”的表。 此代码列出了表中的 Contact 实体，并记录名字和姓氏。

*function.json* 文件如下所示：

```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```

C# 脚本代码如下所示：

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retrieve table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

### <a name="sql-server-data-source"></a>SQL Server 数据源

若要在 SQL Server 中创建要用于此示例的表，可使用以下脚本。 `dataSetName` 为“default”。

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets-data-source"></a>Google Sheets 数据源

若要在 Google Docs 中创建要用于此示例的表，请创建具有名为 `Contact` 的工作表的电子表格。 连接器不能使用电子表格显示名称。 需要将内部名称（以粗体显示）作为 dataSetName，例如：`docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** 将列名称 `Id`、`LastName`、`FirstName` 添加到第一行，然后填充后续行的数据。

### <a name="salesforce"></a>Salesforce

若要将此示例用于 Salesforce，`dataSetName` 为“default”。

## <a name="configuration"></a>配置

下表解释了在 *function.json* 文件中设置的绑定配置属性。

|function.json 属性 | 说明|
|---------|----------------------|
|**类型** | 必须设置为 `apiHubTable`。 在 Azure 门户中创建触发器时，会自动设置此属性。|
|**direction** | 必须设置为 `in`。 在 Azure 门户中创建触发器时，会自动设置此属性。 |
|**name** | 在函数代码中表示事件项的变量的名称。 | 
|**连接**| 标识存储 API 连接字符串的应用设置。 在集成 UI 中添加 API 连接时，会自动创建应用设置。|
|**dataSetName**|包含要读取的表的数据集的名称。|
|**tableName**|表的名称|
|**entityId**|对于表绑定必须为空。

表格连接器提供数据集，每个数据集包含表。 默认数据集的名称为“default”。 下面列出了各种 SaaS 提供程序中的数据集和表的标题：

|连接器|数据集|表|
|:-----|:---|:---| 
|**SharePoint**|站点|SharePoint 列表
|**SQL**|数据库|表 
|**Google Sheet**|电子表格|工作表 
|**Excel**|Excel 文件|工作表 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 Azure Functions 触发器和绑定](functions-triggers-bindings.md)
