---
title: "将数据复制到 Azure Blob 存储/从 Azure Blob 存储复制数据 | Microsoft Docs"
description: "了解如何在 Azure 数据工厂中复制 blob 数据。 使用我们的示例：如何将数据复制到 Azure Blob 存储和 Azure SQL 数据库，以及如何从中复制数据。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d94fef9d51c5f696df37b26867c1c8ebe12a15b9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2018
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>使用 Azure 数据工厂将数据复制到 Azure Blob 存储中或从 Azure Blob 存储中复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](data-factory-azure-blob-connector.md)
> * [版本 2 - 预览版](../connector-azure-blob-storage.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（预览版），请参阅 [V2 中的 Azure Blob 存储连接器](../connector-azure-blob-storage.md)。


本文介绍如何使用 Azure 数据工厂中的复制活动向/从 Azure Blob 存储复制数据。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。

## <a name="overview"></a>概述
可将数据从任一支持的源数据存储复制到 Azure Blob 存储，或从 Azure Blob 存储移动到任一支持的接收器数据存储。 下表列出了有关复制活动支持作为源或接收器的数据存储。 例如，可以将数据从 SQL Server 数据库或 Azure SQL 数据库移动到 Azure blob 存储中。 并且，可以将数据**从** Azure Blob 存储复制**到** Azure SQL 数据仓库或 Azure Cosmos DB 集合中。 

## <a name="supported-scenarios"></a>支持的方案
可以将数据**从 Azure Blob 存储**复制到以下数据存储：

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

可以将数据从以下数据存储复制**到 Azure Blob 存储**：

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]
 
> [!IMPORTANT]
> 复制活动支持从常规用途的 Azure 存储帐户和冷/热 Blob 存储中复制数据，以及将数据复制到常规用途的 Azure 存储帐户和冷/热 Blob 存储。 活动支持**从块 blob、追加 blob 或页 blob 进行读取**，但仅支持**对块 blob 写入**。 不支持将 Azure 高级存储用作接收器，因为它由页 Blob 支持。
> 
> 复制活动在源数据成功复制到目标位置后不会删除该数据。 如果需要在成功复制后删除源数据，请创建一个[自定义活动](data-factory-use-custom-activities.md)，以便删除数据并在管道中使用该活动。 相关示例请参阅 [GitHub 上的删除 Blob 或文件夹示例](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity)。 

## <a name="get-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，此管道将数据移入/移出 Azure Blob 存储。

创建管道的最简单方法是使用**复制向导**。 本文[演练](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage)了如何创建管道，以便将数据从一个 Azure Blob 存储位置复制到另一个 Azure Blob 存储位置。 有关如何创建管道将数据从 Azure Blob 存储复制到 Azure SQL 数据库的教程，请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)。

也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure 资源管理器模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储：

1. 创建**数据工厂**。 数据工厂可以包含一个或多个管道。 
2. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。 例如，如果要将数据从 Azure Blob 存储复制到 Azure SQL 数据库，可创建两个链接服务，将 Azure 存储帐户和 Azure SQL 数据库链接到数据工厂。 有关特定于 Azure Blob 存储的链接服务属性，请参阅[链接服务属性](#linked-service-properties)部分。 
2. 创建**数据集**以表示复制操作的输入和输出数据。 在上一个步骤所述的示例中，创建了一个数据集来指定 Blob 容器和包含输入数据的文件夹。 创建了另一个数据集来指定 Azure SQL 数据库中用于保存从 Blob 存储复制的数据的 SQL 表。 有关特定于 Azure Blob 存储的数据集属性，请参阅[数据集属性](#dataset-properties)部分。
3. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。 在前面所述的示例中，在复制活动中使用 BlobSource 作为源，SqlSink 作为接收器。 同样，如果从 Azure SQL 数据库复制到 Azure Blob 存储，则在复制活动中使用 SqlSource 和 BlobSink。 有关特定于 Azure Blob 存储的复制活动属性，请参阅[复制活动属性](#copy-activity-properties)部分。 有关如何将数据存储用作源或接收器的详细信息，请单击前面章节中的相应数据存储链接。  

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于向/从 Azure Blob 存储复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例](#json-examples-for-copying-data-to-and-from-blob-storage  )部分。

对于特定于 Azure Blob 存储的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息。

## <a name="linked-service-properties"></a>链接服务属性
有两种类型的链接服务，可用于将 Azure 存储链接到 Azure 数据工厂。 它们是：**AzureStorage** 链接服务和 **AzureStorageSas** 链接服务。 Azure 存储链接服务为数据工厂提供 Azure 存储的全局访问权限。 而 Azure 存储 SAS（共享访问签名）链接服务为数据工厂提供 Azure 存储的受限/有时限的访问。 这两种链接服务之间没有其他区别。 请选择适合你需求的链接服务。 以下各部分提供了有关这两种链接服务的详细信息。

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>数据集属性
要指定数据集来表示 Azure Blob 存储中的输入或输出数据，可以将数据集的类型属性设置为：**AzureBlob**。 将数据集的 **linkedServiceName** 属性设置为 Azure 存储或 Azure 存储 SAS 链接服务的名称。  数据集的 type 属性指定 Blob 存储中的 **Blob 容器**和**文件夹**。

有关可用于定义数据集的 JSON 部分和属性的完整列表，请参阅[创建数据集](data-factory-create-datasets.md)一文。 结构、可用性和数据集 JSON 的策略等部分与所有数据集类型（Azure SQL、Azure blob、Azure 表等）类似。

数据工厂支持使用以下符合 CLS 标准、基于 .NET 的类型值在“结构”中为读取数据源（如 Azure Blob）的架构提供类型信息：Int16、Int32、Int64、Single、Double、Decimal、Byte[]、Bool、String、Guid、Datetime、Datetimeoffset、Timespan。 将数据从源数据存储移到接收器数据存储时，数据工厂自动执行类型转换。

每种数据集的 **typeProperties** 节有所不同，该部分提供有关数据在数据存储区中的位置、格式等信息。 **AzureBlob** 类型的数据集的 typeProperties 部分具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| folderPath |到 Blob 存储中的容器和文件夹的路径。 示例：myblobcontainer\myblobfolder\ |是 |
| fileName |blob 的名称。 fileName 可选，并且区分大小写。<br/><br/>如果指定文件名，则活动（包括复制）将对特定 Blob 起作用。<br/><br/>如果未指定 fileName，则复制将包括输入数据集的 folderPath 中所有的 Blob。<br/><br/>如果没有为输出数据集指定 **fileName**，并且没有在活动接收器中指定 **preserveHierarchy**，所生成文件的名称会采用以下格式：Data.<Guid>.txt（例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |否 |
| partitionedBy |partitionedBy 是一个可选属性。 它可用于指定时序数据的动态 folderPath 和 filename。 例如，folderPath 可针对每小时的数据参数化。 请参阅[使用 partitionedBy 属性](#using-partitionedBy-property)部分，了解详细信息和示例。 |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 <br><br> 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。 支持的级别为：**最佳**和**最快**。 有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |

### <a name="using-partitionedby-property"></a>使用 partitionedBy 属性
如上一部分所述，可以使用 **partitionedBy** 属性、[数据工厂函数和系统变量](data-factory-functions-variables.md)指定时序数据的动态 folderPath 和 filename。

有关时序数据集、计划和切片的详细信息，请参阅文章[创建数据集](data-factory-create-datasets.md)和[计划和执行](data-factory-scheduling-and-execution.md)。

#### <a name="sample-1"></a>示例 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

在本示例中，{Slice} 已替换为数据工厂系统变量 SliceStart 的值，格式为指定的指定的格式 (YYYYMMDDHH)。 SliceStart 指切片开始时间。 每个切片的 folderPath 不同。 例如：wikidatagateway/wikisampledataout/2014100103 or wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>示例 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

在本示例中，SliceStart 的年、月、日和时间已提取到 folderPath 和 fileName 属性使用的各个变量。

## <a name="copy-activity-properties"></a>复制活动属性
有关可用于定义活动的节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出数据集等属性和策略可用于所有类型的活动。 但是，可用于此活动的 **typeProperties** 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。 要从 Azure Blob 存储移动数据，请在复制活动中将源类型设置为 **BlobSource**。 同样，要将数据移动到 Azure Blob 存储，请在复制活动中将接收器类型设置为 **BlobSink**。 本部分提供 BlobSource 和 BlobSink 支持的属性列表。

**BlobSource** 支持 **typeProperties** 部分的以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True（默认值）、False |否 |

**BlobSink** 支持以下 **typeProperties** 属性部分：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| copyBehavior |源为 BlobSource 或 FileSystem 时，请定义复制行为。 |<b>PreserveHierarchy</b>：保留目标文件夹中的文件层次结构。 从源文件到源文件夹的相对路径与从目标文件到目标文件夹的相对路径相同。<br/><br/><b>FlattenHierarchy：</b>源文件夹中的所有文件都位于目标文件夹的第一级。 目标文件具有自动生成的名称。 <br/><br/><b>MergeFiles</b>：将源文件夹的所有文件合并到一个文件中。 如果指定文件/Blob 名称，则合并的文件名称将为指定的名称；否则，会自动生成文件名。 |否 |

**BlobSource** 还支持向后兼容性的这两种属性。

* **treatEmptyAsNull**：指定是否将 Null 或空字符串视为 Null 值。
* **skipHeaderLineCount** - 指定需要跳过的行数。 仅适用于输入数据集使用 TextFormat 的情况。

同样，**BlobSink** 支持以下向后兼容性的属性。

* **blobWriterAddHeader**：指定在写入到输出数据集时，是否添加列定义的标头。

数据集目前支持实现相同功能的以下属性：**treatEmptyAsNull**、**skipLineCount** 和 **firstRowAsHeader**。

下表提供了如何使用新数据集属性替代这些 blob 源/接收器属性的指南。

| 复制活动属性 | 数据集属性 |
|:--- |:--- |
| BlobSource 上的 skipHeaderLineCount |skipLineCount 和 firstRowAsHeader。 首先跳过行，然后读取第一行作为标头。 |
| BlobSource 上的 treatEmptyAsNull |输入数据集上的 treatEmptyAsNull |
| BlobSink 上的 blobWriterAddHeader |输出数据集上的 firstRowAsHeader |

有关这些属性的详细信息，请参阅[指定 TextFormat](data-factory-supported-file-and-compression-formats.md#text-format)。    

### <a name="recursive-and-copybehavior-examples"></a>recursive 和 copyBehavior 示例
本节介绍了将 recursive 和 copyBehavior 值进行不同组合所产生的复制操作行为。

| recursive | copyBehavior | 产生的行为 |
| --- | --- | --- |
| 是 |preserveHierarchy |对于具有以下结构的源文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用与源相同的结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5。 |
| 是 |flattenHierarchy |对于具有以下结构的源文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成名称 |
| 是 |mergeFiles |对于具有以下结构的源文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 的内容将合并到一个文件中，且自动生成文件名 |
| false |preserveHierarchy |对于具有以下结构的源文件夹 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy |对于具有以下结构的源文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/><br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles |对于具有以下结构的源文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。 File1 的自动生成名称<br/><br/>不会选取包含 File3、File4 和 File5 的 Subfolder1。 |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>演练：使用“复制向导”将数据复制到 Blob 存储/从 Blob 存储复制数据
让我们看一下如何快速将数据复制到 Azure Blob 存储/从 Azure Blob 存储复制数据。 在本演练中，源和目标数据存储都属于 Azure Blob 存储类型。 本演练中的管道将数据从一个文件夹复制到同一 blob 容器中的其他文件夹中。 本演练有意简单设计，以显示使用 Blob 存储作为源或接收器时的设置或属性。 

### <a name="prerequisites"></a>先决条件
1. 如果尚无 Azure 存储帐户，请创建一个通用 Azure 存储帐户。 在本演练中，使用 blob 存储同时作为源和目标数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../../storage/common/storage-create-storage-account.md#create-a-storage-account)一文获取创建步骤。
2. 在存储帐户中创建名为 adfblobconnector 的 Blob 容器。 
4. 在 adfblobconnector 容器中创建名为 input 的文件夹。
5. 创建含以下内容且名为 emp.txt 的文件，并使用 [Azure 存储资源管理器](https://azurestorageexplorer.codeplex.com/)等工具将其上传到 input 文件夹
    ```json
    John, Doe
    Jane, Doe
    ```
### <a name="create-the-data-factory"></a>创建数据工厂
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击左上角的“创建资源”，单击“智能 + 分析”，然后单击“数据工厂”。
3. 在“新建数据工厂”窗格中：   
    1. 输入 ADFBlobConnectorDF作为名称。 Azure 数据工厂的名称必须全局唯一。 如果收到错误“`*Data factory name “ADFBlobConnectorDF” is not available`”，请更改数据工厂的名称（例如改为 yournameADFBlobConnectorDF），并重新尝试创建。 有关数据工厂项目命名规则，请参阅 [Data Factory - Naming Rules](data-factory-naming-rules.md) （数据工厂 - 命名规则）主题。
    2. 选择 **Azure 订阅**。
    3. 对于资源组，选择“使用现有”以选择现有资源组（或）选择“新建”以输入资源组的名称。
    4. 选择数据工厂的**位置**。
    5. 选中位于边栏选项卡底部的“固定到仪表板”复选框。
    6. 单击“创建”。
3. 完成创建后，将看到如下图所示的“数据工厂”边栏选项卡：![数据工厂主页](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>复制向导
1. 在“数据工厂”主页上，单击“复制数据[预览]”磁贴，以在单独的选项卡上启动“复制数据向导”。    
    
    > [!NOTE]
    >    如果 Web 浏览器卡在“正在授权...”处，请禁用或取消选中“阻止第三方 Cookie 和站点数据”设置，或在保持启用的状态下为 login.microsoftonline.com 创建一个例外，然后尝试再次启动该向导。
2. 在“属性”  页中：
    1. 输入 CopyPipeline作为任务名称。 任务名称是数据工厂中管道的名称。
    2. 输入任务的说明（可选）。
    3. 对于任务频率或任务计划，请选中“按计划定期运行”选项。 如果只想运行该任务一次而不是按计划定期运行，请选择“立即运行一次”。 如果选择“立即运行一次”选项，将创建[一次性管道](data-factory-create-pipelines.md#onetime-pipeline)。 
    4. 保存设置以用于“重复执行模式”。 此任务在下一步中指定的开始和结束时间之间每日运行。
    5. 将开始日期时间更改为 2017/04/21。 
    6. 将结束日期时间更改为 2017/04/25。 建议直接键入日期而不是浏览日历。     
    8. 单击“资源组名称” 的 Azure 数据工厂。
      ![复制工具 - 属性页](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png) 
3. 在“源数据存储”页上，单击“Azure Blob 存储”磁贴。 此页用于指定复制任务的源数据存储。 可使用现有的数据存储链接服务，或指定新的数据存储。 要使用现有链接服务，请选择“来自现有链接服务” ，并选择适当的链接服务。 
    ![复制工具 - 源数据存储页](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. 在“指定 Azure Blob 存储帐户”  页上：
   1. 保留自动生成的名称作为连接名称。 连接名称是 Azure 存储类型的链接服务名称。 
   2. 确认为“帐户选择方法”选择了“来自 Azure 订阅”。
   3. 选择 Azure 订阅或针对 Azure 订阅选择“全选”。   
   4. 从所选订阅的可用 Azure 存储帐户列表中，选择一个 **Azure 存储帐户**。 还可选择手动输入存储帐户设置，方法是在“帐户选择方法”中选择“手动输入”选项。
   5. 单击“资源组名称” 的 Azure 数据工厂。 
      ![复制工具 - 指定 Azure Blob 存储帐户](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. 在“选择输入文件或文件夹”  页上：
   1. 双击“adfblobcontainer”。
   2. 选择“input”，并单击“选择”。 在本演练中，选择输入文件夹。 也可以改为选择文件夹中的 emp.txt 文件。 
      ![复制工具 - 选择输入文件或文件夹](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. 在“选择输入文件或文件夹” 页上：
    1. 确认“文件或文件夹”已设置为 adfblobconnector/input。 如果文件位于子文件夹中，例如，2017/04/01、2017/04/02 依此类推，请输入 adfblobconnector/input/{年}/{月}/{日} 作为文件或文件夹。 在文本框外按 Tab 时，会看到三个下拉列表，用于选择年 (yyyy)、月 (MM) 和日 (dd) 的格式。 
    2. 请勿设置“以递归方式复制文件”。 选择此选项以递归方式遍历文件夹，寻找要复制到目标的文件。 
    3. 请勿选择“二进制复制”选项。 选择此选项将对源文件执行到目标的二进制复制。 请勿对此演练选择该选项，以便在下一页中看到更多选项。 
    4. 确认“压缩类型”已设为“无”。 如果源文件使用支持的格式之一进行压缩，请为此选项选择一个值。 
    5. 单击“资源组名称” 的 Azure 数据工厂。
    ![复制工具 - 选择输入文件或文件夹](./media/data-factory-azure-blob-connector/chose-input-file-folder.png) 
7. 在“文件格式设置”页上，可以看到分隔符以及向导通过分析文件自动检测到的架构。 
    1. 请确认以下选项：a. “文件格式”已设为“文本格式”。 可在下拉列表中看到所有支持的格式。 例如：JSON、Avro、ORC 和 Parquet。
        b. “列分隔符”已设为 `Comma (,)`。 可在下拉列表中看到数据工厂支持的其他列分隔符。 还可以指定自定义分隔符。
        c. “行分隔符”已设为 `Carriage Return + Line feed (\r\n)`。 可在下拉列表中看到数据工厂支持的其他行分隔符。 还可以指定自定义分隔符。
        d.单击“下一步”。 “跳过行计数”已设为“0”。 如果想要跳过文件顶部的几行，请在此处输入数字。
        e.  未设置“第一数据行包含列名”。 如果源文件的第一行包含列名称，请选择此选项。
        f. 已设置“将空列值视为 null”选项。
    2. 展开“高级设置”以查看可用的高级选项。
    3. 在页面底部，查看 emp.txt 文件的数据的“预览”。
    4. 单击底部的“架构”选项卡，查看复制向导通过查看源文件中的数据推断出来的架构。
    5. 检查分隔符并预览数据之后，请单击“下一步”。
    ![复制工具 - 文件格式设置](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)  
8. 在“目标数据存储”页上，选择“Azure Blob 存储”，并单击“下一步”。 在本演练中，使用 Azure blob 存储同时作为源和目标数据存储。    
    ![复制工具 - 选择目标数据存储](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. 在“指定 Azure Blob 存储帐户”页上：
   1. 在“连接名称”字段中输入 AzureStorageLinkedService。
   2. 确认为“帐户选择方法”选择了“来自 Azure 订阅”。
   3. 选择 **Azure 订阅**。  
   4. 选择 Azure 存储帐户。 
   5. 单击“资源组名称” 的 Azure 数据工厂。     
10. 在“选择输出文件或文件夹”页上： 
    6. 指定“文件夹路径”为 adfblobconnector/output/{年}/{月}/{日}。 输入 TAB。
    7. 对于“年”，请选择“yyyy”。
    8. 对于“月”，请确认它已设为“MM”。
    9. 对于“日”，请确认它已设为“dd”。
    10. 确认“压缩类型”已设为“无”。
    11. 确认“复制行为”已设为“合并文件”。 如果已存在具有相同名称的输出文件，新内容将添加到相同文件的末尾。
    12. 单击“资源组名称” 的 Azure 数据工厂。
    ![复制工具 - 选择输出文件或文件夹](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. 在“文件格式设置”页上，查看设置，并单击“下一步”。 可在此处选择“向输出文件添加标题”。 如果选择该选项，将添加一个标题行，包含源架构的列名称。 查看源的架构时，可以重命名默认列名称。 例如，可以将第一列改为“名字”，而第二列改为“姓氏”。 然后，将生成输出文件和标题，其中这些名称为列名称。 
    ![复制工具 - 目标的文件格式设置](media/data-factory-azure-blob-connector/file-format-destination.png)
12. 在“性能设置”页上，确认“云单位”和“并行副本”已设为“自动”，并单击“下一步”。 有关这些设置的详细信息，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md#parallel-copy)。
    ![复制工具 - 性能设置](media/data-factory-azure-blob-connector/copy-performance-settings.png) 
14. 在“摘要”页上，查看所有设置（任务属性、源和目标的设置以及复制设置），并单击“下一步”。
    ![复制工具 -“摘要”页](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. 在“摘要”页中检查信息，并单击“完成”。 复制向导在数据工厂（即启动该向导的位置）中创建两个链接服务、两个数据集（输入和输出）和一个管道。
    ![复制工具 -“部署”页](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>监视管道（复制任务）

1. 单击“部署”页中的链接`Click here to monitor copy pipeline`。 
2. 将以单独的选项卡形式显示“监视和管理应用程序”。![监视和管理应用](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. 将顶部的“开始”时间更改为 `04/19/2017`，“结束”时间改为 `04/27/2017`，然后单击“应用”。 
4. “活动时段”列表中应会显示五个活动时段。 “WindowStart”时间应包含从管道开始到管道结束时间的所有日子。 
5. 针对“活动时段”列表多次单击“刷新”按钮，直到看到所有活动时段均已设为“就绪”。 
6. 现在，确认已在 adfblobconnector 容器的输出文件夹中生成输出文件。 输出文件夹中应包含以下文件夹结构： 
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25    
    ```
有关监视和管理数据工厂的详细信息，请参阅[监视和管理数据工厂管道](data-factory-monitor-manage-app.md)一文。 
 
### <a name="data-factory-entities"></a>数据工厂实体
现在，切换回具有“数据工厂”主页的选项卡。 请注意，数据工厂中现在有两个链接服务、两个数据集和一个管道。 

![“数据工厂”主页和实体](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

单击“创作和部署”启动数据工厂编辑器。 

![数据工厂编辑器](media/data-factory-azure-blob-connector/data-factory-editor.png)

数据工厂中应包含以下“数据工厂”实体： 

 - 两个链接服务。 一个用于源，另一个用于目标。 在本演练中，两个链接服务均指代同一 Azure 存储帐户。 
 - 两个数据集。 一个输入数据集和一个输出数据集。 在本演练中，两个数据集使用相同的 blob 容器，但指代不同的文件夹（输入和输出文件夹）。
 - 一个管道。 管道包含使用 blob 源和 blob 接收器将数据从 Azure blob 位置复制到另一个 Azure blob 位置的复制活动。 

以下各节详细介绍了这些实体。 

#### <a name="linked-services"></a>链接服务
应看到两项链接服务。 一个用于源，另一个用于目标。 在本演练中，这两个服务除名称不同外，定义看起来完全一样。 链接服务的“类型”已设为“AzureStorage”。 链接服务定义中最重要的属性是“connectionString”，数据工厂在运行时用其连接到 Azure 存储帐户。 忽略定义中的 hubName 属性。 

##### <a name="source-blob-storage-linked-service"></a>源 blob 存储链接服务
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>目标 blob 存储链接服务

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

有关 Azure 存储链接服务的详细信息，请参阅[链接服务属性](#linked-service-properties)部分。 

#### <a name="datasets"></a>数据集
有两个数据集：一个输入数据集和一个输出数据集。 两个数据集的类型均已设为“AzureBlob”。 

输入数据集指向 adfblobconnector blob 容器的输入文件夹。 此数据集的“external”属性已设为 true，因为该数据不是由包含将此数据集作为输入的复制活动的管道生成的。 

输出数据集指向同一 blob 容器的输出文件夹。 输出数据集也使用 SliceStart 系统变量的年、月、日来动态计算输出文件的路径。 有关数据工厂支持的函数和系统变量列表，请参阅[数据工厂函数和系统变量](data-factory-functions-variables.md)。 “external”属性已设为 false（默认值），因为此数据集由管道生成。 

有关 Azure Blob 数据集支持的属性的详细信息，请参阅[数据集属性](#dataset-properties)部分。

##### <a name="input-dataset"></a>输入数据集

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>输出数据集

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>管道
管道只包含一个活动。 将活动的“类型”设为“复制”。  活动的类型属性有两个部分，一部分用于源，另一部分用于接收器。 源类型已设为“BlobSource”，因为活动从 blob 存储复制数据。 接收器类型已设为“BlobSink”，因为活动将数据复制到 blob 存储。 复制活动采用 InputDataset-z4y 作为输入，采用OutputDataset-z4y 作为输出。 

有关 BlobSource 和 BlobSink 支持的属性的详细信息，请参阅[复制活动属性](#copy-activity-properties)部分。 

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>向/从 Blob 存储复制数据的 JSON 示例  
以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 这些示例演示了如何将数据复制到 Azure Blob 存储和 Azure SQL 数据库，以及如何从中复制数据。 但是，可使用 Azure 数据工厂中的复制活动将数据**直接**从任何源复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON 示例：将数据从 Blob 存储复制到 SQL 数据库
以下示例显示：

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties) 类型的链接服务。
2. [AzureStorage](#linked-service-properties) 类型的链接服务。
3. [AzureBlob](#dataset-properties)类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的一个[管道](data-factory-create-pipelines.md)，该复制活动使用 [BlobSource](#copy-activity-properties) 和 [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties)。

此示例按小时将时序数据从 Azure blob 复制到 Azure SQL 表。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

**Azure SQL 链接服务：**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure 存储链接服务：**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure 数据工厂支持两种类型的 Azure 存储链接服务：**AzureStorage** 和 **AzureStorageSas**。 对于前者，请指定包括帐户密钥的连接字符串，对于后者，请指定共享访问签名 (SAS) URI。 有关详细信息，请参阅[链接服务](#linked-service-properties)部分。  

**Azure Blob 输入数据集：**

从新 blob 获取数据，每隔一小时进行一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态评估 blob 的文件夹路径和文件名。 文件夹路径使用开始时间的年、月和日部分，文件名使用开始时间的小时部分。 “external”: “true” 设置将告知数据工厂：表在数据工厂外部，且不由数据工厂中的活动生成。

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SQL 输出数据集：**

此示例将数据复制到 Azure SQL 数据库中名为“MyTable”的表。 在 Azure SQL 数据库中创建表，其列数与 Blob CSV 文件要包含的列数相同。 每隔一小时会向表添加新行。

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**管道中使用 Blob 源和 SQL 接收器的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **BlobSource**，**接收器**类型设置为 **SqlSink**。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
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
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON 示例：将数据从 Azure SQL 复制到 Azure Blob
以下示例显示：

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties) 类型的链接服务。
2. [AzureStorage](#linked-service-properties) 类型的链接服务。
3. [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的一个[管道](data-factory-create-pipelines.md)，该复制活动使用 [WebSource](data-factory-azure-sql-connector.md#copy-activity-properties) 和 [BlobSink](#copy-activity-properties)。

此示例按每小时将时序数据从 Azure SQL 表复制到 Azure blob。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

**Azure SQL 链接服务：**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure 存储链接服务：**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure 数据工厂支持两种类型的 Azure 存储链接服务：**AzureStorage** 和 **AzureStorageSas**。 对于前者，请指定包括帐户密钥的连接字符串，对于后者，请指定共享访问签名 (SAS) URI。 有关详细信息，请参阅[链接服务](#linked-service-properties)部分。  

**Azure SQL 输入数据集：**

该示例假定已在 Azure SQL 中创建表“MyTable”，并且它包含用于时间序列数据的名为“timestampcolumn”的列。

“external”: “true” 设置将告知数据工厂服务：表在数据工厂外部，且不由数据工厂中的活动生成。

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Blob 输出数据集：**

数据将写入到新 blob，每隔一小时进行一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime",  "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**管道中使用 SQL 源和 Blob 接收器的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **SqlSource**，**接收器**类型设置为 **BlobSink**。 为 **SqlReaderQuery** 属性指定的 SQL 查询选择复制过去一小时的数据。

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureSQLInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

> [!NOTE]
> 要将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法。
