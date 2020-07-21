---
title: 存储概述-Azure 时序见解 Gen2 |Microsoft Docs
description: 了解 Azure 时序见解 Gen2 中的数据存储。
author: esung22
ms.author: elsung
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/07/2020
ms.custom: seodec18
ms.openlocfilehash: 77616afa95b61d5a0ca726db0d66734fc57133f8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495357"
---
# <a name="data-storage"></a>数据存储

创建 Azure 时序见解 Gen2 环境时，需要创建两个 Azure 资源：

* 可为热数据存储配置的 Azure 时序见解 Gen2 环境。
* 用于冷数据存储的 Azure 存储帐户。

你的热存储中的数据仅可通过[时序查询 api](./time-series-insights-update-tsq.md)和[Azure 时序见解 Gen2 资源管理器](./time-series-insights-update-explorer.md)使用。 你的温存储将包含创建 Azure 时序见解 Gen2 环境时选择的[保持期](./time-series-insights-update-plan.md#the-preview-environment)内的最新数据。

Azure 时序见解 Gen2 将冷存储数据以[Parquet 文件格式](#parquet-file-format-and-folder-structure)保存到 Azure Blob 存储。 Azure 时序见解 Gen2 以独占方式管理此冷存储数据，但可以直接作为标准 Parquet 文件读取。

> [!WARNING]
> 冷存储数据所在 Azure Blob 存储帐户的所有者对该帐户中的所有数据拥有完全访问权限。 此访问权限包括“写入”和“删除”权限。 请勿编辑或删除 Azure 时序见解 Gen2 写入的数据，因为这可能会导致数据丢失。

## <a name="data-availability"></a>数据可用性

Azure 时序见解 Gen2 对数据进行分区和索引，以获得最佳查询性能。 为数据编制索引后，可以从热（如果启用）和冷存储进行查询。 正在引入的数据量可能会影响此可用性。

> [!IMPORTANT]
> 在数据变为可用之前，你可能会遇到长达60秒的时间段。 如果出现超过 60 秒的明显延迟，请通过 Azure 门户提交支持票证。

## <a name="azure-storage"></a>Azure 存储

本部分介绍 azure 时序见解 Gen2 相关的 Azure 存储空间详细信息。

有关 Azure Blob 存储的全面介绍，请阅读[存储 Blob 简介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-storage-account"></a>你的存储帐户

创建 Azure 时序见解 Gen2 环境时，会将 Azure 存储帐户创建为长期冷存储。  

Azure 时序见解 Gen2 在你的 Azure 存储帐户中保留每个事件的两个副本。 一个副本存储按引入时间排序的事件，始终允许按时序访问事件。 随着时间的推移，Azure 时序见解 Gen2 还会创建数据的重新分区副本以优化性能查询。

所有数据都存储在 Azure 存储帐户中。

#### <a name="writing-and-editing-blobs"></a>编写和编辑 blob

若要确保查询性能和数据可用性，请不要编辑或删除 Azure 时序见解 Gen2 创建的任何 blob。

#### <a name="accessing-cold-store-data"></a>访问冷存储数据

除了从[Azure 时序见解 Gen2 资源管理器](./time-series-insights-update-explorer.md)和[时序查询 api](./time-series-insights-update-tsq.md)访问数据外，还可能需要直接从存储在 cold 存储区中的 Parquet 文件访问数据。 例如，可以在 Jupyter 笔记本中读取、转换和清理数据，然后使用它来训练同一 Spark 工作流中的 Azure 机器学习模型。

若要直接从 Azure 存储帐户访问数据，需要具有用于存储 Azure 时序见解 Gen2 数据的帐户的读取访问权限。 然后，可以根据 Parquet 文件的创建时间读取选定的数据，该文件位于下面的 [Parquet 文件格式](#parquet-file-format-and-folder-structure)部分所述的 `PT=Time` 文件夹中。  若要详细了解如何启用对存储帐户的读取访问权限，请参阅[管理对存储帐户资源的访问权限](../storage/blobs/storage-manage-access-to-resources.md)。

#### <a name="data-deletion"></a>数据删除

请勿删除 Azure 时序见解 Gen2 文件。 仅从 Azure 时序见解 Gen2 中管理相关数据。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 文件格式和文件夹结构

Parquet 是一种开源的列式文件格式，旨在提高存储效率和性能。 Azure 时序见解 Gen2 使用 Parquet 来大规模启用基于时间序列 ID 的查询性能。  

有关 Parquet 文件类型的详细信息，请参阅 [Parquet 文档](https://parquet.apache.org/documentation/latest/)。

Azure 时序见解 Gen2 存储数据的副本，如下所示：

* 首先，初始副本按引入时间进行分区，并按到达顺序存储数据。 该数据驻留在 `PT=Time` 文件夹中：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* 其次，重新分区的副本按时序 ID 分组，驻留在 `PT=TsId` 文件夹中：

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

文件夹中 blob 名称的时间戳 `PT=Time` 对应于数据到 Azure 时序见解 Gen2 的到达时间，而不是事件的时间戳。

`PT=TsId` 文件夹中的数据会不断针对查询进行优化，不是静态的。 在重新分区过程中，某些事件可能出现在多个 blob 中。 不保证此文件夹中的 blob 的命名保持不变。 

通常，如果需要通过 Parquet 文件直接访问数据，请使用 `PT=Time` 文件夹。  将来的功能将启用对文件夹的有效访问 `PT=TsId` 。 

> [!NOTE]
>
> * `<YYYY>` 映射到 4 位数的年份表示形式。
> * `<MM>` 映射到两位数的月份表示形式。
> * `<YYYYMMDDHHMMSSfff>` 映射到时间戳表示形式，其中包括四位数的年份 (`YYYY`)、两位数的月份 (`MM`)、两位数的日 (`DD`)、两位数的小时 (`HH`)、两位数的分钟 (`MM`)、两位数的秒 (`SS`) 和三位数的毫秒 (`fff`)。

Azure 时序见解 Gen2 事件被映射到 Parquet 文件内容，如下所示：

* 每个事件映射到单个行。
* 每一行包含带有事件时间戳的**时间戳**列。 “时间戳”属性永远不会为 null。 如果事件源中未指定时间戳属性，则默认为**事件排队时间**。 存储的时间戳始终为 UTC 时间。
* 每一行都包含在创建 Azure 时序见解 Gen2 环境时定义的时序 ID （TSID）列。 TSID 属性名称包含 `_string` 后缀。
* 作为遥测数据发送的所有其他属性都映射到以 `_bool` （布尔）、（ `_datetime` 时间戳）、 `_long` （长）、 `_double` （double）、 `_string` （字符串）或（动态）结尾的列名称， `dynamic` 具体取决于属性类型。  有关详细信息，请阅读[支持的数据类型](./concepts-supported-data-types.md)。
* 此映射架构适用于文件格式的第一个版本（以 **V=1** 表示，存储在使用相同名称的基文件夹中）。 随着此功能的演变，此映射架构可能会更改，引用名称的数字会递增。

## <a name="next-steps"></a>后续步骤

* 阅读有关[数据建模](./time-series-insights-update-tsm.md)的信息。

* 规划[Azure 时序见解 Gen2 环境](./time-series-insights-update-plan.md)。
