---
title: 对 Vowpal Wabbit 模型评分
titleSuffix: Azure Machine Learning
description: 了解如何使用评分 Vowpal Wabbit 模块为一组输入数据生成分数，使用现有的定型 Vowpal Wabbit 模型。
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 02f4fe4f97d3e976675757835e3931666d1c6410
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857727"
---
# <a name="score-vowpal-wabbit-model"></a>对 Vowpal Wabbit 模型评分
本文介绍如何使用 Azure 机器学习设计器（预览版）中的**评分 Vowpal Wabbit**模块，通过现有的定型 Vowpal Wabbit 模型为一组输入数据生成评分。  

此模块提供最新版本的 Vowpal Wabbit framework 版本8.8.1。 使用此模块可通过以 VW 版本8格式保存的定型模型对数据进行评分。  

## <a name="how-to-configure-score-vowpal-wabbit-model"></a>如何配置评分 Vowpal Wabbit 模型

1.  向试验中添加**评分 Vowpal Wabbit**模块。  
  
2.  添加训练的 Vowpal Wabbit 模型，并将其连接到左侧输入端口。 您可以使用在同一试验中创建的训练模型，或在设计器左侧导航窗格的 "**数据集**" 类别中找到保存的模型。 但是，模型必须在 Azure 机器学习设计器中可用。  
  
    > [!NOTE]
    > 仅支持 Vowpal Wabbit 8.8.1 模型;不能连接使用其他算法训练的已保存模型。
  
3.  添加测试数据集，并将其连接到右侧输入端口。 如果测试数据集是包含测试数据文件的目录，请使用**测试数据文件名称**指定测试数据文件名称。 如果测试数据集是单个文件，请将**测试数据文件的名称**保留为空。

4. 在 " **VW 参数**" 文本框中，为 Vowpal Wabbit 可执行文件键入一组有效的命令行参数。  

    有关 Azure 机器学习中支持和不支持哪些 Vowpal Wabbit 参数的信息，请参阅[技术说明](#technical-notes)部分。  

5.  **测试数据文件的名称**：键入包含输入数据的文件的名称。 仅当测试数据集是目录时才使用此参数。

6. **指定文件类型**：指示定型数据使用的格式。 Vowpal Wabbit 支持以下两种输入文件格式：  

   - **VW**表示 Vowpal Wabbit 使用的内部格式。 有关详细信息，请参阅[Vowpal Wabbit wiki 页](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)。 
   - **SVMLight**是其他一些机器学习工具使用的格式。 

7. 如果要将标签与分数一起输出，请选择 "**包含标签的额外列**" 选项。  

   通常，在处理文本数据时，Vowpal Wabbit 不需要标签，并且将仅返回每个数据行的分数。  

8. 如果要将原始分数与结果一起输出，请选择 "**包含原始分数的额外列**" 选项。  

9. 提交管道。

## <a name="results"></a>结果

在训练完成后：

+ 若要直观显示结果，请右键单击[评分 Vowpal Wabbit](score-vowpal-wabbit-model.md)模块的输出。 输出指示从0到1的预测分数。 

+ 若要评估结果，输出数据集应包含符合评估模型模块要求的特定分数列名称。

  + 对于回归任务，要评估的数据集必须有一个名为的列 `Regression Scored Labels` ，该列表示评分标签。
  + 对于二元分类任务，要计算的数据集必须具有两个名为的列， `Binary Class Scored Labels` `Binary Class Scored Probabilities` 分别表示评分标签和概率。
  + 对于多分类任务，要评估的数据集必须有一个名为的列 `Multi Class Scored Labels` ，该列表示评分标签。

  请注意，无法直接计算评分 Vowpal Wabbit 模型的结果。 在计算之前，应根据上述要求修改数据集。

##  <a name="technical-notes"></a>技术说明

本部分包含实现详情、使用技巧和常见问题解答。

### <a name="parameters"></a>参数

Vowpal Wabbit 提供了许多用于选择和优化算法的命令行选项。 此处不能全面讨论这些选项;建议查看[Vowpal Wabbit wiki 页](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)。  

在 Azure 机器学习 Studio （经典）中，不支持以下参数。  

-   中指定的输入/输出选项[https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     模块已自动配置这些属性。  
  
-   此外，不允许任何生成多个输出或使用多个输入的选项。 其中包括 *`--cbt`* 、 *`--lda`* 和 *`--wap`* 。  
  
-   仅支持监督学习算法。 这不允许这些选项： *`–active`* 、等 `--rank` *`--search`* 。  

不允许使用以上所述的所有参数。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 