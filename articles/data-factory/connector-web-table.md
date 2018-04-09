---
title: 使用 Azure 数据工厂从 Web 表复制数据 | Microsoft Docs
description: 了解 Azure 数据工厂的 Web 表连接器，可通过它将数据从 Web 表复制到数据工厂支持作为接收器的数据存储中。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: f1fa79ed32969f5087107d6105fd2f4baf7640e3
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2018
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>使用 Azure 数据工厂从 Web 表复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](v1/data-factory-web-table-connector.md)
> * [版本 2 - 预览版](connector-web-table.md)

本文概述了如何使用 Azure 数据工厂中的复制活动从 Web 表数据库复制数据。 它是基于概述复制活动总体的[复制活动概述](copy-activity-overview.md)一文。

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务第 1 版（已正式推出 (GA)），请参阅 [V1 中的 Web 表连接器](v1/data-factory-web-table-connector.md)。

## <a name="supported-capabilities"></a>支持的功能

可以将数据从 Web 表数据库复制到任何支持的接收器数据存储。 有关复制活动支持作为源/接收器的数据存储列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)表。

具体而言，此 Web 表连接器支持**从 HTML 页提取表内容**。 若要从 HTTP/s 终结点中检索数据，请改用 [HTTP 连接器](connector-http.md)。

## <a name="prerequisites"></a>先决条件

若要使用此 Web 表连接器，需要设置自托管集成运行时。 有关详细信息，请参阅[自承载集成运行时](create-self-hosted-integration-runtime.md)一文。

## <a name="getting-started"></a>入门

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

对于特定于 Web 表连接器的数据工厂实体，以下部分提供有关用于定义这些实体的属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性

Web 表链接的服务支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | type 属性必须设置为：**Web** |是 |
| url | Web 源的 URL |是 |
| authenticationType | 允许的值为：Anonymous。 |是 |
| connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如[先决条件](#prerequisites)中所述，需要自承载集成运行时。 |是 |

**示例：**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>数据集属性

有关可用于定义数据集的各个部分和属性的完整列表，请参阅数据集一文。 本部分提供 Web 表数据集支持的属性列表。

要从 Web 复制数据，请将数据集的 type 属性设置为“RelationalTable”。 支持以下属性：

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| type | 数据集的 type 属性必须设置为：**WebTable** | 是 |
| 路径 |包含表的资源的相对 URL。 |不会。 未指定路径时，仅使用链接服务定义中指定的 URL。 |
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
        }
    }
}
```

## <a name="copy-activity-properties"></a>复制活动属性

有关可用于定义活动的各部分和属性的完整列表，请参阅[管道](concepts-pipelines-activities.md)一文。 本部分提供 Web 表源支持的属性列表。

### <a name="web-table-as-source"></a>Web 表作为源

要从 Web 表复制数据，请将复制活动中的源类型设置为“WebSource”，不支持任何其他属性。

**示例：**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>获取 HTML 页中表的索引

若要获取表的索引（需要在[数据集属性](#dataset-properties)中进行配置），可以使用例如 Excel 2016 作为工具，如下所示：

1. 启动 **Excel 2016**，并切换到“数据”选项卡。
2. 单击工具栏中的“新建查询”，指向“从其他源”，并单击“从 Web”。

    ![Power Query 菜单](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. 在“从 Web”对话框中，输入要在链接服务 JSON 中使用的 URL（例如：https://en.wikipedia.org/wiki/)）以及要为数据集指定的路径（例如：AFI%27s_100_Years...100_Movies），并单击“确定”。

    ![“从 Web”对话框](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    此示例中使用的 URL：https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. 如果出现“访问 Web 内容”对话框，请选择正确的 **URL** 和**身份验证**，并单击“连接”。

   ![“访问 Web 内容”对话框](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. 单击树视图中的“表”项，查看表中的内容，并单击底部的“编辑”按钮。  

   ![“导航器”对话框](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. 在“查询编辑器”窗口中，单击工具栏上的“高级编辑器”按钮。

    ![“高级编辑器”按钮](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. 在“高级编辑器”对话框中，“源”旁边的编号为索引。

    ![高级编辑器 - 索引](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

如果使用的是 Excel 2013，请使用 [Microsoft Power Query for Excel](https://www.microsoft.com/download/details.aspx?id=39379) 获取索引。 有关详细信息，请参阅[连接到网页](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8)一文。 如果使用的是 [Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/)，步骤与之类似。


## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂中复制活动支持作为源和接收器的数据存储的列表，请参阅[支持的数据存储](copy-activity-overview.md#supported-data-stores-and-formats)。