---
title: Azure 数据工厂中的链接服务 | Microsoft Docs
description: 了解数据工厂中的链接服务。 链接服务将计算/数据存储链接到数据工厂。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 80e9cee0d973dc8575e9645c537b6b69fbeef700
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70137123"
---
# <a name="linked-services-in-azure-data-factory"></a>Azure 数据工厂中的链接服务
> [!div class="op_single_selector" title1="选择在使用数据工厂服务版本："]
> * [版本 1](v1/data-factory-create-datasets.md)
> * [当前版本](concepts-datasets-linked-services.md)

本文介绍了链接服务的定义，采用 JSON 格式定义链接服务的方式以及链接服务在 Azure 数据工厂管道中的用法。

如果对数据工厂不熟悉，请参阅 [Azure 数据工厂简介](introduction.md)了解相关概述。

## <a name="overview"></a>概述
数据工厂可以包含一个或多个数据管道。 “管道”是共同执行一项任务的活动的逻辑分组。 管道中的活动定义对数据执行的操作。 例如，可使用复制活动将数据从本地 SQL Server 复制到 Azure Blob 存储。 然后，可使用在 Azure HDInsight 群集上运行 Hive 脚本的 Hive 活动，将 Blob 存储中的数据处理为生成输出数据。 最后，可再使用一个复制活动将输出数据复制到 Azure SQL 数据仓库，基于该仓库构建商业智能 (BI) 报告解决方案。 有关管道和活动的详细信息，请参阅 Azure 数据工厂中的[管道和活动](concepts-pipelines-activities.md)。

现在，数据集这一名称的意义已经变为看待数据的一种方式，就是以输入和输出的形式指向或引用活动中要使用的数据。

创建数据集之前，必须创建“链接的服务”，将数据存储链接到数据工厂。 链接的服务类似于连接字符串，它定义数据工厂连接到外部资源时所需的连接信息。 不妨这样考虑：数据集代表链接的数据存储中的数据结构，而链接服务则定义到数据源的连接。 例如，Azure 存储链接服务可将存储帐户链接到数据工厂。 Azure Blob 数据集表示 blob 容器以及包含要处理的输入 blob 的 Azure 存储帐户的文件夹。

下面是一个示例方案。 要将数据从 Blob 存储复制到 SQL 数据库，请创建以下两个链接服务：Azure 存储和 Azure SQL 数据库。 然后创建两个数据集：Azure Blob 数据集（即 Azure 存储链接服务）和 Azure SQL 表数据集（即 Azure SQL 数据库链接服务）。 Azure 存储和 Azure SQL 数据库链接服务分别包含数据工厂在运行时用于连接到 Azure 存储和 Azure SQL 数据库的连接字符串。 Azure Blob 数据集指定 blob 容器和 blob 文件夹，该文件夹包含 Blob 存储中的输入 blob。 Azure SQL 表数据集指定要向其复制数据的 SQL 数据库中的 SQL 表。

下图显示了数据工厂中管道、活动、数据集和链接服务之间的关系：

![管道、活动、数据集和链接服务之间的关系](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>链接服务 JSON
数据工厂中的链接服务采用 JSON 格式定义，如下所示：

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

下表描述了上述 JSON 中的属性：

属性 | 说明 | 必填 |
-------- | ----------- | -------- |
name | 链接服务的名称。 请参阅 [Azure 数据工厂 - 命名规则](naming-rules.md)。 |  是 |
type | 链接服务的类型。 例如：AzureStorage（数据存储）或 AzureBatch（计算）。 请参阅 typeProperties 说明。 | 是 |
typeProperties | 每个数据存储或计算的类型属性各不相同。 <br/><br/> 有关支持的数据存储类型及其类型属性，请参阅本文中的[数据集类型](concepts-datasets-linked-services.md#dataset-type)表。 导航到数据存储连接器一文，了解特定于数据存储的类型属性。 <br/><br/> 有关支持的计算类型及其类型属性，请参阅[计算链接服务](compute-linked-services.md)。 | 是 |
connectVia | 用于连接到数据存储的[集成运行时](concepts-integration-runtime.md)。 如果数据存储位于专用网络，则可以使用 Azure 集成运行时或自承载集成运行时。 如果未指定，则使用默认 Azure Integration Runtime。 | 否

## <a name="linked-service-example"></a>链接服务示例
以下链接服务是 Azure 存储链接服务。 请注意：类型设置为“AzureStorage”。 Azure 存储链接服务的类型属性包含连接字符串。 数据工厂服务使用此连接字符串在运行时连接到数据存储。

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>创建链接服务

可以使用以下任一工具或 SDK 创建链接服务：[.NET API](quickstart-create-data-factory-dot-net.md)、[PowerShell](quickstart-create-data-factory-powershell.md)、[REST API](quickstart-create-data-factory-rest-api.md)、Azure 资源管理器模板和 Azure 门户

## <a name="data-store-linked-services"></a>数据存储链接的服务
可以从 "[连接器概述](copy-activity-overview.md#supported-data-stores-and-formats)" 一文中找到数据工厂支持的数据列表。 单击数据存储以了解支持的连接属性。

## <a name="compute-linked-services"></a>计算链接服务
有关可以从数据工厂连接到的不同计算环境以及不同配置的详细信息，请参考[支持的计算环境](compute-linked-services.md)。

## <a name="next-steps"></a>后续步骤
请参阅以下教程，了解使用下列某个工具或 SDK 创建管道和数据集的分步说明。

- [快速入门：使用 .NET 创建数据工厂](quickstart-create-data-factory-dot-net.md)
- [快速入门：使用 PowerShell 创建数据工厂](quickstart-create-data-factory-powershell.md)
- [快速入门：使用 REST API 创建数据工厂](quickstart-create-data-factory-rest-api.md)
- [快速入门：使用 Azure 门户创建数据工厂](quickstart-create-data-factory-portal.md)
