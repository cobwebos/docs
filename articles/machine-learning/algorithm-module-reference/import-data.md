---
title: 导入数据：模块引用
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的 "导入数据" 模块将数据从现有的云数据服务加载到机器学习管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: fef7d686479b24b0402ab6f1e6990df74231b8d6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693136"
---
# <a name="import-data-module"></a>导入数据模块

本文介绍了 Azure 机器学习服务的可视界面（预览）的模块。

使用此模块将数据从现有的云数据服务加载到机器学习管道。  

首先，选择要从中进行读取的基于云的存储类型，然后完成其他设置。 定义所需的数据并连接到源后，[导入数据](./import-data.md)会根据其包含的值推断每个列的数据类型，并将数据加载到 Azure 机器学习工作区中。 [导入数据](./import-data.md)的输出是可以与任何管道一起使用的数据集。

  
如果源数据发生更改，可以通过重新运行 "[导入数据](./import-data.md)" 来刷新数据集并添加新的数据。 但是，如果你不想在每次运行管道时从源重新读取源，请选择 "**使用缓存的结果**" 选项 TRUE。 选中此选项后，模块将检查管道是否以前使用相同的源和相同的输入选项运行过。 如果找到了以前的运行，则将使用缓存中的数据，而不是从数据源重新加载数据。
 

## <a name="data-sources"></a>数据源

导入数据模块支持以下数据源。 单击链接以获取有关使用每个数据源的详细说明和示例。 
 
如果你不确定应如何或在何处存储数据，请参阅数据科学过程中常见数据方案的指南： [Azure 机器学习中的高级分析方案](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-plan-sample-scenarios)。 


|数据源| 使用|
|-----------|-----------|  
|[通过 HTTP 的 Web URL](./import-from-web-url-via-http.md)|获取在使用 HTTP 并已提供 CSV、TSV、ARFF 或 SvmLight 格式的 web URL 上承载的数据|  
|[从 Azure Blob 存储导入](./import-from-azure-blob-storage.md) |获取存储在 Azure blob 服务中的数据|  
|[从 Azure SQL 数据库导入](./import-from-azure-sql-database.md) |获取 Azure SQL 数据库中的数据|

## <a name="how-to-configure-import-data"></a>如何配置导入数据
 
1. 将**导入数据**模块添加到管道。 可以在接口的 "**数据输入和输出**" 类别中找到此模块。

1. 单击 "**数据源**"，然后选择要从中进行读取的基于云的存储的类型。 

    其他设置取决于你选择的存储类型以及存储是否受保护。 你可能需要提供帐户名称、文件类型或凭据。 某些源不需要身份验证;对于其他用户，可能需要知道帐户名称、密钥或容器名称。

1. 如果要缓存数据集以便在连续运行时重复使用，请选择 "**使用缓存的结果**" 选项。

    假设没有对模块参数进行其他更改，则管道仅在模块第一次运行时加载数据，并使用数据集的缓存版本。

    如果需要在每次运行管道时重新加载数据，请取消选择此选项。

1. 运行管道。

    当导入数据将数据加载到接口时，它将根据它包含的值（数字或分类）推断每一列的数据类型。

    - 如果标头存在，则使用标头来命名输出数据集的列。

    - 如果数据中没有现有列标题，将使用格式 col1、col2,... 生成新的列名称。 , coln*.

## <a name="results"></a>结果

导入完成后，单击输出数据集，并选择 "**可视化**" 以查看是否已成功导入数据。

如果要保存数据以供重复使用，而不是在每次运行管道时导入新的数据集，请右键单击输出并选择 "**另存为数据集**"。 选择数据集的名称。 保存的数据集在保存时保留数据，当重新运行管道时，即使管道中的数据集发生更改，也不会更新数据。 这可以方便地获取数据的快照。

导入数据后，可能需要对建模和分析进行一些额外的准备工作：


- 使用 "[编辑元数据](./edit-metadata.md)" 可以更改列名，将列处理为不同的数据类型，或指示某些列是标签或特征。

- 使用["选择数据集中的列"](./select-columns-in-dataset.md)可以选择要在建模中转换或使用的列的子集。 通过使用 "[添加列](./add-columns.md)" 模块，可以轻松地将转换或删除的列重新加入原始数据集。  

- 使用[分区和示例](./partition-and-sample.md)来划分数据集、执行采样或获取前 n 行。

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 