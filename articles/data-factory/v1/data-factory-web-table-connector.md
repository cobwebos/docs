---
title: "使用 Azure 数据工厂从 Web 表移动数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂从网页中的表移动数据。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: aaf11636484fb6295c9522795b069839d2783ba9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>使用 Azure 数据工厂从 Web 表源移动数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - GA](data-factory-web-table-connector.md)
> * [版本 2 - 预览版](../connector-web-table.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（即正式版 (GA)）。 如果使用数据工厂服务版本 2（预览版），请参阅 [V2 中的 Web 表连接器](../connector-web-table.md)。

本文概述了如何使用 Azure 数据工厂中的复制活动，从网页中的表将数据移至受支持的接收器数据存储。 本文建立在 [数据移动活动](data-factory-data-movement-activities.md)一文的基础上，该文提供通过复制活动和一系列支持作为源/接收器的数据存储进行数据移动的一般概述。

数据工厂当前仅支持将数据从 Web 表移至其他数据存储，但不支持将数据从其他数据存储移至 Web 表目标。

> [!IMPORTANT]
> 此 Web 连接器目前仅支持从 HTML 页提取表内容。 若要从 HTTP/s 终结点中检索数据，请改用 [HTTP 连接器](data-factory-http-connector.md)。

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以从本地 Cassandra 数据存储移动数据。 

- 创建管道的最简单方法是使用**复制向导**。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。 
- 也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager 模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。 

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储：

1. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。
2. 创建**数据集**以表示复制操作的输入和输出数据。 
3. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。 

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于从 Web 表复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例：将数据从 Web 表复制到 Azure Blob](#json-example-copy-data-from-web-table-to-azure-blob) 部分。 

对于特定于 Web 表的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息：

## <a name="linked-service-properties"></a>链接服务属性
下表提供 Web 链接服务专属 JSON 元素的描述。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |type 属性必须设置为：**Web** |是 |
| URL |Web 源的 URL |是 |
| authenticationType |匿名。 |是 |

### <a name="using-anonymous-authentication"></a>使用匿名身份验证

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 对于所有数据集类型（Azure SQL、Azure Blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分均类似。

每种数据集的 **TypeProperties** 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **WebTable** 类型的数据集的 typeProperties 部分具有以下属性

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type |数据集类型。 必须设置为 **WebTable** |是 |
| path |包含表的资源的相对 URL。 |否。 未指定路径时，仅使用链接服务定义中指定的 URL。 |
| index |资源中表的索引。 请参阅[获取 HTML 页中表的索引](#get-index-of-a-table-in-an-html-page)，了解获取 HTML 页中表的索引的步骤。 |是 |

**示例：**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

而可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

目前，复制活动中的源为 **WebSource** 类型时，不支持其他属性。


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON 示例：将数据从 Web 表复制到 Azure Blob
以下示例显示：

1. [Web](#linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
3. [WebTable](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，其使用 [WebSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

此示例每小时将数据从 Web 表复制到 Azure blob。 示例后续部分描述了这些示例中使用的 JSON 属性。

下一个示例介绍如何从 Web 表将数据复制到 Azure blob。 然而，可在 Azure 数据工厂中使用复制活动，直接将数据复制到[数据移动活动](data-factory-data-movement-activities.md)一文中所述的接收器中。

**Web 链接服务**：该示例将 Web 链接服务用于匿名身份验证。 有关可使用的身份验证的不同类型，请参阅 [Web 链接服务](#linked-service-properties)部分。

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Azure 存储链接服务**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**WebTable 输入数据集** 将“external”设置为“true”会告知数据工厂服务数据集位于数据工厂外，且不由数据工厂中的活动生成。

> [!NOTE]
> 请参阅[获取 HTML 页中表的索引](#get-index-of-a-table-in-an-html-page)，了解获取 HTML 页中表的索引的步骤。  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Azure Blob 输出数据集**

数据将写入到新 blob，每小时进行一次（频率：小时，间隔：1）。

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**包含复制活动的管道**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **WebSource**，将 **sink** 类型设置为 **BlobSink**。

有关 WebSource 支持的属性列表，请参阅 [WebSource 类型属性](#copy-activity-type-properties)。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="get-index-of-a-table-in-an-html-page"></a>获取 HTML 页中表的索引
1. 启动 **Excel 2016**，并切换到“数据”选项卡。  
2. 单击工具栏中的“新建查询”，指向“从其他源”，并单击“从 Web”。

    ![Power Query 菜单](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. 在“从 Web”对话框中，输入要在链接服务 JSON 中使用的 **URL**（例如：https://en.wikipedia.org/wiki/）以及要为数据集指定的路径（例如：AFI%27s_100_Years...100_Movies），并单击“确定”。

    ![“从 Web”对话框](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    此示例中使用的 URL：https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. 如果出现“访问 Web 内容”对话框，请选择正确的 **URL** 和**身份验证**，并单击“连接”。

   ![“访问 Web 内容”对话框](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. 单击树视图中的“表”项，查看表中的内容，并单击底部的“编辑”按钮。  

   ![“导航器”对话框](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. 在“查询编辑器”窗口中，单击工具栏上的“高级编辑器”按钮。

    ![“高级编辑器”按钮](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. 在“高级编辑器”对话框中，“源”旁边的编号为索引。

    ![高级编辑器 - 索引](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

如果使用的是 Excel 2013，请使用 [Microsoft Power Query for Excel](https://www.microsoft.com/download/details.aspx?id=39379) 获取索引。 有关详细信息，请参阅[连接到网页](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8)一文。 如果使用的是 [Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/)，步骤与之类似。

> [!NOTE]
> 要将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法。
