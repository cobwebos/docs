---
title: 手动输入数据：模块引用
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的 "手动输入数据" 模块通过键入值来创建小型数据集。 数据集可以有多个列。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 61c3dde49d4cd298fb3a0f19dc1d8c00e54977af
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152340"
---
# <a name="enter-data-manually-module"></a>手动输入数据模块

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块通过键入值来创建小型数据集。 数据集可以有多个列。
  
此模块可用于以下方案：  
  
- 生成一小部分值用于测试  
  
- 创建标签的简短列表
  
- 键入要插入到数据集中的列名的列表

## <a name="enter-data-manually"></a>手动输入数据 
  
1.  将 "[手动输入数据](./enter-data-manually.md)" 模块添加到管道。 可以在 Azure 机器学习的 "**数据输入和输出**" 类别中找到此模块。 
  
2.  对于**DataFormat**，请选择以下选项之一。 这些选项确定应该如何分析你提供的数据。 每种格式的要求差别很大，因此请务必阅读相关主题。  
  
    -   **ARFF**。 Weka 使用的属性关系文件格式。   
  
    -   **CSV**。 逗号分隔值格式。 有关详细信息，请参阅[转换为 CSV](./convert-to-csv.md)。  
  
    -   **SVMLight**。 Vowpal Wabbit 和其他机器学习框架使用的格式。  
  
    -   **TSV**。 制表符分隔值格式。

     如果选择格式，而不提供满足格式规范的数据，则会发生运行时错误。
  
3.  单击**数据**文本框内部，开始输入数据。 以下格式需要特别注意：  
  
    - **CSV**：若要创建多个列，请以逗号分隔的文本形式粘贴，或在字段之间使用逗号键入多个列。
  
        如果选择**HasHeader**选项，则可以使用第一行值作为列标题。  
  
        如果取消选择此选项，将使用列名称、Col1、Col2 等。 以后可以使用 "[编辑元数据](./edit-metadata.md)" 来添加或更改列名。  
  
    - **TSV**：若要创建多个列，请粘贴制表符分隔文本，或在字段之间使用制表符键入多个列。  
  
        如果选择**HasHeader**选项，则可以使用第一行值作为列标题。  
  
        如果取消选择此选项，将使用列名称、Col1、Col2 等。 以后可以使用 "[编辑元数据](./edit-metadata.md)" 来添加或更改列名。  
  
    -   **ARFF**：粘贴现有的 ARFF 格式文件。 如果直接键入值，请确保在数据的开头添加可选的 "标题" 和 "必需" 属性字段。 
    
        例如，以下标头和属性行可以添加到简单列表中。 列标题将 `SampleText`。
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**：使用 SVMLight 格式键入或粘贴值。  
  
        例如，下面的示例以 SVMight 格式表示血糖捐赠数据集的前几行：  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        当你运行 "[手动输入数据](./enter-data-manually.md)" 模块时，这些行将转换为列和索引值的数据集，如下所示：  
  
        |Col1|Col2|Col3|Col4|标签|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  在每一行后按 ENTER 以开始新行。  
  
     **请确保在最后一行后按 ENTER 键。** 
     
     如果按 ENTER 多次来添加多个空尾随行，则会删除最终的空行，但会将其他空行视为缺失值。  
  
     如果创建缺少值的行，则可以在以后随时对其进行筛选。  
  
5.  将输出端口连接到其他模块，并运行管道。  
  
     若要查看数据集，请右键单击模块并选择 "**可视化**"。  
## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习[的模块集](module-reference.md)。 