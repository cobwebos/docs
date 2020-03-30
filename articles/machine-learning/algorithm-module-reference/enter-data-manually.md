---
title: 手动输入数据：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“手动输入数据”模块通过键入值来创建小型数据集。 该数据集可以有多个列。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 35e31e5ace53654e8aad794dd3e25fc04bd9a088
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367510"
---
# <a name="enter-data-manually-module"></a>“手动输入数据”模块

本文介绍 Azure 机器学习设计器（预览版）中的一个模块。

使用 **"手动输入数据"** 模块通过键入值来创建小型数据集。 该数据集可以有多个列。
  
此模块在以下情况下非常有用：  
  
- 生成一小组值进行测试。  
- 创建标签的简短列表。  
- 键入要插入数据集的列名称列表。

## <a name="create-a-dataset"></a>创建数据集 
  
1. 将[手动输入数据](./enter-data-manually.md)模块添加到管道。 可以在 Azure 机器学习的**数据输入和输出**类别中找到此模块。 
  
1. 对于“DataFormat”****，选择以下选项之一。 这些选项决定了应该如何分析你提供的数据。 每种格式的要求差别很大，因此请务必阅读相关主题。  
  
   - **ARFF**：韦卡使用的属性关系文件格式。   
   - **CSV**：逗号分隔值格式。 有关详细信息，请参阅[转换为 CSV](./convert-to-csv.md)。    
   - **SVMLight**：Vowpal Wabbit 和其他机器学习框架使用的格式。    
   - **TSV**：选项卡分隔值格式。

   如果选择格式，但不提供符合格式规范的数据，则会发生运行时错误。
  
1. 在“数据”**** 文本框内单击以开始输入数据。 以下格式需要特别注意：  
  
   - **CSV**： 要创建多个列，请粘贴逗号分隔的文本，或使用字段之间的逗号键入多个列。
  
     如果选择“HasHeader”**** 选项，则可以使用第一行值作为列标题。  
  
     如果取消选择此选项，将使用列名称（Col1、Col2 等）。 以后可以使用["编辑元数据"](./edit-metadata.md)添加或更改列名称。  
  
   - **TSV**： 要创建多个列，请粘贴与选项卡分隔的文本，或使用字段之间的选项卡键入多个列。  
  
     如果选择“HasHeader”**** 选项，则可以使用第一行值作为列标题。  
  
     如果取消选择此选项，将使用列名称（Col1、Col2 等）。 以后可以使用["编辑元数据"](./edit-metadata.md)添加或更改列名称。  
  
   - **ARFF**： 粘贴到现有的 ARFF 格式文件中。 如果直接键入值，请确保在数据开头添加可选标头和所需属性字段。 

     例如，可以将以下标头和属性行添加到简单列表中。 列标题将是 `SampleText`。 请注意，不支持字符串类型。
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**：使用 SVMLight 格式键入或粘贴值。  
  
     例如，以下示例以 SVMLight 格式表示献血数据集的前两行：  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     运行[手动输入数据](./enter-data-manually.md)模块时，这些行将转换为列和索引值的数据集，如下所示：  
  
     |Col1|Col2|Col3|Col4|标签|  
     |-|-|-|-|-|  
     |0.00016|0.004|0.999961|0.00784|1|  
     |0|0.004|0.999955|0.008615|1|  
  
1. 选择每行之后的 Enter 键，以启动新行。      
     
   如果选择"输入多次"以添加多个空尾随行，则空行将被删除或修剪。  
  
   如果创建包含缺失值的行，则稍后随时可以将其筛选出来。  
  
1. 将输出端口连接到其他模块，并运行管道。  
  
   要查看数据集，请右键单击模块并选择 **"可视化**"。

## <a name="next-steps"></a>后续步骤

参阅 Azure 机器学习[可用的模块集](module-reference.md)。 