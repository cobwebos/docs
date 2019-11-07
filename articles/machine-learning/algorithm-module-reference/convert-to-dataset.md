---
title: 转换为数据集：模块引用
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure 机器学习服务中的 "转换为数据集" 模块将数据输入转换为 Microsoft Azure 机器学习使用的内部数据集格式。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 81eb71da43623f6478e267f55d6576789d494f9b
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73717253"
---
# <a name="convert-to-dataset"></a>转换为数据集

本文介绍如何使用 Azure 机器学习设计器（预览版）中的 "转换为数据集" 模块将管道的任何数据转换为设计器的内部格式。
  
大多数情况下不需要转换。 在对数据执行任何操作时，Azure 机器学习将数据隐式转换为其本机数据集格式。 

如果您对一组数据执行了某种标准化或清理，并且您希望确保在其他管道中使用这些更改，我们建议将数据保存到数据集格式。  
  
> [!NOTE]
> "转换为数据集" 只会更改数据的格式。 它不会在工作区中保存数据的新副本。 若要保存数据集，请双击输出端口，选择 "**另存为数据集**"，然后输入新名称。  
  
## <a name="how-to-use-convert-to-dataset"></a>如何使用转换为数据集  

建议在使用 "转换为数据集" 之前，使用 "[编辑元数据](edit-metadata.md)" 模块来准备数据集。 您可以添加或更改列名、调整数据类型，并根据需要进行其他更改。

1.  向管道添加 "转换为数据集" 模块。 您可以在设计器的 "**数据转换**" 类别中找到此模块。 

2. 将其连接到输出数据集的任何模块。   

    只要数据是[表格](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)形式，就可以将其转换为数据集。 这包括通过[导入数据](import-data.md)加载的数据、[手动输入数据](enter-data-manually.md)创建的数据或通过[应用转换](apply-transformation.md)转换的数据集。

3.  在 "**操作**" 下拉列表中，指示是否要在保存数据集之前对数据执行任何清理操作：  
  
    - **无**：按原样使用数据。  
  
    - **SetMissingValue**：将特定值设置为数据集中的缺失值。 默认占位符是问号字符（？），但您可以使用 "**自定义缺失值**" 选项来输入不同的值。 例如，如果为**自定义缺失值**输入**出租车**，则数据集中的**出租车**的所有实例都将更改为缺少的值。
  
    - **ReplaceValues**：使用此选项可以指定要替换为任何其他确切值的单个精确值。 可以通过设置**replace**方法替换缺失值或自定义值：

      - **缺少**：选择此选项以替换输入数据集中的缺失值。 对于 "**新值**"，请输入要用来替换缺失值的值。
      - **自定义**：选择此选项以替换输入数据集中的自定义值。 对于 "**自定义值**"，输入要查找的值。 例如，如果数据包含用作缺失值占位符的字符串 `obs` 则输入 `obs`。 对于 "**新值**"，请输入新值以将原始字符串替换为。
  
    请注意， **ReplaceValues**操作仅适用于完全匹配项。 例如，这些字符串不会受到影响： `obs.`，`obsolete`。  
 
  
5.  运行管道，或右键单击 "转换为数据集" 模块，然后选择 "**运行选定**项"。  

## <a name="results"></a>结果

+  若要使用新名称保存生成的数据集，请右键单击 "转换为数据集" 的输出，然后选择 "**另存为数据集**"。  
  
## <a name="technical-notes"></a>技术说明  

-   将数据集作为输入的任何模块还可以在 CSV 文件或 TSV 文件中使用数据。 在运行任何模块代码之前，将对输入进行预处理。 预处理等效于对输入运行 "转换为数据集" 模块。  
  
-   无法从 SVMLight 格式转换为数据集。  
  
-   当你指定自定义替换操作时，搜索和替换操作将应用于完成值。 不允许使用部分匹配项。 例如，可以将3替换为-1 或使用33，但不能替换两位数（如35）中的3。  
  
-   对于自定义替换操作，如果你使用替换不符合列的当前数据类型的任何字符，则替换将以静默方式失败。  

  
## <a name="next-steps"></a>后续步骤

查看可用于 Azure 机器学习服务[的模块集](module-reference.md)。 
