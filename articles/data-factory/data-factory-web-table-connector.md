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
ms.date: 02/23/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 5046ec3f6a29fb4791cd6badc67c2111a9e3ab2c
ms.openlocfilehash: 132ecec8aef2f166753214fce410237103583c08
ms.lasthandoff: 02/24/2017


---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>使用 Azure 数据工厂从 Web 表源移动数据
本文概述如何在 Azure 数据工厂中使用复制活动从网页中的表将数据复制到另一个数据存储。 本文基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何结合使用复制活动和受支持的数据存储进行数据移动。

数据工厂当前仅支持将数据从 Web 表移至其他数据存储，但不支持将数据从其他数据存储移至 Web 表目标。

> [!NOTE]
> 此 Web 连接器目前仅支持从 HTML 页提取表内容。
>
>

## <a name="sample-copy-data-from-web-table-to-azure-blob"></a>示例：将数据从 Web 表复制到 Azure Blob
以下示例显示：

1. [Web](#web-linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) 类型的链接服务。
3. [WebTable](#WebTable-dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，其使用 [WebSource](#websource-copy-activity-type-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties)。

此示例每小时将数据从 Web 表复制到 Azure blob。 示例后续部分描述了这些示例中使用的 JSON 属性。

下一个示例介绍如何从 Web 表将数据复制到 Azure blob。 但是，可使用 Azure 数据工厂中的复制活动，直接将数据复制到[数据移动活动](data-factory-data-movement-activities.md)一文中所述的任何接收器。

**Web 链接服务**：该示例将 Web 链接服务用于匿名身份验证。 有关可使用的身份验证的不同类型，请参阅 [Web 链接服务](#web-linked-service-properties)部分。

```JSON
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

```JSON
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

```JSON
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

```JSON
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

管道包含配置为使用上述输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **WebSource**，将 **sink** 类型设置为 **BlobSink**。

有关 WebSource 支持的属性列表，请参阅 [WebSource 类型属性](#websource-copy-activity-type-properties)。

```JSON
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

## <a name="web-linked-service-properties"></a>Web 链接服务属性
下表提供 Web 链接服务专属 JSON 元素的描述。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |type 属性必须设置为：**Web** |是 |
| URL |Web 源的 URL |是 |
| authenticationType |匿名。 |是 |

### <a name="using-anonymous-authentication"></a>使用匿名身份验证

```JSON
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

## <a name="webtable-dataset-properties"></a>WebTable 数据集属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 对于所有数据集类型（Azure SQL、Azure Blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分均类似。

每种数据集的 **TypeProperties** 节有所不同，该部分提供有关数据在数据存储区中的位置信息。 **WebTable** 类型的数据集的 typeProperties 部分具有以下属性

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type |数据集类型。 必须设置为 **WebTable** |是 |
| path |包含表的资源的相对 URL。 |否。 未指定路径时，仅使用链接服务定义中指定的 URL。 |
| index |资源中表的索引。 请参阅[获取 HTML 页中表的索引](#get-index-of-a-table-in-an-html-page)，了解获取 HTML 页中表的索引的步骤。 |是 |

**示例：**

```JSON
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

## <a name="websource---copy-activity-type-properties"></a>WebSource - 复制活动类型属性
有关可用于定义活动的各节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

另一方面，可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

目前，复制活动中的源为 **WebSource** 类型时，不支持其他属性。

## <a name="get-index-of-a-table-in-an-html-page"></a>获取 HTML 页中表的索引
1. 启动 **Excel 2016**，然后切换到“数据”选项卡。  
2. 单击工具栏中的“新建查询”，指向“从其他源”，然后单击“从 Web”。

    ![Power Query 菜单](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. 在“从 Web”对话框中，输入要在链接服务 JSON 中使用的 **URL**（例如：https://en.wikipedia.org/wiki/）以及要为数据集指定的路径（例如：AFI%27s_100_Years...100_Movies），然后单击“确定”。

    ![“从 Web”对话框](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    此示例中使用的 URL：https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. 如果出现“访问 Web 内容”对话框，请选择正确的 **URL** 和**身份验证**，然后单击“连接”。

   ![“访问 Web 内容”对话框](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. 单击树视图中的“表”项，查看表中的内容，然后单击底部的“编辑”按钮。  

   ![“导航器”对话框](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. 在“查询编辑器”窗口中，单击工具栏上的“高级编辑器”按钮。

    ![“高级编辑器”按钮](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. 在“高级编辑器”对话框中，“源”旁边的编号为索引。

    ![高级编辑器 - 索引](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

如果使用的是 Excel 2013，请使用 [Microsoft Power Query for Excel](https://www.microsoft.com/download/details.aspx?id=39379) 获取索引。 有关详细信息，请参阅[连接到网页](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8)一文。 如果使用的是 [Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/)，步骤与之类似。

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法。

