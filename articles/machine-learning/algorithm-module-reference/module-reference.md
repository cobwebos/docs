---
title: 算法和模块参考
titleSuffix: Azure Machine Learning service
description: 了解 Azure 机器学习可视界面中提供的模块
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 76b70c742289ac98e49338b267660a30abba9557
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210448"
---
# <a name="algorithm--module-reference-overview"></a>算法 & 模块参考概述

此参考内容在 Azure 机器学习服务的可视界面 (预览版) 中提供的每个机器学习算法和模块上都提供了技术背景。

每个模块表示一组可以独立运行的代码, 并根据所需的输入执行机器学习任务。 模块可能包含特定的算法, 或执行在机器学习中非常重要的任务, 如缺少值替换或统计分析。

> [!TIP]
> 在可视界面的任何实验中, 都可以获取有关特定模块的信息。 选择该模块, 然后在 "**快速帮助**" 窗格中选择 "**更多帮助**" 链接。

## <a name="modules"></a>模块

模块按功能进行组织:

| 功能 | 描述 | 模块 |
| --- |--- | ---- |
| 数据格式转换 | 在机器学习中使用的各种文件格式之间转换数据, | [转换为 CSV](convert-to-csv.md) |
| 数据输入和输出 | 将数据从云数据源移到实验中。 在运行试验时, 将结果或中间数据写入到 Azure 存储、SQL 数据库或 Hive, 或使用云存储在试验之间交换数据。  | [导入数据](import-data.md)<br/>[导出数据](export-data.md)<br/>[手动输入数据](enter-data-manually.md) |
| 数据转换 | 对机器学习独有的数据进行的操作, 如规范化或装箱数据、功能选择以及维数缩减。| [选择数据集中的列](select-columns-in-dataset.md) <br/> [编辑元数据](edit-metadata.md) <br/> [清理缺失数据](clean-missing-data.md) <br/>  [功能哈希](feature-hashing.md) <br/>  [从文本提取 N 元语法特征](extract-n-gram-features-from-text.md) <br/> [添加列](add-columns.md) <br/> [添加行](add-rows.md) <br/> [删除重复行](remove-duplicate-rows.md) <br/> [预处理文本](preprocess-text.md) <br/> [联接数据](join-data.md) <br/> [拆分数据](split-data.md) <br/> [规范化数据](normalize-data.md) <br/> [分区和示例](partition-and-sample.md) |
| Python 和 R 模块 | 编写代码, 并将其嵌入模块, 以将 Python 和 R 与试验集成。 | [执行 Python 脚本](execute-python-script.md)   <br/> [创建 Python 模型](create-python-model.md) <br/> [执行 R 脚本](execute-r-script.md)
|  | **机器学习算法**: | |
| 分类 | 预测类。  从二进制 (两类) 或多类算法中进行选择。| [多类决策林](multiclass-decision-forest.md) <br/> [多类提升决策树](multiclass-boosted-decision-tree.md) <br/> [多类逻辑回归](multiclass-logistic-regression.md)  <br/> [多类神经网络](multiclass-neural-network.md)  <br/>  [双类逻辑回归](two-class-logistic-regression.md)  <br/>[双类平均感知器](two-class-averaged-perceptron.md) <br/> [双类&nbsp;提升&nbsp;决策树&nbsp;](two-class-boosted-decision-tree.md)  <br/> [双类决策林](two-class-decision-forest.md)  <br/> [双类神经网络](two-class-neural-network.md)  <br/> [两&#8209;类&nbsp;支持&nbsp;向量机&nbsp;](two-class-support-vector-machine.md) 
| 群集 | 将数据组合在一起。| [K 平均值聚类](k-means-clustering.md)
| 回归 | 预测值。 | [线性回归](linear-regression.md)  <br/> [神经网络回归](neural-network-regression.md)  <br/> [决策林回归](decision-forest-regression.md)  <br/> [提升&nbsp;决策&nbsp;树回归&nbsp;](boosted-decision-tree-regression.md)
|  | **生成和评估模型**: | |
| 训练   | 通过算法运行数据。 | [训练模型](train-model.md)  <br/> [训练聚类模型](train-clustering-model.md)    |
| 评估模型 | 度量定型模型的准确性。 |  [评估模型](evaluate-model.md)
| 分数 | 从您刚训练的模型中获取预测。 | [应用转换](apply-transformation.md)<br/>[将&nbsp;数据&nbsp;分配到&nbsp;群集](assign-data-to-clusters.md) <br/>[评分模型](score-model.md)

## <a name="error-messages"></a>错误消息

了解在 Azure 机器学习服务的视觉对象界面中可能会遇到的[错误消息和异常代码](machine-learning-module-error-codes.md)。
