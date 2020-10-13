---
title: 对 Vowpal Wabbit 模型评分
titleSuffix: Azure Machine Learning
description: 了解如何使用“Vowpal Wabbit 评分模型”模块，借助经过训练的现有 Vowpal Wabbit 模型为一组输入数据生成分数。
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 23dfee7b78f2606c54525391e1260af69a9b0779
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898408"
---
# <a name="score-vowpal-wabbit-model"></a>对 Vowpal Wabbit 模型评分
本文介绍如何使用 Azure 机器学习设计器中的 **评分 Vowpal Wabbit** 模块，使用现有的定型 Vowpal Wabbit 模型生成一组输入数据的分数。  

本模块提供 Vowpal Wabbit 框架的最新版本 - 版本 8.8.1。 使用此模块，借助以 VW 版本 8 格式保存的经过训练的模型对数据进行评分。  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>如何配置“评分 Vowpal Wabbit 模型”

1.  将“评分 Vowpal Wabbit 模型”模块添加到试验。  
  
2.  添加经过训练的 Vowpal Wabbit 模型，并将其连接到左侧输入端口。 可以使用在同一试验中创建的训练模型，或在设计器的左侧导航窗格的“数据集”类别中找到保存的模型。 但是，模型必须在 Azure 机器学习设计器中可用。  
  
    > [!NOTE]
    > 仅支持 Vowpal Wabbit 8.8.1 模型；无法连接使用其他算法训练的已保存模型。
  
3.  添加测试数据集，并将其连接到右侧输入端口。 如果测试数据集是一个包含测试数据文件的目录，请使用“测试数据文件的名称”指定测试数据文件名。 如果测试数据集是一个文件，请将测试数据文件的名称保留为空。

4. 在 VW 参数文本框中，键入一组有效的 Vowpal Wabbit 可执行文件命令行参数。  

    有关 Azure 机器学习中支持和不支持哪些 Vowpal Wabbit 参数的信息，请参阅[技术说明](#technical-notes)部分。  

5.  **测试数据文件的名称**：键入包含输入数据的文件的名称。 此参数仅在测试数据集是目录时使用。

6. **指定文件类型**：指示训练数据使用的格式。 Vowpal Wabbit 支持以下两种输入文件格式：  

   - VW 表示 Vowpal Wabbit 使用的内部格式。 有关详细信息，请参阅 [Vowpal Wabbit Wiki 页面](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)。 
   - SVMLight 是某些其他机器学习工具使用的格式。 

7. 如果要将标签与分数一起输出，请选择选项“包括含标签的额外列”。  

   通常，在处理文本数据时，Vowpal Wabbit 不需要标签，并且仅返回每行数据的分数。  

8. 如果要将原始分数和结果与一起输出，请选择选项“包括含原始分数的额外列”。  

9. 提交管道。

## <a name="results"></a>结果

在训练完成后：

+ 若要直观显示结果，请右键单击[评分 Vowpal Wabbit 模型](score-vowpal-wabbit-model.md)模块的输出。 输出会显示一个标准化为 0 到 1 范围内数字的预测分数。 

+ 若要计算结果，输出数据集应包含符合评估模型模块要求的特定分数列名称。

  + 对于回归任务，要计算的数据集必须设一个列，名为 `Regression Scored Labels`，表示评分标签。
  + 对于二进制分类任务，要计算的数据集必须具有两列，名为 `Binary Class Scored Labels`、`Binary Class Scored Probabilities`，分别表示评分标签和概率。
  + 对于多分类任务，要计算的数据集必须设一个列，名为 `Multi Class Scored Labels`，表示评分标签。

  注意，无法直接计算“评分 Vowpal Wabbit 模型”的结果。 在计算之前，应根据上述要求修改数据集。

##  <a name="technical-notes"></a>技术说明

本部分包含实现详情、使用技巧和常见问题解答。

### <a name="parameters"></a>参数

Vowpal Wabbit 具有许多用于选择和优化算法的命令行选项。 这里不对这些选项做全面介绍；建议查看 [Vowpal Wabbit wiki 页面](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)。  

在 Azure 机器学习工作室（经典）中，不支持以下参数。  

-   [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments) 中指定的输入/输出选项  
  
     模块已自动配置这些属性。  
  
-   此外，不允许任何生成多个输出或接受多个输入的选项。 这些选项包括 *`--cbt`* 、 *`--lda`* 和 *`--wap`* 。  
  
-   仅支持监督式学习算法。 无法使用这些选项： *`–active`* 、`--rank`、 *`--search`* 等。  

上述参数以外的其他所有参数都可以使用。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 