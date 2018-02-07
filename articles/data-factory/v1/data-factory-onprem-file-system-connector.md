---
title: "使用 Azure 数据工厂向/从文件系统复制数据 | Microsoft Docs"
description: "了解如何使用 Azure 数据工厂向或从本地文件系统复制数据。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: ce19f1ae-358e-4ffc-8a80-d802505c9c84
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a717ea57642c0fb56eec176542ae401f83af6841
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>使用 Azure 数据工厂向或从本地文件系统复制数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](data-factory-onprem-file-system-connector.md)
> * [版本 2 - 预览版](../connector-file-system.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（正式版 (GA)）。 如果使用数据工厂服务版本 2（预览版），请参阅 [V2 中的文件系统连接器](../connector-file-system.md)。


本文介绍如何使用 Azure 数据工厂中的复制活动向/从本地文件系统复制数据。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。

## <a name="supported-scenarios"></a>支持的方案
可以将数据**从本地文件系统**复制到以下数据存储：

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

可以将数据从以下数据存储复制**到本地文件系统**：

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> 复制活动在将源文件成功复制到目标后不会删除该文件。 如果需要在成功复制后删除源文件，请创建一个自定义活动，以便删除该文件并在管道中使用复制活动。 

## <a name="enabling-connectivity"></a>启用连接
数据工厂支持通过**数据管理网关**连接到本地文件系统和从本地文件系统进行连接。 必须在数据工厂服务连接到任何支持的本地数据存储包括文件系统的本地环境中安装数据管理网关。 请参阅[使用数据管理网关在本地源和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文，了解数据管理网关和设置网关的分步说明。 除了数据管理网关之外，要与本地文件系统进行通信，不需要安装其他二进制文件。 必须安装并使用数据管理网关，即使文件系统在 Azure IaaS VM 中。 有关网关的详细信息，请参阅[数据管理网关](data-factory-data-management-gateway.md)。

若要使用 Linux 文件共享，请在 Linux 服务器上安装 [Samba](https://www.samba.org/)，在 Windows 服务器上安装数据管理网关。 不支持在 Linux 服务器上安装数据管理网关。

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以将数据移入/移出文件系统。

创建管道的最简单方法是使用**复制向导**。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure 资源管理器模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储：

1. 创建**数据工厂**。 数据工厂可以包含一个或多个管道。 
2. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。 例如，如果要将数据从 Azure Blob 存储复制到本地文件系统，可创建两个链接服务，将本地文件系统和 Azure 存储帐户链接到数据工厂。 有关特定于本地文件系统的链接服务属性，请参阅[链接服务属性](#linked-service-properties)部分。
3. 创建**数据集**以表示复制操作的输入和输出数据。 在上一个步骤所述的示例中，创建了一个数据集来指定 Blob 容器和包含输入数据的文件夹。 创建了另一个数据集来指定文件系统中的文件夹和文件名（可选）。 有关特定于本地文件系统的数据集属性，请参阅[数据集属性](#dataset-properties)部分。
4. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。 在前面所述的示例中，在复制活动中使用 BlobSource 作为源，FileSystemSink 作为接收器。 同样，如果从本地文件系统复制到 Azure Blob 存储，则在复制活动中使用 FileSystemSource 和 BlobSink。 有关特定于本地文件系统的复制活动属性，请参阅[复制活动属性](#copy-activity-properties)部分。 有关如何将数据存储用作源或接收器的详细信息，请单击前面章节中的相应数据存储链接。

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于向/从文件系统复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例](#json-examples-for-copying-data-to-and-from-file-system)部分。

对于特定于文件系统的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息：

## <a name="linked-service-properties"></a>链接服务属性
可使用**本地文件服务器**链接服务将本地文件系统链接到 Azure 数据工厂。 下表提供本地文件服务器链接服务特定的 JSON 元素的说明。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |确保类型属性设置为 **OnPremisesFileServer**。 |是 |
| host |指定要复制的文件夹的根路径。 请对字符串中的特殊字符使用转义符“\”。 有关示例，请参阅 [Sample linked service and dataset definitions](#sample-linked-service-and-dataset-definitions)（链接服务和数据集定义示例）。 |是 |
| userid |指定有权访问服务器的用户的 ID。 |否（如果选择 encryptedCredential） |
| password |设置用户的密码 (userid)。 |否（如果选择 encryptedCredential） |
| encryptedCredential |指定可通过运行 New-AzureRmDataFactoryEncryptValue cmdlet 获取的加密凭据。 |否（如果选择在纯文本中指定 userid 和密码） |
| gatewayName |指定网关的名称，数据工厂应将其用于连接到本地文件服务器。 |是 |


### <a name="sample-linked-service-and-dataset-definitions"></a>链接服务和数据集定义示例
| 场景 | 链接服务定义中的主机 | 数据集定义中的 folderPath |
| --- | --- | --- |
| 数据管理网关计算机上的本地文件夹： <br/><br/>示例：D:\\\* 或 D:\folder\subfolder\\* |D:\\\\（适用于数据管理网关 2.0 以及更高版本） <br/><br/> localhost（适用于数据管理网关 2.0 之前的版本） |.\\\\ 或 folder\\\\subfolder（适用于数据管理网关 2.0 以及更高版本） <br/><br/>D:\\\\ 或 D:\\\\folder\\\\subfolder（适用于低于 2.0 的网关版本） |
| 远程共享文件夹： <br/><br/>示例：\\\\myserver\\share\\\* 或 \\\\myserver\\share\\folder\\subfolder\\* |\\\\\\\\myserver\\\\share |.\\\\ 或 folder\\\\subfolder |


### <a name="example-using-username-and-password-in-plain-text"></a>示例：使用纯文本格式的用户名和密码

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>示例：使用 encryptedcredential

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的各部分和属性的完整列表，请参阅[创建数据集](data-factory-create-datasets.md)。 数据集 JSON 的结构、可用性和策略等节类似于所有数据集类型。

每个数据集类型的 typeProperties 部分不同。 它提供诸如数据存储中数据的位置和格式等信息。 **FileShare** 类型的数据集的 typeProperties 部分具有以下属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| folderPath |指定文件夹的子路径。 请对字符串中的特殊字符使用转义符“\”。 有关示例，请参阅 [Sample linked service and dataset definitions](#sample-linked-service-and-dataset-definitions)（链接服务和数据集定义示例）。<br/><br/>可将此属性与 **partitionBy** 相组合，基于切片开始/结束日期时间构成文件夹路径。 |是 |
| fileName |如果希望表引用文件夹中的特定文件，请在 **folderPath** 中指定文件名。 如果没有为此属性指定任何值，表将指向文件夹中的所有文件。<br/><br/>如果没有为输出数据集指定 **fileName**，并且没有在活动接收器中指定 **preserveHierarchy**，所生成文件的名称会采用以下格式： <br/><br/>`Data.<Guid>.txt`（示例：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt） |否 |
| fileFilter |指定在 folderPath 中选择一部分文件而不是所有文件时要使用的筛选器。 <br/><br/>允许的值为：`*`（多个字符）和 `?`（单个字符）。<br/><br/>示例 1："fileFilter": "*.log"<br/>示例 2："fileFilter": 2014-1-?.txt"<br/><br/>注意：fileFilter 适用于 FileShare 输入数据集。 |否 |
| partitionedBy |可使用 partitionedBy 指定时序数据的动态 folderPath/fileName。 例如，针对每小时数据参数化的 folderPath。 |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 <br><br> 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。 支持的级别为：**最佳**和**最快**。 请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |

> [!NOTE]
> 不能同时使用 fileName 和 fileFilter。

### <a name="using-partitionedby-property"></a>使用 partitionedBy 属性
如上一部分所述，可以使用 **partitionedBy** 属性、[数据工厂函数和系统变量](data-factory-functions-variables.md)指定时序数据的动态 folderPath 和 filename。

若要了解有关时序数据集、计划和切片的信息，请参阅[创建数据集](data-factory-create-datasets.md)、[计划和执行](data-factory-scheduling-and-execution.md)以及[创建管道](data-factory-create-pipelines.md)。

#### <a name="sample-1"></a>示例 1：

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

在本示例中，{Slice} 已替换为数据工厂系统变量 SliceStart 的值（使用的格式为 YYYYMMDDHH）。 SliceStart 指切片开始时间。 每个切片的 folderPath 不同。 例如：wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104。

#### <a name="sample-2"></a>示例 2：

```JSON
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
有关可用于定义活动的节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出数据集等属性和策略可用于所有类型的活动。 但是，可用于此活动的 **typeProperties** 节的属性因每个活动类型而异。

对于复制活动，这些属性则因源和接收器的类型而异。 要从本地文件系统移动数据，请在复制活动中将源类型设置为 **FileSystemSource**。 同样，要将数据移入本地文件系统，请在复制活动中将接收器类型设置为 **FileSystemSink**。 本部分提供 FileSystemSource 和 FileSystemSink 支持的属性列表。

**FileSystemSource** 支持以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True、False（默认值） |否 |

**FileSystemSink** 支持以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| copyBehavior |源为 BlobSource 或 FileSystem 时，请定义复制行为。 |**PreserveHierarchy：**将文件层次结构保留到目标文件夹中。 也就是说，指向源文件夹的源文件相对路径与指向目标文件夹的目标文件相对路径相同。<br/><br/>**FlattenHierarchy：**源文件夹中的所有文件在目标文件夹的第一个级别中创建。 创建目标文件时，自动生成名称。<br/><br/>**MergeFiles：**将源文件夹的所有文件合并到一个文件中。 如果指定了文件名/blob 名称，则合并的文件名是指定的名称。 否则，它是自动生成的文件名。 |否 |

### <a name="recursive-and-copybehavior-examples"></a>recursive 和 copyBehavior 示例
本节介绍了 recursive 和 copyBehavior 属性值的不同组合的复制操作产生的行为。

| recursive 值 | copyBehavior 值 | 产生的行为 |
| --- | --- | --- |
| 是 |preserveHierarchy |对于具有以下结构的源文件夹 Folder1，<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用与源相同的结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| 是 |flattenHierarchy |对于具有以下结构的源文件夹 Folder1，<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 的自动生成名称 |
| 是 |mergeFiles |对于具有以下结构的源文件夹 Folder1，<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标 Folder1： <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File 5 的内容将合并到一个文件中，且自动生成文件名。 |
| false |preserveHierarchy |对于具有以下结构的源文件夹 Folder1，<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |
| false |flattenHierarchy |对于具有以下结构的源文件夹 Folder1，<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2 的自动生成名称<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |
| false |mergeFiles |对于具有以下结构的源文件夹 Folder1，<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>使用以下结构创建目标文件夹 Folder1：<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 的内容将合并到一个文件中，且自动生成文件名。<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 的自动生成名称<br/><br/>不会选取带有 File3、File4 和 File5 的 Subfolder1。 |

## <a name="supported-file-and-compression-formats"></a>支持的文件和压缩格式
请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md)一文了解详细信息。

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>向/从文件系统复制数据的 JSON 示例
以下示例提供示例 JSON 定义，可使用该定义通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 创建管道。 它们演示如何将数据复制到本地文件系统和 Azure Blob 存储，以及如何从本地文件系统和 Azure Blob 存储复制数据。 但是，可使用 Azure 数据工厂中的复制活动将数据直接从任意源复制到[受支持的源和接收器](data-factory-data-movement-activities.md#supported-data-stores-and-formats)中列出的任意接收器。

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>示例：将数据从本地文件系统复制到 Azure Blob 存储
此示例演示如何将数据从本地文件系统复制到 Azure Blob 存储。 此示例具有以下数据工厂实体：

* [OnPremisesFileServer](#linked-service-properties) 类型的链接服务。
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
* [FileShare](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
* 包含复制活动的[管道](data-factory-create-pipelines.md)，其使用 [FileSystemSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

以下示例每小时将时间序列数据从本地文件系统复制到 Azure Blob 存储。 示例后续部分描述了这些示例中使用的 JSON 属性。

第一步，按照[使用数据管理网关在本地源和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)中的说明设置数据管理网关。

**本地文件服务器的链接服务：**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

建议使用 **encryptedCredential** 属性，而不是 **userid** 和 **password** 属性。 有关此链接服务的详细信息，请参阅[文件服务器的链接服务](#linked-service-properties)。

**Azure 存储链接服务：**

```JSON
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

**本地文件系统输入数据集：**

每小时从新文件中获取数据。 基于切片的开始时间确定 folderPath 和 fileName 属性。  

设置 `"external": "true"` 将告知数据工厂：数据集在数据工厂外部且不由数据工厂中的活动生成。

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**Azure Blob 存储输出数据集：**

数据将写入到新 blob，每小时进行一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
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

**管道中使用文件系统源和 Blob 接收器的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **FileSystemSource**，将 **sink** 类型设置为 **BlobSink**。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[  
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>示例：将数据从 Azure SQL 数据库复制到本地文件系统
以下示例显示：

* [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties) 类型的链接服务。
* [OnPremisesFileServer](#linked-service-properties) 类型的链接服务。
* [AzureSqlTable](data-factory-azure-sql-connector.md#dataset-properties) 类型的输入数据集。
* [FileShare](#dataset-properties) 类型的输出数据集。
* 包含复制活动的管道，该复制活动使用 [SqlSource](data-factory-azure-sql-connector.md##copy-activity-properties) 和 [FileSystemSink](#copy-activity-properties)。

该示例每小时将时间序列数据从 Azure SQL 表复制到本地文件系统。 示例后续部分描述了这些示例中使用的 JSON 属性。

**Azure SQL 数据库链接服务：**

```JSON
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

**本地文件服务器的链接服务：**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

建议使用 **encryptedCredential** 属性，而不是 **userid** 和 **password** 属性。 有关此链接服务的详细信息，请参阅[文件系统的链接服务](#linked-service-properties)。

**Azure SQL 输入数据集：**

该示例假定已在 Azure SQL 中创建表“MyTable”，并且它包含用于时间序列数据的名为“timestampcolumn”的列。

设置 ``“external”: ”true”`` 将告知数据工厂：数据集在数据工厂外部且不由数据工厂中的活动生成。

```JSON
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

**本地文件系统输出数据集：**

每小时将数据复制到新文件。 blob 的 folderPath 和 fileName 基于切片的开始时间确定。

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**管道中使用 SQL 源和文件系统接收器的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，**源**类型设置为 **SqlSource**，**接收器**类型设置为 **FileSystemSink**。 为 **SqlReaderQuery** 属性指定的 SQL 查询选择复制过去一小时的数据。

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[  
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
     ]
   }
}
```


还可以在复制活动定义中将源数据集中的列映射到接收器数据集中的列。 有关详细信息，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。

## <a name="performance-and-tuning"></a>性能和优化
 若要了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素以及各种优化方法，请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)。
