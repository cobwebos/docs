---
title: 导入数据：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "导入数据" 模块将数据从现有的云数据服务加载到机器学习管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: e7aa19c1d189eb19237ea85aae1ad2441d7e98b9
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77163188"
---
# <a name="import-data-module"></a>导入数据模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块将数据从现有的云数据服务加载到机器学习管道。 

> [!Note]
> 此模块提供的所有功能都可以通过工作区登陆页面上的**数据存储**和**数据集**来完成。 建议使用包含其他功能（例如数据监视）的**数据存储**和**数据集**。 若要了解详细信息，请参阅[如何访问数据](../how-to-access-data.md)和[如何注册数据集](../how-to-create-register-datasets.md)一文。
> 注册数据集后，可以在设计器界面**中 -> "** 我的**数据**集" 类别中找到它。 此模块是为 Studio （经典）用户保留的，以获得熟悉的体验。 
>

**导入数据**模块支持从以下源读取数据：

- 通过 HTTP 的 URL
- 通过[**数据存储**](../how-to-access-data.md)的 Azure 云存储）
    - Azure Blob 容器
    - Azure 文件共享
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL 数据库
    - Azure PostgreSQL    

使用云存储之前，需要先在 Azure 机器学习工作区中注册数据存储。 有关详细信息，请参阅[如何访问数据](../how-to-access-data.md)。 

定义所需的数据并连接到源后， **[导入数据](./import-data.md)** 会根据其包含的值推断每个列的数据类型，并将数据加载到设计器管道。 **导入数据**的输出是一个可与任何设计器管道一起使用的数据集。

如果源数据发生更改，可以通过重新运行 "[导入数据](./import-data.md)" 来刷新数据集并添加新的数据。

## <a name="how-to-configure-import-data"></a>如何配置导入数据

1. 将**导入数据**模块添加到管道。 可以在设计器的 "**数据输入和输出**" 类别中找到此模块。

1. 单击 "**启动数据导入向导**"，使用向导配置数据源。

    向导将获取帐户名称和凭据，并帮助您配置其他选项。 如果正在编辑现有配置，则会先加载当前值。

1. 选择 "**数据源**"，然后选择数据源类型。 它可以是 HTTP 或数据存储。

    如果选择 "数据存储"，则可以选择已注册到 Azure 机器学习工作区或创建新数据存储的现有数据存储。 然后，定义要在数据存储中导入的数据的路径。 可以通过单击 "**浏览路径**" !["导入-数据-路径" 来轻松浏览该路径](media/module/import-data-path.png)

1. 选择预览架构以筛选要包括的列。 你还可以在分析选项中定义诸如分隔符这样的高级设置。

    ![导入-数据-预览](media/module/import-data.png)



1. 运行管道。

    当导入数据将数据加载到设计器中时，它将根据它包含的值（数字或分类）推断每一列的数据类型。

    如果存在标头，则使用该标头来命名输出数据集的列。

    如果数据中没有现有列标题，将使用格式 col1、col2,... 生成新的列名称。 , coln*.

## <a name="results"></a>结果

导入完成后，单击输出数据集，并选择 "**可视化**" 以查看是否已成功导入数据。

如果要保存数据以供重复使用，而不是在每次运行管道时导入新的数据集，请在模块的右面板中的 "**输出**" 选项卡下选择 "**注册数据集**" 图标。 选择数据集的名称。 保存的数据集保留保存时的数据，重新运行管道时不会更新数据集，即使管道中的数据集发生更改。 这可用于获取数据的快照。

导入数据后，可能需要对建模和分析进行一些额外的准备工作：

- 使用 "[编辑元数据](./edit-metadata.md)" 可以更改列名，将列处理为不同的数据类型，或指示某些列是标签或特征。

- 使用["选择数据集中的列"](./select-columns-in-dataset.md)可以选择要在建模中转换或使用的列的子集。 通过使用 "[添加列](./add-columns.md)" 模块，可以轻松地将转换或删除的列重新加入原始数据集。  

- 使用[分区和示例](./partition-and-sample.md)来划分数据集、执行采样或获取前 n 行。

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 
