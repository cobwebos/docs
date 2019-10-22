---
title: 从 Azure SQL 数据库导入
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习可视化界面中的 "导入数据" 模块从 Azure SQL 数据库中获取数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 374776088d93813f39122f2018b00466c55d2b6e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694605"
---
# <a name="import-from-azure-sql-database"></a>从 Azure SQL 数据库导入

本文介绍如何使用 Azure 机器学习可视化界面中的 "[导入数据](import-data.md)" 模块从 Azure SQL 数据库中获取数据。  

若要从数据库导入数据，必须指定服务器名称和数据库名称，以及定义表、视图或查询的 SQL 语句。  

通常，在 Azure 数据库中存储数据比在 Azure 中使用表或 blob 更昂贵。 您可以在数据库中存储的数据量可能有限制，具体取决于您的订阅类型。 但是，没有针对 SQL Azure 数据库的事务费用，因此该选项非常适合用于快速访问较少数量的常用信息，如数据查找表或数据字典。

如果在读取数据之前需要能够对数据进行筛选，或者要将预测或指标保存回数据库进行报告，还最好在 Azure 数据库中存储数据。

## <a name="how-to-import-data-from-azure-sql-database"></a>如何从 Azure SQL 数据库导入数据

1. 将[导入数据](import-data.md)模块添加到管道。 可以在 "数据输入和输出" 类别中的 "可视界面" 中找到此模块。

1. 对于 "**数据源**"，请选择 " **Azure SQL 数据库**"。

1. 设置特定于 Azure SQL 数据库的以下选项。

    **数据库服务器名称**：键入由 Azure 生成的服务器名称。 通常，格式 `<generated_identifier>.database.windows.net`。

    **数据库名称**：在指定的服务器上键入现有数据库的名称。

    **服务器用户帐户名**：键入具有数据库访问权限的帐户的用户名。

    **服务器用户帐户密码**：提供指定的用户帐户的密码。

    **数据库查询**：键入或粘贴描述要读取的数据的 SQL 语句。 始终使用 Visual Studio 服务器资源管理器或 SQL Server Data Tools 之类的工具，验证 SQL 语句并预先验证查询结果。

1. 如果读取到 Azure 机器学习的数据集不会在管道的运行之间发生更改，请选择 "**使用缓存的结果**" 选项。

    如果选择此选项，则在第一次运行模块时，管道将加载数据，并使用数据集的缓存版本。

    如果要在每次循环迭代时重载数据集，请取消选择此选项。 每当[导入数据](import-data.md)中的任何参数发生更改时，数据集就会从源中重新加载。

1. 运行管道。

    随着[导入数据](import-data.md)将数据加载到可视界面，还可能会执行某些隐式类型转换，具体取决于源数据库中使用的数据类型。

## <a name="results"></a>结果

导入完成后，单击输出数据集，并选择 "**可视化**" 以查看是否已成功导入数据。

或者，可以使用 visual interface 中的工具更改数据集及其元数据：

- 使用 "[编辑元数据](edit-metadata.md)" 可以更改列名，将列转换为其他数据类型，或指示哪些列是标签或特征。

- 使用["选择数据集中的列"](select-columns-in-dataset.md)可以选择列的子集。

- 使用 "[分区和采样](partition-and-sample.md)" 按条件将数据集分隔开来，或获取前 n 行。

## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 
