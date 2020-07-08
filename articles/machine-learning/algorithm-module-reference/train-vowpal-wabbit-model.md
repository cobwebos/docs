---
title: 训练 Vowpal Wabbit 模型
titleSuffix: Azure Machine Learning
description: 了解如何使用定型 Vowpal Wabbit 模块通过 Vowpal Wabbit 的实例来创建机器学习模型。
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 34caefba5bae660ca150f6e447b1d59b7174c768
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857726"
---
# <a name="train-vowpal-wabbit-model"></a>训练 Vowpal Wabbit 模型
本文介绍如何使用 Azure 机器学习设计器（预览版）中的 "**定型 Vowpal Wabbit 模型**" 模块，通过 Vowpal Wabbit 创建机器学习模型。  

若要将 Vowpal Wabbit 用于机器学习，请根据 Vowpal Wabbit 要求设置输入的格式，并以所需格式准备数据。 使用此模块可指定 Vowpal Wabbit 命令行参数。 

运行管道时，会将 Vowpal Wabbit 的实例与指定的数据一起加载到试验运行时。 训练完成后，模型将被序列化回工作区。 您可以直接使用该模型对数据进行评分。 

若要以增量方式对新数据的现有模型进行定型，请将已保存的模型连接到**定型 Vowpal Wabbit 模型**的**预先训练的 Vowpal Wabbit 模型**输入端口，并将新数据添加到其他输入端口。  

## <a name="what-is-vowpal-wabbit"></a>什么是 Vowpal Wabbit？  

Vowpal Wabbit （VW）是一个快速的并行机器学习框架，为 Yahoo！的分布式计算开发 研究。 后来移植到了 Windows，并由 John Langford (Microsoft Research) 改写，以便在并行体系结构中进行科学计算。  

适用于机器学习的 Vowpal Wabbit 的功能包括持续学习（联机学习）、维度缩减和交互式学习。 如果你无法将数据拟合到内存中，Vowpal Wabbit 也能够解决你的问题。  

Vowpal Wabbit 的主要用户是以前曾使用过用于机器学习任务（例如分类、回归、主题建模或 matrix 因子分解）的框架的数据科学家。 适用于 Vowpal Wabbit 的 Azure 包装为本地版本提供非常类似的性能特性，因此，你可以使用 Vowpal Wabbit 的强大功能和本机性能，并轻松地将定型模型发布为操作化服务。  

[特征哈希](feature-hashing.md)模块还包括 Vowpal Wabbit 提供的功能，可让你使用哈希算法将文本数据集转换为二进制功能。  

## <a name="how-to-configure-vowpal-wabbit-model"></a>如何配置 Vowpal Wabbit 模型  

本部分介绍如何定型新模型，以及如何将新数据添加到现有模型中。

与设计器中的其他模块不同，此模块均指定模块参数，并训练模型。 如果您具有现有模型，则可以将其作为可选输入添加，以增量方式对模型进行定型。

+ [采用所需的一种格式准备输入数据](#prepare-the-input-data)
+ [为新模型定型](#create-and-train-a-vowpal-wabbit-model)
+ [以增量方式训练现有模型](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>对输入数据进行准备

若要使用此模块为模型定型，输入数据集必须包含采用以下两种受支持的格式之一的单个文本列： **SVMLight**或**VW**。 这并不意味着 Vowpal Wabbit 仅分析文本数据，而只是必须以所需的文本文件格式准备功能和值。  

可以从两种类型的数据集（文件数据集或表格数据集）中读取数据。 这两个数据集都必须为 SVMLight 或 VW 格式。 Vowpal Wabbit 数据格式的优点它不需要以分列格式，处理稀疏数据时节省空间。 有关此格式的详细信息，请参阅[Vowpal Wabbit wiki 页](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)。  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>创建和定型 Vowpal Wabbit 模型

1. 向试验中添加**训练 Vowpal Wabbit**模块。 
  
2. 添加定型数据集并将其连接到**定型数据**。 如果定型数据集是包含定型数据文件的目录，请使用**定型数据文件名称**指定定型数据文件名称。 如果定型数据集是单个文件，请将**定型数据文件的名称**保留为空。

3. 在 " **VW 参数**" 文本框中，键入 Vowpal Wabbit 可执行文件的命令行参数。

     例如，你可以添加 *`–l`* 来指定学习速率，或 *`-b`* 指示哈希位的数量。  

     有关详细信息，请参阅[Vowpal Wabbit parameters](#supported-and-unsupported-parameters)部分。  

4. **定型数据文件的名称**：键入包含输入数据的文件的名称。 仅当定型数据集为目录时，才使用此参数。

5. **指定文件类型**：指示定型数据使用的格式。 Vowpal Wabbit 支持以下两种输入文件格式：  

    - **VW**表示 Vowpal Wabbit 使用的内部格式。 有关详细信息，请参阅[Vowpal Wabbit wiki 页](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)。 
    - **SVMLight**是其他一些机器学习工具使用的格式。 

6. **输出可读模型文件**：如果希望模块将可读模型保存到运行记录中，请选择此选项。 此参数对应于 `--readable_model` VW 命令行中的参数。  

7. **输出反转的哈希文件**：如果希望模块将反转哈希函数保存到运行记录中的一个文件，请选择此选项。 此参数对应于 `--invert_hash` VW 命令行中的参数。  

8. 提交管道。

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>重新训练现有的 Vowpal Wabbit 模型

通过向现有模型添加新数据，Vowpal Wabbit 支持增量培训。 可通过两种方法获取现有模型以进行重新训练：

+ 在同一管道中使用另一个**定型 Vowpal Wabbit 模型**模块的输出。  
  
+ 在设计器左侧导航窗格的 "**数据集**" 类别中找到保存的模型，并将其拖放到管道。  

1. 将**定型 Vowpal Wabbit**模块添加到管道。  
2. 将以前训练的模型连接到模块的**预先训练的 Vowpal Wabbit 模型**输入端口。
3. 将新训练数据连接到模块的**定型数据**输入端口。
4. 在 "**定型 Vowpal Wabbit 模型**" 的 "参数" 窗格中，指定新定型数据的格式，如果输入数据集是目录，则还要指定定型数据文件名称。
5. 如果需要将相应的文件保存在运行记录中，请选择 "输出可读模型文件 * *" 和 "**输出反转的哈希文件**选项"。

6. 提交管道。  
7. 选择该模块，然后在右窗格中选择 "**输出 + 日志**" 选项卡下的 "**注册数据集**"，以在 Azure 机器学习工作区中保留更新后的模型。  如果不指定新名称，则更新后的模型将覆盖现有的已保存模型。

## <a name="technical-notes"></a>技术说明

本部分包含实现详情、使用技巧和常见问题解答。

### <a name="advantages-of-vowpal-wabbit"></a>Vowpal Wabbit 的优点

Vowpal Wabbit 通过非线性特征（如 n 元语法）提供极快的学习。  

Vowpal Wabbit 使用*在线学习*技术，如随机梯度下降 (SGD)，以一次一条记录的形式拟合模型。 因此，它将循环访问原始数据的速度非常快，并可以比大多数其他模型更快地开发的最佳预测因子。 这种方法也可避免让所有训练数据读入内存。  

Vowpal Wabbit 将所有数据都转换为哈希值，而不仅仅是文本数据，但其他类别的变量。 使用哈希可以更有效地查找回归权重，这对于有效的随机梯度下降至关重要。  

###  <a name="supported-and-unsupported-parameters"></a>支持和不支持的参数 

本部分介绍 Azure 机器学习设计器中的 Vowpal Wabbit 命令行参数支持。 

通常，支持除一组有限的参数以外的所有参数。 有关参数的完整列表，请使用[Vowpal Wabbit wiki 页](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)。    

不支持以下参数：

-   中指定的输入/输出选项[https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)  
  
     模块已自动配置这些属性。  
  
-   此外，不允许任何生成多个输出或使用多个输入的选项。 其中包括 *`--cbt`* 、 *`--lda`* 和 *`--wap`* 。  
  
-   仅支持监督学习算法。 因此，不支持这些选项： *`–active`* 、 `--rank` 等 *`--search`* 。 

### <a name="restrictions"></a>限制

由于服务的目标是支持 Vowpal Wabbit 的经验丰富的用户，因此必须使用 Vowpal Wabbit 本机文本格式而不是其他模块使用的数据集格式来提前准备输入数据。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
