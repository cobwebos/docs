---
title: Common Data Model 格式
description: 使用通用数据模型元数据系统转换数据
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/16/2020
ms.author: daperlov
ms.openlocfilehash: 5e75f2203552a69e50ed16176525429c6c9d8810
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807816"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Azure 数据工厂中的通用数据模型格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

通用数据模型（CDM）元数据系统可让数据及其含义在应用程序和业务流程之间轻松共享。 若要了解详细信息，请参阅[通用数据模型](https://docs.microsoft.com/common-data-model/)概述。

在 Azure 数据工厂中，用户可以使用映射数据流来转换[Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) （ADLS Gen2）中存储的 CDM 实体。

> [!NOTE]
> 用于 ADF 数据流的通用数据模型（CDM）格式连接器目前以公共预览版形式提供。

## <a name="mapping-data-flow-properties"></a>映射数据流属性

通用数据模型作为[内联数据集](data-flow-source.md#inline-datasets)提供，同时将数据流映射为源和接收器。

### <a name="source-properties"></a>源属性

下表列出了 CDM 源支持的属性。 可以在 "**源选项**" 选项卡中编辑这些属性。

| “属性” | 描述 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必须为`cdm` | 是 | `cdm` | format |
| 元数据格式 | 数据实体引用所在的位置。 如果使用 CDM 版本1.0，则选择 "清单"。 如果使用1.0 之前的 CDM 版本，请选择 "model.js打开"。 | 是 | `'manifest'` 或 `'model'` | manifestType |
| 根位置：容器 | CDM 文件夹的容器名称 | 是 | String | fileSystem |
| 根位置：文件夹路径 | CDM 文件夹的根文件夹位置 | 是 | String | folderPath |
| 清单文件：实体路径 | 根文件夹中实体的文件夹路径 | 否 | String | entityPath |
| 清单文件：清单名称 | 清单文件的名称。 默认值为 "default"  | 否 | String | manifestName |
| 按上次修改时间筛选 | 选择根据文件上次更改时间筛选文件 | 否 | Timestamp | ModifiedAfter <br> modifiedBefore | 
| 架构链接的服务 | 语料库所在的链接服务 | 是，如果使用清单 | `'adlsgen2'` 或 `'github'` | corpusStore | 
| 实体引用容器 | 容器语料库处于 | 是，如果在 ADLS Gen2 中使用清单和语料库 | String | adlsgen2_fileSystem |
| 实体引用存储库 | GitHub 存储库名称 | 是，如果使用 GitHub 中的清单和语料库 | String | github_repository |
| 实体引用分支 | GitHub 存储库分支 | 是，如果使用 GitHub 中的清单和语料库 | String |  github_branch |
| 语料库文件夹 | 语料库的根位置 | 是，如果使用清单 | String | corpusPath |
| 语料库实体 | 实体引用的路径 | 是 | String | 实体 |
| 允许找不到文件 | 如果为 true，则在找不到文件时不会引发错误 | 否 | `true` 或 `false` | ignoreNoFilesFound |

#### <a name="import-schema"></a>导入架构

CDM 仅可用作内联数据集，并且默认情况下不具有关联的架构。 若要获取列元数据，请单击 "**投影**" 选项卡中的 "**导入架构**" 按钮。这将允许你引用语料库指定的列名称和数据类型。 若要导入该架构，[数据流调试会话](concepts-data-flow-debug-mode.md)必须处于活动状态。


### <a name="cdm-source-data-flow-script-example"></a>CDM 源数据流脚本示例

```
source(output(
        ProductSizeId as integer,
        ProductColor as integer,
        CustomerId as string,
        Note as string,
        LastModifiedDate as timestamp
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    manifestType: 'manifest',
    manifestName: 'ProductManifest',
    entityPath: 'Product',
    corpusPath: 'Products',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    folderPath: 'ProductData',
    fileSystem: 'data') ~> CDMSource
```

### <a name="sink-properties"></a>接收器属性

下表列出了 CDM 接收器支持的属性。 可以在 "**设置**" 选项卡中编辑这些属性。

| “属性” | 描述 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必须为`cdm` | 是 | `cdm` | format |
| 根位置：容器 | CDM 文件夹的容器名称 | 是 | String | fileSystem |
| 根位置：文件夹路径 | CDM 文件夹的根文件夹位置 | 是 | String | folderPath |
| 清单文件：实体路径 | 根文件夹中实体的文件夹路径 | 否 | String | entityPath |
| 清单文件：清单名称 | 清单文件的名称。 默认值为 "default" | 否 | String | manifestName |
| 架构链接的服务 | 语料库所在的链接服务 | 是 | `'adlsgen2'` 或 `'github'` | corpusStore | 
| 实体引用容器 | 容器语料库处于 | 是，如果 ADLS Gen2 中的语料库 | String | adlsgen2_fileSystem |
| 实体引用存储库 | GitHub 存储库名称 | 是，如果语料库在 GitHub 中 | String | github_repository |
| 实体引用分支 | GitHub 存储库分支 | 是，如果语料库在 GitHub 中 | String |  github_branch |
| 语料库文件夹 | 语料库的根位置 | 是 | String | corpusPath |
| 语料库实体 | 实体引用的路径 | 是 | String | 实体 |
| 分区路径 | 将写入分区的位置 | 否 | String | partitionPath |
| 清除文件夹 | 如果在写入前清除目标文件夹 | 否 | `true` 或 `false` | truncate |
| 格式类型 | 选择指定 parquet 格式 | 否 | `parquet`如果指定 | subformat |
| 列分隔符 | 如果写入 DelimitedText，如何分隔列 | 是，如果写入 DelimitedText | String | columnDelimiter |
| 第一行作为标题 | 如果使用 DelimitedText，则列名称是否添加为标头 | 否 | `true` 或 `false` | columnNamesAsHeader |

### <a name="cdm-sink-data-flow-script-example"></a>CDM sink 数据流脚本示例

关联的数据流脚本为：

```
CDMSource sink(allowSchemaDrift: true,
    validateSchema: false,
    entity: 'Product.cdm.json/Product',
    format: 'cdm',
    entityPath: 'ProductSize',
    manifestName: 'ProductSizeManifest',
    corpusPath: 'Products',
    partitionPath: 'adf',
    folderPath: 'ProductSizeData',
    fileSystem: 'cdm',
    subformat: 'parquet',
    corpusStore: 'adlsgen2',
    adlsgen2_fileSystem: 'models',
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CDMSink

```

## <a name="next-steps"></a>后续步骤

在映射数据流中创建[源转换](data-flow-source.md)。
