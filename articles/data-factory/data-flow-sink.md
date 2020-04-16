---
title: 映射数据流的接收器转换
description: 了解如何在映射数据流时配置接收器转换。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: a728ec6f261b82d563aeb4a8f5c98bbd546f4400
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413216"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>映射数据流的接收器转换

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

转换数据后，可以将数据沉入目标数据集。 每个数据流至少需要一个接收器转换，但您可以根据需要写入尽可能多的接收器来完成转换流。 要写入其他接收器，请通过新分支和条件拆分创建新流。

每个接收器转换都与一个数据工厂数据集关联。 数据集定义要写入的数据的形状和位置。

## <a name="supported-sink-connectors-in-mapping-data-flow"></a>映射数据流中支持的接收器连接器

目前，以下数据集可用于接收器转换：
    
* [Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties)（JSON、Avro、文本、镶木地板）
* [Azure 数据存储湖存储第 1 代](connector-azure-data-lake-store.md#mapping-data-flow-properties)（JSON、Avro、文本、镶木地板）
* [Azure 数据存储第 2 代](connector-azure-data-lake-storage.md#mapping-data-flow-properties)（JSON、Avro、文本、镶木地板）
* [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Azure SQL 数据库](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure 宇宙DB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

特定于这些连接器的**设置位于"设置"** 选项卡中。有关这些设置的信息位于连接器文档中。 

Azure 数据工厂可以访问[90 多个本机连接器](connector-overview.md)。 要将数据从数据流写入这些其他源，请使用复制活动在数据流完成后从受支持的过渡区域之一加载该数据。

## <a name="sink-settings"></a>接收器设置

添加接收器后，通过 **"接收器"** 选项卡进行配置。在这里，您可以选取或创建接收器写入的数据集。 下面是一个视频，解释文本分隔文件类型的许多不同的 Sink 选项：

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tf7T]

![接收器设置](media/data-flow/sink-settings.png "水槽设置")

**架构漂移：**[架构漂移](concepts-data-flow-schema-drift.md)是数据工厂在无需显式定义列更改的情况下，在数据流中本机处理灵活架构的能力。 启用**允许架构漂移**在接收器数据架构中定义的内容之上写入其他列。

**验证架构：** 如果选择了验证架构，则如果在源投影中找不到传入源架构的任何列，或者数据类型不匹配，则数据流将失败。 使用此设置强制源数据满足已定义的投影的协定。 在数据库源方案中，发出列名称或类型已更改的信号非常有用。

## <a name="field-mapping"></a>字段映射

与"选择"转换类似，在接收器的 **"映射"** 选项卡中，可以决定将写入哪些传入列。 默认情况下，将映射所有输入列（包括漂移列）。 这称为**自动映射**。

关闭自动映射时，您可以选择添加基于列的固定映射或基于规则的映射。 基于规则的映射允许您编写具有模式匹配的表达式，而固定映射将映射逻辑列和物理列名称。 有关基于规则的映射的详细信息，请参阅[映射数据流的列模式](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)。

## <a name="custom-sink-ordering"></a>自定义接收器订购

默认情况下，数据以非确定性顺序写入多个接收器。 执行引擎将在转换逻辑完成时并行写入数据，并且接收器顺序可能因每次运行而异。 要指定和精确接收器排序，请在数据流的一般选项卡中启用**自定义接收器排序**。 启用后，将按增加顺序写入接收器。

![自定义接收器订购](media/data-flow/custom-sink-ordering.png "自定义接收器订购")

## <a name="data-preview-in-sink"></a>接收器中的数据预览

在调试群集上获取数据预览时，不会将数据写入接收器。 将返回数据外观的快照，但不会将任何内容写入您的目标。 要测试将数据写入接收器，请从管道画布运行管道调试。

## <a name="next-steps"></a>后续步骤
现在，您已经创建了数据流，请向管道添加[数据流活动](concepts-data-flow-overview.md)。
