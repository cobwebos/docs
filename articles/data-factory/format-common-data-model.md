---
title: Common Data Model 格式
description: 使用通用数据模型元数据系统转换数据
author: djpmsft
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: daperlov
ms.openlocfilehash: 5e846ed02d1a0ac22c9c9479f3367800d1dc9dd2
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042586"
---
# <a name="common-data-model-format-in-azure-data-factory"></a>Azure 数据工厂中的通用数据模型格式
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

通用数据模型 (CDM) 元数据系统使数据及其含义能够在应用程序和业务流程之间轻松共享。 若要了解详细信息，请参阅 [通用数据模型](https://docs.microsoft.com/common-data-model/) 概述。

在 Azure 数据工厂中，用户可以使用映射数据流从存储在 [Azure Data Lake Store Gen2](connector-azure-data-lake-storage.md) (ADLS Gen2) 中的 model.js和清单窗体上转换 CDM 实体中的数据。 你还可以使用 CDM 实体引用来传入 CDM 格式的数据，这些实体引用将以 CSV 或 Parquet 格式将数据置于分区文件夹中。 

> [!NOTE]
> 用于 ADF 数据流 (CDM) 格式连接器的通用数据模型目前作为公共预览版提供。

## <a name="mapping-data-flow-properties"></a>映射数据流属性

通用数据模型作为 [内联数据集](data-flow-source.md#inline-datasets) 提供，同时将数据流映射为源和接收器。

> [!NOTE]
> 在编写 CDM 的实体时，必须已将) 定义为用作参考的现有 CDM 实体定义 (元数据架构。 ADF 数据流接收器将读取该 CDM 实体文件，并将该架构导入到接收器中以便进行字段映射。

### <a name="source-properties"></a>源属性

下表列出了 CDM 源支持的属性。 可以在 " **源选项** " 选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必须为 `cdm` | 是 | `cdm` | format |
| 元数据格式 | 数据实体引用所在的位置。 如果使用 CDM 版本1.0，则选择 "清单"。 如果使用1.0 之前的 CDM 版本，请选择 "model.js打开"。 | 是 | `'manifest'` 或 `'model'` | manifestType |
| 根位置：容器 | CDM 文件夹的容器名称 | 是 | String | fileSystem |
| 根位置：文件夹路径 | CDM 文件夹的根文件夹位置 | 是 | String | folderPath |
| 清单文件：实体路径 | 根文件夹中实体的文件夹路径 | 否 | String | entityPath |
| 清单文件：清单名称 | 清单文件的名称。 默认值为 "default"  | 否 | String | manifestName |
| 按上次修改时间筛选 | 选择根据文件上次更改时间筛选文件 | 否 | 时间戳 | ModifiedAfter <br> modifiedBefore | 
| 架构链接的服务 | 语料库所在的链接服务 | 是，如果使用清单 | `'adlsgen2'` 或 `'github'` | corpusStore | 
| 实体引用容器 | 容器语料库处于 | 是，如果在 ADLS Gen2 中使用清单和语料库 | String | adlsgen2_fileSystem |
| 实体引用存储库 | GitHub 存储库名称 | 是，如果使用 GitHub 中的清单和语料库 | String | github_repository |
| 实体引用分支 | GitHub 存储库分支 | 是，如果使用 GitHub 中的清单和语料库 | String |  github_branch |
| 语料库文件夹 | 语料库的根位置 | 是，如果使用清单 | String | corpusPath |
| 语料库实体 | 实体引用的路径 | 是 | String | 实体 |
| 允许找不到文件 | 如果为 true，则在找不到文件时不会引发错误 | 否 | `true` 或 `false` | ignoreNoFilesFound |

如果要在源转换中使用的实体定义与数据文件夹位于同一目录中，则可以取消选中 "使用语料库中的实体"，只需键入要用作实体引用的实体实体即可。

### <a name="sink-settings"></a>接收器设置

* 指向包含要写入的实体定义的 CDM 实体引用文件。

![实体设置](media/data-flow/common-data-model-111.png "实体引用")

* 定义要 ADF 用于写入实体的输出文件的分区路径和格式。

![实体格式](media/data-flow/common-data-model-222.png "实体格式")

* 设置清单文件的输出文件位置、位置和名称。

![cdm 位置](media/data-flow/common-data-model-333.png "CDM 位置")


#### <a name="import-schema"></a>导入架构

CDM 仅可用作内联数据集，并且默认情况下不具有关联的架构。 若要获取列元数据，请单击 "**投影**" 选项卡中的 "**导入架构**" 按钮。这将允许你引用语料库指定的列名称和数据类型。 若要导入该架构， [数据流调试会话](concepts-data-flow-debug-mode.md) 必须处于活动状态，并且您必须具有现有的 CDM 实体定义文件以指向。

将数据流列映射到接收器转换中的实体属性时，请单击 "映射" 选项卡，然后选择 "导入架构"。 ADF 将读取你在接收器选项中所指向的实体引用，使你能够映射到目标 CDM 架构。

![CDM 接收器设置](media/data-flow/common-data-model-444.png "CDM 映射")

> [!NOTE]
>  当对源自 Power BI 或 Power Platform 数据流的源类型使用 model.js时，可能会遇到源转换中的 "语料库路径为 null 或空" 错误。 这可能是由于文件 model.js上的分区位置路径存在格式问题。 若要解决此问题，请执行以下步骤： 

1. 在文本编辑器中打开文件中的 model.js
2. 查找分区。Location 属性 
3. 将 "blob.core.windows.net" 更改为 "dfs.core.windows.net"
4. 将 URL 中的任何 "% 2F" 编码修复为 "/"
 

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

下表列出了 CDM 接收器支持的属性。 可以在 " **设置** " 选项卡中编辑这些属性。

| 名称 | 说明 | 必需 | 允许的值 | 数据流脚本属性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 格式 | 格式必须为 `cdm` | 是 | `cdm` | format |
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
| 格式类型 | 选择指定 parquet 格式 | 否 | `parquet` 如果指定 | subformat |
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

在映射数据流中创建 [源转换](data-flow-source.md) 。
