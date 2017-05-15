---
title: "Azure Functions 外部表绑定（预览版）| Microsoft Docs"
description: "在 Azure Functions 中使用外部表绑定"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 716438e5ea490f6716999813112305499dbe61a8
ms.contentlocale: zh-cn
ms.lasthandoff: 05/02/2017


---
# <a name="azure-functions-external-table-binding-preview"></a>Azure Functions 外部表绑定（预览版）
本文演示如何在函数中使用内置绑定处理 SaaS 提供程序（例如 Sharepoint、Dynamics）中的表格数据。 Azure Functions 支持外部表的输入和输出绑定。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>API 连接

表绑定利用外部 API 连接使用第三方 SaaS 提供程序进行身份验证。 

分配绑定时，可以创建新的 API 连接，也可以使用同一资源组中的现有 API 连接

### <a name="supported-api-connections-tables"></a>支持的 API 连接（表）

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
> 还可以在 [Azure 逻辑应用](https://docs.microsoft.com/azure/connectors/apis-list)中使用外部表连接

### <a name="creating-an-api-connection-step-by-step"></a>创建 API 连接：分步说明

1. 创建函数 > 自定义函数![创建自定义函数](./media/functions-bindings-storage-table/create-custom-function.jpg)
1. 方案 `Experimental` > `ExternalTable-CSharp` 模板 > 新建 `External Table connection`
![选择表输入模板](./media/functions-bindings-storage-table/create-template-table.jpg)
1. 选择 SaaS 提供程序 > 选择/创建连接 ![配置 SaaS 连接](./media/functions-bindings-storage-table/authorize-API-connection.jpg)
1. 选择 API 连接 > 创建函数 ![创建表函数](./media/functions-bindings-storage-table/table-template-options.jpg)
1. 选择 `Integrate` > `External Table`
    1. 配置要使用目标表的连接。 这些设置将因 SaaS 提供程序而异。 这些将在下文的[数据源设置](#datasourcesettings)
![配置表](./media/functions-bindings-storage-table/configure-API-connection.jpg)中概述

## <a name="usage"></a>使用情况

此示例连接到具有 Id、LastName 和 FirstName 列的名为“Contact”的表。 此代码列出了表中的 Contact 实体，并记录名字和姓氏。

### <a name="bindings"></a>绑定
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
表绑定的 `entityId` 必须为空。

`ConnectionAppSettingsKey` 标识存储 API 连接字符串的应用设置。 在集成 UI 中添加 API 连接时，将自动创建应用设置。

表格连接器提供数据集，每个数据集包含表。 默认数据集的名称为“default”。 下面列出了各种 SaaS 提供程序中的数据集和表的标题：

|连接器|数据集|表|
|:-----|:---|:---| 
|**SharePoint**|站点|SharePoint 列表
|**SQL**|数据库|表 
|**Google Sheet**|电子表格|工作表 
|**Excel**|Excel 文件|工作表 

<!--
See the language-specific sample that copies the input file to the output file.

* [C#](#incsharp)
* [Node.js](#innodejs)

-->
<a name="incsharp"></a>

### <a name="usage-in-c"></a>C 中的用法# #

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
        //retreive table values
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

<!--
<a name="innodejs"></a>

### Usage in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```
-->
<a name="datasourcesettings"></a>
## 数据源设置

### <a name="sql-server"></a>SQL Server

下面是用于创建和填充 Contact 表的脚本。 dataSetName 为“default”。

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

### <a name="google-sheets"></a>Google Sheets
在 Google Docs 中，创建具有名为 `Contact` 的工作表的电子表格。 连接器不能使用电子表格显示名称。 需要将内部名称（以粗体显示）作为 dataSetName，例如：`docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`** 将列名称 `Id`、`LastName`、`FirstName` 添加到第一行，然后填充后续行的数据。

### <a name="salesforce"></a>Salesforce
dataSetName 为“default”。

## <a name="next-steps"></a>后续步骤
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

