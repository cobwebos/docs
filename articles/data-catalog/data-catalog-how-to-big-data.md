---
title: "如何处理“大数据”数据源 | Microsoft Docs"
description: "操作指南文章着重说明通过“大数据”数据源使用 Azure 数据目录的模式，包括 Azure Blob 存储、Azure Data Lake 和 Hadoop HDFS。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 626d1568-0780-4726-bad1-9c5000c6b31a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/04/2016
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8cf7ea7ca69753c12469dff9dc352d616c276ddd


---
# <a name="how-to-work-with-big-data-sources-in-azure-data-catalog"></a>如何在 Azure 数据目录中处理“大数据”源
## <a name="introduction"></a>介绍
**Microsoft Azure 数据目录**是一个完全托管的云服务，具有企业数据源的注册系统和发现系统的功能。 换而言之，“Azure 数据目录”旨在帮助人们发现、了解和使用数据源，并帮助组织从其现有数据源中获取更多价值（包括大数据）。

**Azure 数据目录** 支持 Azure Blog 存储 blob 和目录以及 Hadoop HDFS 文件和目录的注册。 这些数据源的半结构化性质提供很好的灵活性，但也意味着该用户必须考虑数据源的组织方式从使用 **Azure 数据目录**注册数据源中获取最大的价值。

## <a name="directories-as-logical-data-sets"></a>将目录视为逻辑数据集
组织大数据源的常见模式为将目录视为逻辑数据集。 顶级目录用于定义数据集，子文件夹定义分区，而其包含的文件存储数据本身。

此模式的示例可能为：

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

在此示例中，vehicle_maintenance_events 和 location_tracking_events 表示逻辑数据集。 每个文件夹包含数据文件，其按年份和月份组织到子文件夹中。 每个文件夹可能包含数百或数千个文件。

在此模式下，使用 **Azure 数据目录**注册单个文件可能毫无意义。 相反，注册表示数据集的目录将对处理数据的用户有意义。

## <a name="reference-data-files"></a>引用数据文件
互补模式将引用数据集存储为单个文件。 这些数据集可能被视为大数据的“小”部分，且通常与分析数据模型中的维度类似。 引用数据文件包含用于为存储在大数据存储中的批量数据文件提供上下文的记录。

此模式的示例可能为：

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

分析师或数据科学家处理较大目录结构中包含的数据时，这些参考文件中的数据可用于为在较大数据集中仅通过名称或 ID 指代的实体提供更详细的信息。

在此模式中，使用 **Azure 数据目录**注册单个引用数据文件将有意义。 每个文件代表一个数据集且每个文件都可单独进行批注和发现。

## <a name="alternate-patterns"></a>备用模式
上述模式仅为大数据存储的两种组织方式，但每个执行效果都不一样。 无论怎样构造你的数据源，使用 **Azure 数据目录**注册大数据源时，注意代表数据集的注册文件和目录，其会对组织中的其他成员有价值。 注册所有文件和目录会使目录变得混乱，用户难以找到所需内容。

## <a name="summary"></a>摘要
使用 **Azure 数据目录** 注册数据源使其更易发现和理解。 通过注册和批注代表逻辑数据集的大数据和目录，可帮助用户发现和使用所需大数据源。




<!--HONumber=Nov16_HO3-->


