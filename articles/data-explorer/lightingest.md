---
title: Lightingest 是一个命令行实用程序，用于引入 Azure 数据资源管理器。
description: 了解 LightIngest，这是一个用于将临时数据引入 Azure 数据资源管理器的命令行实用程序。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 99517e45892cd7a6167ae83ff3058edae1377b10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109558"
---
# <a name="install-and-use-lightingest"></a>安装和使用最亮灯

Lightingest 是一个命令行实用程序，用于将临时数据引入 Azure 数据资源管理器。
该实用程序可以从本地文件夹或 Azure Blob 存储容器中提取源数据。

## <a name="prerequisites"></a>先决条件

* 最轻 - 作为[Microsoft.Azure.Kusto.Tools NuGet 包](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)的一部分下载它
* WinRAR - 从[www.win-rar.com/download.html](http://www.win-rar.com/download.html)下载

## <a name="install-lightingest"></a>安装最亮灯

1. 导航到您下载的"灯光"计算机上的位置。 
1. 使用 WinRAR，将*工具*目录提取到您的计算机。

## <a name="run-lightingest"></a>运行照明

1. 导航到计算机上的提取*工具*目录。
1. 从位置栏中删除现有位置信息。
    
      ![删除位置信息](media/lightingest/lightingest-location-bar.png)

1. 输入`cmd`并按**Enter**。
1. 在命令提示符下，`LightIngest.exe`输入后跟相关的命令行参数。

    > [!Tip]
    > 有关受支持的命令行参数的列表，请输入`LightIngest.exe /help`。
    >
    >![命令行帮助](media/lightingest/lightingest-cmd-line-help.png)

1. 输入`LightIngest`后跟将管理引入的 Azure 数据资源管理器群集的连接字符串。
    将连接字符串括在双引号中，并遵循[Kusto 连接字符串规范](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto)。

    例如：
    ```
    LightIngest "Data Source=https://{Cluster name and region}.kusto.windows.net;AAD Federated Security=True"  -db:{Database} -table:Trips -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}" -pattern:"*.csv.gz" -format:csv -limit:2 -ignoreFirst:true -cr:10.0 -dontWait:true
    ```

* 建议的方法用于`LightIngest`使用 中的`https://ingest-{yourClusterNameAndRegion}.kusto.windows.net`引入终结点。 这样，Azure 数据资源管理器服务可以管理引入负载，并且可以轻松地从瞬态错误中恢复。 但是，您还可以配置为`LightIngest`直接使用引擎终结点 （）。`https://{yourClusterNameAndRegion}.kusto.windows.net`
* 为了获得最佳的摄入性能，LightIngest 必须了解原始数据大小，因此`LightIngest`可以估计本地文件的未压缩大小。 但是，`LightIngest`如果不首先下载压缩 blob，可能无法正确估计压缩 blob 的原始大小。 因此，在引入压缩的 Blob 时，将`rawSizeBytes`blob 元数据上的属性设置为以字节为单位的未压缩数据大小。

## <a name="general-command-line-arguments"></a>一般命令行参数

|参数名称         |短名称   |类型    |必需 |描述                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|                      |             |字符串  |必需 |[Azure 数据资源管理器连接字符串](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto)，指定将处理引入的 Kusto 终结点。 应以双引号括起来 |
|-数据库             |-db          |字符串  |可选  |目标 Azure 数据资源管理器数据库名称 |
|-表                |             |字符串  |必需 |目标 Azure 数据资源管理器表名称 |
|-sourcePath           |-source      |字符串  |必需 |blob 容器的源文件或根 URI 的路径。 如果数据在 Blob 中，则必须包含存储帐户密钥或 SAS。 建议以双引号括起来 |
|-前缀               |             |字符串  |可选  |当要引入的源数据驻留在 Blob 存储上时，此 URL 前缀由所有 Blob 共享，不包括容器名称。 <br>例如，如果数据在 中`MyContainer/Dir1/Dir2`，则前缀应为`Dir1/Dir2`。 建议用双引号括起来 |
|-模式              |             |字符串  |可选  |选取源文件/blob 的模式。 支持通配符。 例如，`"*.csv"` 。 建议以双引号括起来 |
|-zipPattern           |             |字符串  |可选  |在选择要引入的 ZIP 存档中的文件时使用的正则表达式。<br>存档中的所有其他文件将被忽略。例如， `"*.csv"`. 建议用双引号括起来 |
|-格式               |-f           |字符串  |可选  |源数据格式。 必须是[受支持的格式](https://docs.microsoft.com/azure/kusto/management/data-ingestion/#supported-data-formats)之一 |
|-引入映射路径 |-映射路径 |字符串  |可选  |引入列映射文件的路径（Json 和 Avro 格式的必需）。 查看[数据映射](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-引入映射参考  |-映射参考  |字符串  |可选  |预先创建的引入列映射的名称（Json 和 Avro 格式的必需）。 查看[数据映射](https://docs.microsoft.com/azure/kusto/management/mappings) |
|-创建时间模式  |             |字符串  |可选  |设置后，用于从文件或 Blob 路径中提取创建时间属性。 请参阅[使用创建时间模式参数](#using-creationtimepattern-argument) |
|-忽略第一行       |-忽略第一 |bool    |可选  |如果设置，将忽略每个文件/blob 的第一个记录（例如，如果源数据具有标头） |
|-标签                  |             |字符串  |可选  |要与引入的数据关联的[标记](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging)。 允许多次发生 |
|-不要等待             |             |bool    |可选  |如果设置为"true"，则不等待摄入完成。 摄入大量文件/blob 时非常有用 |

### <a name="using-creationtimepattern-argument"></a>使用创建时间模式参数

参数`-creationTimePattern`从文件或 blob 路径中提取创建时间属性。 模式不需要反映整个项目路径，只需反映不包含要使用的时间戳的部分。
参数的值必须包含三个部分：
* 时间戳前面的常量测试，以单引号括起来
* 时间戳格式，标准为[.NET 日期时间表示法](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)
* 时间戳之后的常量文本 例如，如果 blob 名称以"历史值 19840101.parquet"结尾（时间戳为年份的四位数字、该月的两位数字和月份当天的两位数字），`-creationTimePattern`则参数的相应值为"历史值"yyymMdd'.parquet"。

### <a name="command-line-arguments-for-advanced-scenarios"></a>高级方案的命令行参数

|参数名称         |短名称   |类型    |必需 |描述                                |
|----------------------|-------------|--------|----------|-------------------------------------------|
|-压缩          |-cr          |double  |可选  |压缩比提示。 引入压缩文件/blob 以帮助 Azure 数据资源管理器评估原始数据大小时非常有用。 按压缩大小除以原始大小 |
|-限制                |-l           |integer |可选  |如果设置，则将引入限制为前 N 个文件 |
|-仅列表             |-list        |bool    |可选  |如果设置，则仅显示已选择要摄入的项目| 
|-引入超时        |             |integer |可选  |所有引入操作完成以分钟表示超时。 默认为 `60`|
|-力同步            |             |bool    |可选  |如果设置，则强制同步摄入。 默认为 `false` |
|-数据批量大小        |             |integer |可选  |设置每个引入操作的总大小限制（MB、未压缩） |
|-文件在巴奇         |             |integer |可选  |设置每个引入操作的文件/Blob 计数限制 |
|-dev跟踪           |-跟踪       |字符串  |可选  |如果设置，诊断日志将写入本地目录（默认情况下，`RollingLogs`在当前目录中，或者可以通过设置开关值进行修改） |

## <a name="blob-metadata-properties"></a>Blob 元数据属性
当与 Azure Blob`LightIngest`一起使用时，将使用某些 blob 元数据属性来增强引入过程。

|元数据属性                            | 使用情况                                                                           |
|---------------------------------------------|---------------------------------------------------------------------------------|
|`rawSizeBytes`, `kustoUncompressedSizeBytes` | 如果设置，将解释为未压缩的数据大小                       |
|`kustoCreationTime`, `kustoCreationTimeUtc`  | 解释为 UTC 时间戳。 如果设置，将用于覆盖 Kusto 中的创建时间。 适用于回填方案 |

## <a name="usage-examples"></a>用法示例

<!-- Waiting for Tzvia or Vladik to rewrite the instructions for this example before publishing it

### Ingesting a specific number of blobs in JSON format

* Ingest two blobs under a specified storage account {Account}, in `JSON` format matching the pattern `.json`
* Destination is the database {Database}, the table `SampleData`
* Indicate that your data is compressed with the approximate ratio of 10.0
* LightIngest won't wait for the ingestion to be completed

To use the LightIngest command below:
1. Create a table command and enter the table name into the LightIngest command, replacing `SampleData`.
1. Create a mapping command and enter the IngestionMappingRef command, replacing `SampleData_mapping`.
1. Copy your cluster name and enter it into the LightIngest command, replacing `{ClusterandRegion}`.
1. Enter the database name into the LightIngest command, replacing `{Database name}`.
1. Replace `{Account}` with your account name and replace `{ROOT_CONTAINER}?{SAS token}` with the appropriate information.

    ```
    LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"  
        -db:{Database name} 
        -table:SampleData 
        -source:"https://{Account}.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}" 
        -IngestionMappingRef:SampleData_mapping 
        -pattern:"*.json" 
        -format:JSON 
        -limit:2 
        -cr:10.0 
        -dontWait:true
    ```
     
1. In Azure Data Explorer, open query count.

    ![Injestion result in Azure Data Explorer](media/lightingest/lightingest-show-failure-count.png)
-->

### <a name="ingesting-blobs-using-a-storage-account-key-or-a-sas-token"></a>使用存储帐户密钥或 SAS 令牌引入 Blob

* 在指定的存储帐户`ACCOUNT`下、在文件夹中`DIR`、容器`CONT`下引入 10 个 blob，并匹配模式`*.csv.gz`
* 目标是数据库`DB`、表`TABLE`，在目标上预先创建引入`MAPPING`映射
* 该工具将等待，直到引入操作完成
* 请注意指定目标数据库和存储帐户密钥与 SAS 令牌的不同选项

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER};{StorageAccountKey}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10

LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True;Initial Catalog=DB"
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -limit:10
```

### <a name="ingesting-all-blobs-in-a-container-not-including-header-rows"></a>引入容器中的所有 Blob，不包括标头行

* 在指定的存储帐户`ACCOUNT`下、文件夹中`DIR1/DIR2`、容器`CONT`下和匹配模式下引入所有 Blob`*.csv.gz`
* 目标是数据库`DB`、表`TABLE`，在目标上预先创建引入`MAPPING`映射
* 源 Blob 包含标题行，因此指示工具删除每个 Blob 的第一条记录
* 该工具将发布用于摄取的数据，并且不会等待引入操作完成

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"https://ACCOUNT.blob.core.windows.net/{ROOT_CONTAINER}?{SAS token}"
  -prefix:"DIR1/DIR2"
  -pattern:*.csv.gz
  -format:csv
  -mappingRef:MAPPING
  -ignoreFirstRow:true
```

### <a name="ingesting-all-json-files-from-a-path"></a>从路径中引入所有 JSON 文件

* 引入路径`PATH`下的所有文件，与模式匹配`*.json`
* 目标是数据库`DB`、`TABLE`表 ，引入映射在本地文件中定义`MAPPING_FILE_PATH`
* 该工具将发布用于摄取的数据，并且不会等待引入操作完成

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
```

### <a name="ingesting-files-and-writing-diagnostic-trace-files"></a>引入文件和写入诊断跟踪文件

* 引入路径`PATH`下的所有文件，与模式匹配`*.json`
* 目标是数据库`DB`、`TABLE`表 ，引入映射在本地文件中定义`MAPPING_FILE_PATH`
* 该工具将发布用于摄取的数据，并且不会等待引入操作完成
* 诊断跟踪文件将在文件夹下本地写入`LOGS_PATH`

```
LightIngest.exe "https://ingest-{ClusterAndRegion}.kusto.windows.net;Fed=True"
  -database:DB
  -table:TABLE
  -source:"PATH"
  -pattern:*.json
  -format:json
  -mappingPath:"MAPPING_FILE_PATH"
  -trace:"LOGS_PATH"
```
## <a name="changelog"></a>更改日志
|版本        |更改                                                                             |
|---------------|------------------------------------------------------------------------------------|
|4.0.9.0        |<ul><li>为  添加了 `-zipPattern` 参数</li><li>为  添加了 `-listOnly` 参数</li><li>在开始运行之前显示参数摘要</li><li>根据`-creationTimePattern`参数，从 Blob 元数据属性或 blob 或文件名读取创建时间</li></ul>|
