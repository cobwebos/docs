---
title: 添加列：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“添加列”模块连接两个数据集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 17882581c4548996c9a8b2a8670b5f720e64da1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898999"
---
# <a name="add-columns-module"></a>“添加列”模块

本文介绍 Azure 机器学习设计器中的模块。

使用此模块连接两个数据集。 将指定为输入的两个数据集中的所有列合并，以便创建一个数据集。 如果需要连接两个以上的数据集，请使用“添加列”的多个实例。 



## <a name="how-to-configure-add-columns"></a>如何配置“添加列”
1. 将“添加列”模块添加到管道。 

2. 连接要连接的两个数据集。 如果要合并两个以上的数据集，可以将多个“添加列”的组合链接起来。 

    - 可以合并具有不同行数的两个列。 对于较小的源列中的每一行，输出数据集中会填充缺失值。

    - 不能选择要添加的单个列。 使用“添加列”时，将连接每个数据集中的所有列。  因此，如果只想添加列的一个子集，请使用“选择数据集中的列”来创建包含所需列的数据集。

3. 提交管道。

### <a name="results"></a>结果
管道运行后：

- 若要查看新数据集的头几行，请右键单击“添加列”模块，然后选择“可视化”。  或者选择模块并切换到右侧面板中的“输出”  选项卡，单击**端口输出**中的直方图图标以直观显示结果。

新数据集中的列数等于两个输入数据集的列数之和。

如果输入数据集中存在两个名称相同的列，则会向该列的名称添加一个数字后缀。 例如，如果有两个名为 TargetOutcome 的列的实例，则左列将重命名为 TargetOutcome_1，右列将重命名为 TargetOutcome_2。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 