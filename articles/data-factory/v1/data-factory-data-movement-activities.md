---
title: 使用复制活动移动数据 | Microsoft Docs
description: 了解数据工厂管道中的数据移动：云存储之间以及本地存储和云存储之间的数据迁移。 使用复制活动。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: cdea020ad4c9c9e828d62fff8639acec2e3d6c74
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621915"
---
# <a name="move-data-by-using-copy-activity"></a>使用复制活动移动数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](data-factory-data-movement-activities.md)
> * [版本 2 - 预览版](../copy-activity-overview.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（预览版），请参阅 [V2 中的复制活动](../copy-activity-overview.md)。

## <a name="overview"></a>概述
在 Azure 数据工厂中，可使用“复制活动”在本地和云数据存储区之间复制数据。 复制数据后，可对其进一步执行转换和分析操作。 还可使用复制活动发布有关商业智能 (BI) 和应用程序消耗的转换和分析结果。

![复制活动的角色](media/data-factory-data-movement-activities/copy-activity.png)

复制活动由安全、可靠、可缩放和[全局可用的服务](#global)提供支持。 本文提供数据工厂和复制活动中移动数据的详细信息。

首先，了解如何在两个云数据存储之间，以及本地数据存储和云数据存储之间迁移数据。

> [!NOTE]
> 若要了解活动的常规信息，请参阅[了解管道和活动](data-factory-create-pipelines.md)。
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>在两个云数据存储之间复制数据
源和接收器数据存储均在云中时，复制活动通过以下阶段将数据从源复制到接收器。 为复制活动提供支持的服务可：

1. 读取源数据存储中的数据。
2. 执行序列化/反序列化、压缩/解压缩、列映射和类型转换。 此服务基于输入数据集、输出数据集和复制活动的配置执行这些操作。
3. 将数据写入目标数据存储。

服务自动选择执行数据移动的最佳区域。 此区域通常是最接近接收器数据存储的区域。

![云到云复制](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>在本地数据存储和云数据存储之间复制数据
若要在本地数据存储和云数据存储之间安全移动数据，请在本地计算机上安装数据管理网关。 数据管理网关是一个支持混合数据移动和处理的代理。 可在数据存储本身所在计算机上或在可访问此数据存储的其他计算机上安装数据管理网关。

在此方案中，数据管理网关执行序列化/反序列化、压缩/解压缩、列映射和类型转换。 数据不会通过 Azure 数据工厂服务流动。 相反，数据管理网关会将数据直接写入目标存储。

![本地和云之间的复制](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

有关说明和演练，请参阅[在本地和云数据存储之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)。 有关此代理的详细信息，请参阅[数据管理网关](data-factory-data-management-gateway.md)。

使用数据管理网关还可将数据移出/移入托管在 Azure IaaS 虚拟机 (VM) 上的受支持数据存储。 在此情况下，可在数据存储本身所在 VM 上或在可访问此数据存储的其他 VM 上安装数据管理网关。

## <a name="supported-data-stores-and-formats"></a>支持的数据存储和格式
数据工厂中的复制活动可以将数据从源数据存储复制到接收器数据存储。 数据工厂支持以下数据存储。 来自任何源的数据都可以写入到任何接收器。 单击某个数据存储即可了解如何将数据复制到该存储，以及如何从该存储复制数据。

> [!NOTE] 
> 如需将数据移入/移出复制活动不支持的数据存储，可通过自己的逻辑使用数据工厂内的**自定义活动**来复制/移动数据。 有关创建和使用自定义活动的详细信息，请参阅[在 Azure数据工厂管道中使用自定义活动](data-factory-use-custom-activities.md)。

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> 带 * 的数据存储既可位于本地，也可位于 Azure IaaS 上，需要用户在本地/Azure IaaS 计算机上安装[数据管理网关](data-factory-data-management-gateway.md)。

### <a name="supported-file-formats"></a>支持的文件格式
可使用“复制活动”在两个基于文件的数据存储之间“按原样复制文件”，可以同时在输入和输出数据集定义中跳过[格式部分](data-factory-create-datasets.md)。 无需任何序列化/反序列化操作即可有效复制数据。

“复制活动”还以指定格式从文件中读取并写入到文件：**Text、JSON、Avro、ORC 和 Parquet**，并且压缩编解码器**GZip、Deflate、BZip2 和 ZipDeflate** 也受支持。 有关详细信息，请参阅[支持的文件和压缩格式](data-factory-supported-file-and-compression-formats.md)。

例如，可执行以下复制活动：

* 从本地 SQL Server 中复制数据，并将其以 ORC 格式写入 Azure Data Lake Store。
* 从本地文件系统中复制文本 (CSV) 格式文件，并将其以 Avro 格式写入 Azure Blob。
* 从本地文件系统中复制压缩文件，并将其解压缩然后传到 Azure Data Lake Store。
* 从 Azure Blob 复制 GZip 压缩文本 (CSV) 格式的数据，并将其写入 Azure SQL 数据库。

## <a name="global"></a>全局可用的数据移动
Azure 数据工厂仅在美国西部、美国东部和北欧区域内可用。 但是，为复制活动提供支持的服务在以下区域和地域内全局可用。 全局可用拓扑可确保高效的数据移动，此类移动通常避免跨区域跃点。 有关某区域内数据工厂和数据移动的可用性，请参阅[服务（按区域）](https://azure.microsoft.com/regions/#services)。

### <a name="copy-data-between-cloud-data-stores"></a>在云数据存储之间复制数据
源和接收器数据存储均在云中时，数据工厂会使用同一地域内最接近接收器的区域中的服务部署来移动数据。 请参照下表进行映射：

| 目标数据存储的地域 | 目标数据存储的区域 | 用于数据移动的区域 |
|:--- |:--- |:--- |
| 美国 | 美国东部 | 美国东部 |
| &nbsp; | 美国东部 2 | 美国东部 2 |
| &nbsp; | 美国中部 | 美国中部 |
| &nbsp; | 美国中北部 | 美国中北部 |
| &nbsp; | 美国中南部 | 美国中南部 |
| &nbsp; | 美国中西部 | 美国中西部 |
| &nbsp; | 美国西部 | 美国西部 |
| &nbsp; | 美国西部 2 | 美国西部 2 |
| 加拿大 | 加拿大东部 | 加拿大中部 |
| &nbsp; | 加拿大中部 | 加拿大中部 |
| 巴西 | 巴西南部 | 巴西南部 |
| 欧洲 | 北欧 | 北欧 |
| &nbsp; | 欧洲西部 | 欧洲西部 |
| 英国 | 英国西部 | 英国南部 |
| &nbsp; | 英国南部 | 英国南部 |
| 亚太区 | 东南亚 | 东南亚 |
| &nbsp; | 东亚 | 东南亚 |
| 澳大利亚 | 澳大利亚东部 | 澳大利亚东部 |
| &nbsp; | 澳大利亚东南部 | 澳大利亚东南部 |
| 印度 | 印度中部 | 印度中部 |
| &nbsp; | 印度西部 | 印度中部 |
| &nbsp; | 印度南部 | 印度中部 |
| 日本 | 日本东部 | 日本东部 |
| &nbsp; | 日本西部 | 日本东部 |
| 韩国 | 韩国中部 | 韩国中部 |
| &nbsp; | 韩国南部 | 韩国中部 |

或者可以通过指定复制活动 `typeProperties` 下的 `executionLocation` 属性，明确指示要用于执行复制的数据工厂服务的区域。 上述**用于数据移动的区域**列中列举了此属性支持的值。 请注意复制过程中数据将通过网络经过该区域。 例如，若要在韩国的 Azure 存储间进行复制，可以指定 `"executionLocation": "Japan East"`，以便经过日本区域（请参阅[示例 JSON](#by-using-json-scripts) 作为参考）。

> [!NOTE]
> 如果目标数据存储的区域不在上方列表中或未找到该区域，默认情况下，复制活动会失败，而不会通过其他区域完成，除非指定了 `executionLocation`。 以后支持的区域列表还将扩大。
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>在本地数据存储和云数据存储之间复制数据
在本地（或 Azure 虚拟机/IaaS）和云存储之间复制数据时，[数据管理网关](data-factory-data-management-gateway.md)会在本地计算机或虚拟机上执行数据移动。 数据不会通过此服务在云中流动，除非使用[暂存复制](data-factory-copy-activity-performance.md#staged-copy)功能。 在这种情况下，数据先流经暂存 Azure Blob 存储，然后写入接收器数据存储。

## <a name="create-a-pipeline-with-copy-activity"></a>创建包含复制活动的管道
可通过以下几种方法创建包含复制活动的管道：

### <a name="by-using-the-copy-wizard"></a>使用复制向导
数据工厂复制向导有助于创建包含复制活动的管道。 使用此管道，无需对链接服务、数据集和管道编写 JSON 定义，即可将数据从支持的源复制到目标源。 有关此向导的详细信息，请参阅[数据工厂复制向导](data-factory-copy-wizard.md)。  

### <a name="by-using-json-scripts"></a>使用 JSON 脚本
可在 Azure 门户、Visual Studio 或 Azure PowerShell 中使用数据工厂编辑器（通过使用复制活动）为管道创建 JSON 定义。 然后，可对其进行部署以在数据工厂中创建管道。 有关包含分步说明的教程，请参阅[教程：在 Azure 数据工厂管道中使用复制活动](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。    

JSON 属性（例如名称、说明、输入和输出表，以及策略）可用于所有类型的活动。 可用于此活动的 `typeProperties` 节的属性因每个活动类型而异。

对于复制活动，`typeProperties` 节因源和接收器的类型而异。 单击[支持的源和接收器](#supported-data-stores-and-formats)部分中的源/接收器，可了解复制活动支持该数据存储的哪些类型属性。

下面是示例 JSON 定义：

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
输出数据集中定义的计划决定活动运行时间（例如：**每天**，其频率为**天**，间隔为 **1**）。 该活动将数据从输入数据集（**源**）复制到输出数据集（**接收器**）。

可向复制活动指定多个输入数据集。 这些数据集用于在活动运行之前验证依赖项。 但是，仅第一个数据集中的数据会复制到目标数据集。 有关详细信息，请参阅[计划和执行](data-factory-scheduling-and-execution.md)。  

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，其中介绍了影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素。 还列出了在内部测试期间观察到的性能，并讨论了优化复制活动性能的多种方法。

## <a name="fault-tolerance"></a>容错
默认情况下，如果遇到数据源与接收器的数据不兼容，复制活动会停止复制数据，并返回故障；而用户则可以显式配置跳过和记录不兼容行，只复制兼容数据，以便能够成功执行复制活动。 有关详细信息，请参阅[复制活动容错](data-factory-copy-activity-fault-tolerance.md)。

## <a name="security-considerations"></a>安全注意事项
请参阅[安全注意事项](data-factory-data-movement-security-considerations.md)，其中介绍了 Azure 数据工厂中的数据移动服务用来保护数据的安全基础结构。

## <a name="scheduling-and-sequential-copy"></a>计划和按顺序复制
若要详细了解如何在数据工厂中计划和执行活动，请参阅[计划和执行](data-factory-scheduling-and-execution.md)。 可以按顺序或以有序的方式依次运行多个复制操作。 请参阅[按顺序复制](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)部分。

## <a name="type-conversions"></a>类型转换
不同数据存储具有不同本机类型系统。 复制活动使用以下 2 步方法执行从源类型到接收器类型的自动类型转换：

1. 从本机源类型转换为 .NET 类型。
2. 从 .NET 类型转换为本机接收器类型。

各数据存储文章中介绍了从本机类型系统到 .NET 类型的数据存储映射。 （单击[支持的数据存储](#supported-data-stores)表中的特定链接）。 可在创建表时使用这些映射确定适当的类型，以便复制活动执行正确转换。

## <a name="next-steps"></a>后续步骤
* 要详细了解复制活动，请参阅[将数据从 Azure Blob 存储复制到 Azure SQL 数据库](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。
* 要了解如何将数据从本地数据存储移动到云数据存储，请参阅[在本地和云数据存储之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)。
