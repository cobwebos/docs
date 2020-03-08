---
title: 算法和模块参考
description: 了解 Azure 机器学习设计器中提供的模块（预览）
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 02/22/2020
ms.openlocfilehash: 334cf69db70e8a1e0c2994c492f15ff44505af0d
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673368"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Azure 机器学习设计器的算法 & 模块参考（预览版）

此参考内容在 Azure 机器学习设计器（预览版）中提供的每个机器学习算法和模块上都提供了技术背景。

每个模块表示一组可以独立运行的代码，并根据所需的输入执行机器学习任务。 模块可能包含特定的算法，或者可能执行在机器学习中非常重要的任务，如替换缺少的值或进行统计分析。

有关选择算法的帮助，请参阅 
* [如何选择算法](../how-to-select-algorithms.md)
* [Azure 机器学习算法备忘单](../algorithm-cheat-sheet.md)

> [!TIP]
> 在设计器的任何管道中，可以获取有关特定模块的信息。 选择该模块，然后在“快速帮助”窗格中选择“更多帮助”链接。

## <a name="data-preparation-modules"></a>数据准备模块


| 功能 | 说明 | 模块 |
| --- |--- | --- |
| 数据输入和输出 | 将数据从云源移动到管道。 运行管道时，将结果或中间数据写入到 Azure 存储、SQL 数据库或 Hive，或使用云存储在管道之间交换数据。  | [手动输入数据](enter-data-manually.md) <br/> [导出数据](export-data.md) <br/> [导入数据](import-data.md) |
| 数据转换 | 对机器学习独有的数据进行的操作，如规范化或装箱数据、维度缩减以及在各种文件格式之间转换数据。| [添加列](add-columns.md) <br/> [添加行](add-rows.md) <br/> [应用数学运算](apply-math-operation.md) <br/> [应用 SQL 转换](apply-sql-transformation.md) <br/> [清理缺失数据](clean-missing-data.md) <br/> [剪辑值](clip-values.md) <br/> [转换为 CSV](convert-to-csv.md) <br/> [转换为数据集](convert-to-dataset.md) <br/> [转换为指示器值](convert-to-indicator-values.md) <br/> [编辑元数据](edit-metadata.md) <br/> [联接数据](join-data.md) <br/> [规范化数据](normalize-data.md) <br/> [分区和示例](partition-and-sample.md)  <br/> [删除重复行](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [选择列转换](select-columns-transform.md) <br/> [选择数据集中的列](select-columns-in-dataset.md) <br/> [拆分数据](split-data.md) |
| 功能选择 | 选择用于生成分析模型的相关、有用功能的子集。 | [基于筛选器的功能选择](filter-based-feature-selection.md) <br/> [排列特征重要性](permutation-feature-importance.md) |
| 统计函数 | 提供各种与数据科学相关的统计方法。 | [汇总数据](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>机器学习算法

| 功能 | 说明 | 模块 |
| --- |--- | --- |
| 回归 | 预测值。 | [提升决策树回归](boosted-decision-tree-regression.md) <br/> [决策林回归](decision-forest-regression.md) <br/> [线性回归](linear-regression.md)  <br/> [神经网络回归](neural-network-regression.md)  <br/> |
| 群集 | 将数据组合在一起。| [K 平均值聚类](k-means-clustering.md)
| 分类 | 预测类。  从二进制（两类）或多类算法中进行选择。| [多类提升决策树](multiclass-boosted-decision-tree.md) <br/> [多类决策林](multiclass-decision-forest.md) <br/> [多类逻辑回归](multiclass-logistic-regression.md)  <br/> [多类神经网络](multiclass-neural-network.md) <br/> [One 和 All 多类](one-vs-all-multiclass.md) <br/> [双类平均感知器](two-class-averaged-perceptron.md) <br/>  [双类提升决策树](two-class-boosted-decision-tree.md)  <br/> [双类决策林](two-class-decision-forest.md) <br/>  [双类逻辑回归](two-class-logistic-regression.md) <br/> [双类神经网络](two-class-neural-network.md) <br/> [两类支持向量机](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>用于生成和评估模型的模块

| 功能 | 说明 | 模块 |
| --- |--- | --- |
| 模型定型 | 通过算法运行数据。 |  [训练聚类模型](train-clustering-model.md) <br/> [训练模型](train-model.md)  <br/> [优化模型超参数](tune-model-hyperparameters.md) |
| 模型评分和评估 | 度量定型模型的准确性。 | [应用转换](apply-transformation.md) <br/> [将数据分配到群集](assign-data-to-clusters.md) <br/> [交叉验证模型](cross-validate-model.md) <br/> [评估模型](evaluate-model.md) <br/> [评分模型](score-model.md) |
| Python 语言 | 编写代码，并将其嵌入到模块中，以便将 Python 与管道集成。 | [创建 Python 模型](create-python-model.md) <br/> [执行 Python 脚本](execute-python-script.md) |
| R 语言 | 编写代码，并将其嵌入到模块中，以便将 R 与管道集成。 | [执行 R 脚本](execute-r-script.md) |
| 文本分析 | 提供专门用于处理结构化和非结构化文本的计算工具。 | [从文本提取 N 元语法特征](extract-n-gram-features-from-text.md) <br/> [功能哈希](feature-hashing.md) <br/> [预处理文本](preprocess-text.md) |
| 建议 | 生成建议模型。 | [评估推荐器](evaluate-recommender.md) <br/> [评分 SVD 推荐器](score-svd-recommender.md) <br/> [训练 SVD 推荐器](train-SVD-recommender.md) |


## <a name="error-messages"></a>错误消息

了解在 Azure 机器学习设计器中可能会遇到的[错误消息和异常代码](designer-error-codes.md)。

## <a name="next-steps"></a>后续步骤

* [教程：在设计器中生成模型以预测自动价格](../tutorial-designer-automobile-price-train-score.md)
