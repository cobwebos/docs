---
title: 从本地 HDFS 移动数据 | Microsoft Docs
description: 了解如何使用 Azure 数据工厂从本地 HDFS 移动数据。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b8a35e19628ebbe75d3f1890a6d64ab777519698
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34621541"
---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>使用 Azure 数据工厂从本地 HDFS 移动数据
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [版本 1 - 正式版](data-factory-hdfs-connector.md)
> * [版本 2 - 预览版](../connector-hdfs.md)

> [!NOTE]
> 本文适用于数据工厂版本 1（即正式版 (GA)）。 如果使用数据工厂服务版本 2（预览版），请参阅 [V2 中的 HDFS 连接器](../connector-hdfs.md)。

本文介绍如何使用 Azure 数据工厂中的复制活动从本地 HDFS 移动数据。 它基于[数据移动活动](data-factory-data-movement-activities.md)一文，其中总体概述了如何使用复制活动移动数据。

可以将数据从 HDFS 复制到任何支持的接收器数据存储。 有关复制活动支持作为接收器的数据存储列表，请参阅[支持的数据存储](data-factory-data-movement-activities.md#supported-data-stores-and-formats)表。 数据工厂当前仅支持将数据从本地 HDFS 移至其他数据存储，但不支持将数据从其他数据存储移至本地 HDFS。

> [!NOTE]
> 复制活动在将源文件成功复制到目标后不会删除该文件。 如果需要在成功复制后删除源文件，请创建一个自定义活动，以便删除该文件并在管道中使用复制活动。 

## <a name="enabling-connectivity"></a>启用连接
数据工厂服务支持通过数据管理网关连接到本地 HDFS。 请参阅[在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文，了解数据管理网关和设置网关的分步说明。 使用网关连接到 HDFS，即使它托管于 Azure IaaS VM 中也是如此。

> [!NOTE]
> 请确保数据管理网关可以访问**所有**[名称节点服务器]:[名称节点端口]和 Hadoop 群集的[数据节点服务器]:[数据节点端口]。 默认[名称节点端口]为 50070，默认[数据节点端口]为 50075。

虽然可在同一台本地计算机或 Azure VM 上将网关安装为 HDFS，但是我们建议在单独的计算机/Azure IaaS VM 上安装网关。 在单独的计算机上安装网关可减少资源争用和提高性能。 在单独的计算机上安装网关时，该计算机应能访问具有 HDFS 的计算机。

## <a name="getting-started"></a>入门
可以使用不同的工具/API 创建包含复制活动的管道，以从 HDFS 源移动数据。

创建管道的最简单方法是使用**复制向导**。 请参阅[教程：使用复制向导创建管道](data-factory-copy-data-wizard-tutorial.md)，以快速了解如何使用复制数据向导创建管道。

也可以使用以下工具创建管道：**Azure 门户**、**Visual Studio**、**Azure PowerShell**、**Azure 资源管理器模板**、**.NET API** 和 **REST API**。 有关创建包含复制活动的管道的分步说明，请参阅[复制活动教程](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

无论使用工具还是 API，执行以下步骤都可创建管道，以便将数据从源数据存储移到接收器数据存储：

1. 创建**链接服务**可将输入和输出数据存储链接到数据工厂。
2. 创建**数据集**以表示复制操作的输入和输出数据。
3. 创建包含复制活动的**管道**，该活动将一个数据集作为输入，将一个数据集作为输出。

使用向导时，会自动创建这些数据工厂实体（链接服务、数据集和管道）的 JSON 定义。 使用工具/API（.NET API 除外）时，使用 JSON 格式定义这些数据工厂实体。  有关用于从 HDFS 数据存储复制数据的数据工厂实体的 JSON 定义示例，请参阅本文的 [JSON 示例：将数据从本地 HDFS 复制到 Azure Blob](#json-example-copy-data-from-on-premises-hdfs-to-azure-blob) 部分。

对于特定于 HDFS 的数据工厂实体，以下部分提供了有关用于定义这些实体的 JSON 属性的详细信息：

## <a name="linked-service-properties"></a>链接服务属性
链接服务可将数据存储链接到数据工厂。 可创建 **Hdfs** 类型的链接服务，以便将本地 HDFS 链接到数据工厂。 下表提供 HDFS 链接服务专属 JSON 元素的描述。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性必须设置为：**Hdfs** |是 |
| URL |HDFS 的 URL |是 |
| authenticationType |匿名或 Windows。 <br><br> 若要对 HDFS 连接器使用 **Kerberos 身份验证**，请参阅[此部分](#use-kerberos-authentication-for-hdfs-connector)相应地设置本地环境。 |是 |
| userName |Windows 身份验证的用户名。 对于 Kerberos 身份验证，指定 `<username>@<domain>.com`。 |是（对于 Windows 身份验证） |
| password |Windows 身份验证的密码。 |是（对于 Windows 身份验证） |
| gatewayName |数据工厂服务用于连接到 HDFS 的网关的名称。 |是 |
| encryptedCredential |访问凭据的 [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) 输出。 |否 |

### <a name="using-anonymous-authentication"></a>使用匿名身份验证

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>使用 Windows 身份验证

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```
## <a name="dataset-properties"></a>数据集属性
有关可用于定义数据集的节和属性的完整列表，请参阅 [Creating datasets](data-factory-create-datasets.md)（创建数据集）一文。 对于所有数据集类型（Azure SQL、Azure Blob、Azure 表等），结构、可用性和数据集 JSON 的策略等部分均类似。

每种数据集的 **typeProperties** 节有所不同，该部分提供有关数据在数据存储中的位置信息。 **FileShare** 类型数据集（包括 HDFS 数据集）的 typeProperties 节具有以下属性

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| folderPath |文件夹路径。 示例：`myfolder`<br/><br/>请对字符串中的特殊字符使用转义符“\”。 例如：对于 folder\subfolder，请指定 folder\\\\subfolder；对于 d:\samplefolder，请指定 d:\\\\samplefolder。<br/><br/>可将此属性与 **partitionBy** 相组合，基于切片开始/结束日期时间构成文件夹路径。 |是 |
| fileName |如果希望表引用文件夹中的特定文件，请在 **folderPath** 中指定文件名。 如果没有为此属性指定任何值，表将指向文件夹中的所有文件。<br/><br/>如果没有为输出数据集指定 fileName，生成的文件的名称会采用以下格式： <br/><br/>Data<Guid>.txt（例如：Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |否 |
| partitionedBy |partitionedBy 可用于指定时序数据的动态 folderPath 和 filename。 示例：folderPath 可针对每小时的数据参数化。 |否 |
| 格式 | 支持以下格式类型：**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat** 和 **ParquetFormat**。 请将格式中的 **type** 属性设置为上述值之一。 有关详细信息，请参阅[文本格式](data-factory-supported-file-and-compression-formats.md#text-format)、[Json 格式](data-factory-supported-file-and-compression-formats.md#json-format)、[Avro 格式](data-factory-supported-file-and-compression-formats.md#avro-format)、[Orc 格式](data-factory-supported-file-and-compression-formats.md#orc-format)和 [Parquet 格式](data-factory-supported-file-and-compression-formats.md#parquet-format)部分。 <br><br> 如果想要在基于文件的存储之间**按原样复制文件**（二进制副本），可以在输入和输出数据集定义中跳过格式节。 |否 |
| compression | 指定数据的压缩类型和级别。 支持的类型为：**GZip**、**Deflate**、**BZip2** 和 **ZipDeflate**。 支持的级别为：**最佳**和**最快**。 有关详细信息，请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md#compression-support)。 |否 |

> [!NOTE]
> filename 和 fileFilter 不能同时使用。

### <a name="using-partionedby-property"></a>使用 partionedBy 属性
如上一部分所述，可以使用 **partitionedBy** 属性、[数据工厂函数和系统变量](data-factory-functions-variables.md)指定时序数据的动态 folderPath 和 filename。

若要了解有关时序数据集、计划和切片的详细信息，请参阅文章[创建数据集](data-factory-create-datasets.md)、[计划和执行](data-factory-scheduling-and-execution.md)和[创建管道](data-factory-create-pipelines.md)。

#### <a name="sample-1"></a>示例 1：

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
在本示例中，{Slice} 已替换为数据工厂系统变量 SliceStart 的值（使用指定的格式 (YYYYMMDDHH)）。 SliceStart 指切片开始时间。 每个切片的 folderPath 不同。 例如：wikidatagateway/wikisampledataout/2014100103 或 wikidatagateway/wikisampledataout/2014100104。

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
有关可用于定义活动的节和属性的完整列表，请参阅[创建管道](data-factory-create-pipelines.md)一文。 名称、说明、输入和输出表格等属性和策略可用于所有类型的活动。

而可用于此活动的 typeProperties 节的属性因每个活动类型而异。 对于复制活动，这些属性则因源和接收器的类型而异。

对于复制活动，如果源的类型为 **FileSystemSource**，则可以在 typeProperties 节中使用以下属性：

**FileSystemSource** 支持以下属性：

| 属性 | 说明 | 允许的值 | 必选 |
| --- | --- | --- | --- |
| recursive |指示是要从子文件夹中以递归方式读取数据，还是只从指定的文件夹中读取数据。 |True、False（默认值） |否 |

## <a name="supported-file-and-compression-formats"></a>支持的文件和压缩格式
请参阅 [Azure 数据工厂中的文件和压缩格式](data-factory-supported-file-and-compression-formats.md)一文了解详细信息。

## <a name="json-example-copy-data-from-on-premises-hdfs-to-azure-blob"></a>JSON 示例：将数据从本地 HDFS 复制到 Azure Blob
此示例演示如何将数据从本地 HDFS 复制到 Azure Blob 存储。 但是，可使用 Azure 数据工厂中的复制活动，**直接**将数据复制到[此处](data-factory-data-movement-activities.md#supported-data-stores-and-formats)所述的任何接收器。  

此示例提供了以下数据工厂实体的 JSON 定义。 可以通过 [Azure 门户](data-factory-copy-activity-tutorial-using-azure-portal.md)、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) 使用这些定义创建管道，以便将数据从 HDFS 复制到 Azure Blob 存储。

1. [OnPremisesHdfs](#linked-service-properties) 类型的链接服务。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 类型的链接服务。
3. [FileShare](#dataset-properties) 类型的输入[数据集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 类型的输出[数据集](data-factory-create-datasets.md)。
5. 包含复制活动的[管道](data-factory-create-pipelines.md)，其使用 [FileSystemSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)。

此示例每隔一小时将数据从本地 HDFS 复制到 Azure blob。 对于这些示例中使用的 JSON 属性，在示例后的部分对其进行描述。

第一步，设置数据管理网关。 [在本地位置和云之间移动数据](data-factory-move-data-between-onprem-and-cloud.md)一文中的说明。

**HDFS 链接服务：** 此示例使用 Windows 身份验证。 请参阅 [HDFS 链接服务](#linked-service-properties)部分，了解各种可用的身份验证。

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure 存储链接服务：**

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

**HDFS 输入数据集：** 此数据集是指 HDFS 文件夹 DataTransfer/UnitTest/。 管道将此文件夹中的所有文件复制到目标。

设置“external”: ”true”将告知数据工厂服务：数据集在数据工厂外部且不由数据工厂中的活动生成。

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Azure Blob 输出数据集：**

数据将写入到新 blob，每隔一小时进行一次（频率：小时，间隔：1）。 根据处理中切片的开始时间，动态计算 blob 的文件夹路径。 文件夹路径使用开始时间的年、月、日和小时部分。

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**管道中使用文件系统源和 Blob 接收器的复制活动：**

管道包含配置为使用输入和输出数据集、且计划每小时运行一次的复制活动。 在管道 JSON 定义中，将 **source** 类型设置为 **FileSystemSource**，将 **sink** 类型设置为 **BlobSink**。 为 **query** 属性指定的 SQL 查询选择复制过去一小时的数据。

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>对 HDFS 连接器使用 Kerberos 身份验证
设置本地环境有两个选项，以便在 HDFS 连接器中使用 Kerberos 身份验证。 可以选择更适合情况的选项。
* 选项 1：[将网关计算机加入 Kerberos 领域](#kerberos-join-realm)
* 选项 2：[启用 Windows 域和 Kerberos 领域之间的相互信任](#kerberos-mutual-trust)

### <a name="kerberos-join-realm"></a>选项 1：将网关计算机加入 Kerberos 领域

#### <a name="requirement"></a>要求：

* 网关计算机需要加入 Kerberos 领域，而不能加入任何 Windows 域。

#### <a name="how-to-configure"></a>如何配置：

**在网关计算机上：**

1.  运行 **Ksetup** 实用工具以配置 Kerberos KDC 服务器和领域。

    由于 Kerberos 领域不同于 Windows 域，计算机必须配置为工作组的成员。 这可以通过按如下所示设置 Kerberos 领域和添加 KDC 服务器来实现。 根据需要将 *REALM.COM* 替换为自己的领域。

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    执行这 2 个命令后，**重启**计算机。

2.  使用 **Ksetup** 命令验证配置。 输出应如下所示：

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**在 Azure 数据工厂中：**

* 使用 **Windows 身份验证**配置 HDFS 连接器，以及用于连接到 HDFS 数据源的 Kerberos 主体名称和密码。 查看配置详细信息中的 [HDFS 链接服务属性](#linked-service-properties)部分。

### <a name="kerberos-mutual-trust"></a>选项 2：启用 Windows 域和 Kerberos 领域之间的相互信任

#### <a name="requirement"></a>要求：
*   网关计算机必须加入 Windows 域。
*   需要可更新域控制器的设置的权限。

#### <a name="how-to-configure"></a>如何配置：

> [!NOTE]
> 根据需要将下面教程中的 REALM.COM 和 AD.COM 替换为自己的领域和域控制器。

**在 KDC 服务器上：**

1.  编辑 **krb5.conf** 文件中的 KDC 配置，以便让 KDC 信任引用以下配置模板的 Windows 域。 默认情况下，该配置位于 **/etc/krb5.conf**。

            [logging]
             default = FILE:/var/log/krb5libs.log
             kdc = FILE:/var/log/krb5kdc.log
             admin_server = FILE:/var/log/kadmind.log

            [libdefaults]
             default_realm = REALM.COM
             dns_lookup_realm = false
             dns_lookup_kdc = false
             ticket_lifetime = 24h
             renew_lifetime = 7d
             forwardable = true

            [realms]
             REALM.COM = {
              kdc = node.REALM.COM
              admin_server = node.REALM.COM
             }
            AD.COM = {
             kdc = windc.ad.com
             admin_server = windc.ad.com
            }

            [domain_realm]
             .REALM.COM = REALM.COM
             REALM.COM = REALM.COM
             .ad.com = AD.COM
             ad.com = AD.COM

            [capaths]
             AD.COM = {
              REALM.COM = .
             }

  配置后**重新启动** KDC 服务。

2.  使用以下命令准备 KDC 服务器中名为 **krbtgt/REALM.COM@AD.COM** 的主体：

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  在 **hadoop.security.auth_to_local** HDFS 服务配置文件中，添加 `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`。

**在域控制器上：**

1.  运行以下 **Ksetup** 命令添加一个领域条目：

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  建立从 Windows 域到 Kerberos 领域的信任关系。 [password] 是主体 **krbtgt/REALM.COM@AD.COM** 的密码。

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  选择在 Kerberos 中使用的加密算法。

    1. 转到“服务器管理器”>“组策略管理”>“域”>“组策略对象”>“默认或活动的域策略”，单击“编辑”。

    2. 在“组策略管理编辑器”弹出窗口中，转到“计算机配置”>“策略”>“Windows 设置”>“安全设置”>“本地策略”>“安全选项”，配置“网络安全: 配置 Kerberos 允许使用的加密类型”。

    3. 选择连接到 KDC 时要使用的加密算法。 通常情况下，可以简单地选择所有选项。

        ![配置 Kerberos 的加密类型](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. 使用 **Ksetup** 命令可指定要在特定领域上使用的加密算法。

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  在域帐户和 Kerberos 主体之间创建映射，以便在 Windows 域中使用 Kerberos 主体。

    1. 启动管理工具 >“Active Directory 用户和计算机”。

    2. 通过单击“查看” > “高级功能”配置高级功能。

    3. 找到要创建映射的帐户，并右键单击以查看“名称映射”> 单击“Kerberos 名称”选项卡。

    4. 从领域中添加主体。

        ![映射安全标识](media/data-factory-hdfs-connector/map-security-identity.png)

**在网关计算机上：**

* 运行以下 **Ksetup** 命令添加一个领域条目。

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**在 Azure 数据工厂中：**

* 使用 **Windows 身份验证**配置 HDFS 连接器，以及用于连接到 HDFS 数据源的域帐户或 Kerberos 主体。 查看配置详细信息中的 [HDFS 链接服务属性](#linked-service-properties)部分。

> [!NOTE]
> 要将源数据集中的列映射到接收器数据集中的列，请参阅[映射 Azure 数据工厂中的数据集列](data-factory-map-columns.md)。


## <a name="performance-and-tuning"></a>性能和优化
请参阅[复制活动性能和优化指南](data-factory-copy-activity-performance.md)，了解影响 Azure 数据工厂中数据移动（复制活动）性能的关键因素及各种优化方法。
