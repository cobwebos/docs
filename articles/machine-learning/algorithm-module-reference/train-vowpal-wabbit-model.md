---
title: 训练 Vowpal Wabbit 模型
titleSuffix: Azure Machine Learning
description: 了解如何使用“训练 Vowpal Wabbit 模型”模块，以通过 Vowpal Wabbit 实例创建一个机器学习模型。
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 07/02/2020
ms.openlocfilehash: 6bc9f69440be772910ea8200b5ccf7d5a5122ae6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907794"
---
# <a name="train-vowpal-wabbit-model"></a>训练 Vowpal Wabbit 模型
本文介绍如何使用 Azure 机器学习设计器中的 " **定型 Vowpal Wabbit 模型** " 模块，通过 Vowpal Wabbit 创建机器学习模型。  

若将 Vowpal Wabbit 用于机器学习，请按照 Vowpal Wabbit 要求将输入格式化，并按照必需的格式准备数据。 使用此模块可指定 Vowpal Wabbit 命令行参数。 

在管道运行时，Vowpal Wabbit 的实例将连同指定数据一起加载到实验运行时。 训练完成后，模型会被序列化回工作区。 你可以立即使用该模型对数据进行评分。 

若要根据新数据对现有模型进行增量训练，将保存的模型连接到“训练 Vowpal Wabbit 模型”的“预训练 Vowpal Wabbit 模型”输入端口，然后将新数据添加到另一个输入端口 。  

## <a name="what-is-vowpal-wabbit"></a>什么是 Vowpal Wabbit？  

Vowpal Wabbit (VW) 是一个快速的并行机器学习框架，由 Yahoo! 开发以用于分布式计算 研究。 后来移植到了 Windows，并由 John Langford (Microsoft Research) 改写，以便在并行体系结构中进行科学计算。  

对机器学习很重要的 Vowpal Wabbit 功能包括持续学习（在线学习）、维数约简和交互式学习。 如果你无法将数据拟合到内存中，Vowpal Wabbit 也能够解决你的问题。  

Vowpal Wabbit 的主要用户是数据科学家，他们以前曾将该框架用于机器学习任务，例如分类、回归、主题建模或矩阵因子分解。 用于 Vowpal Wabbit 的 Azure 包装器具有与其本地版本非常相似的性能特征，因此你可以使用 Vowpal Wabbit 的强大功能和本机性能，并轻松将已训练的模型作为可操作的服务发布。  

[特征哈希](feature-hashing.md)模块也包括由 Vowpal Wabbit 提供的功能，使你可以使用哈希算法将文本数据集转换为二进制特征。  

## <a name="how-to-configure-vowpal-wabbit-model"></a>如何配置 Vowpal Wabbit 模型  

本节介绍如何训练新模型，以及如何将新数据添加到现有模型中。

与设计器中的其他模块不同，该模块既指定模块参数，又训练模型。 如果你有一个现有模型，则可将其添加为可选输入以对该模型进行增量训练。

+ [按一种必需的格式准备输入数据](#prepare-the-input-data)
+ [训练新模型](#create-and-train-a-vowpal-wabbit-model)
+ [对现有模型进行增量训练](#retrain-an-existing-vowpal-wabbit-model)

### <a name="prepare-the-input-data"></a>对输入数据进行准备

若要使用此模块训练模型，输入数据集必须由采用以下两种支持格式之一的单一文本列组成：SVMLight 或 VW . 这并不表示 Vowpal Wabbit 只分析文本数据，只是特征和值必须按必需的文本文件格式进行准备。  

可以从两种类型的数据集中读取数据：文件数据集或表格数据集。 这两种数据集必须采用 SVMLight 或 VW 格式。 Vowpal Wabbit 数据格式的优点它不需要以分列格式，处理稀疏数据时节省空间。 有关此格式的详细信息，请参阅 [Vowpal Wabbit Wiki 页面](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)。  

### <a name="create-and-train-a-vowpal-wabbit-model"></a>创建和训练 Vowpal Wabbit 模型

1. 将“训练 Vowpal Wabbit 模型”模块添加至试验。 
  
2. 添加训练数据集并将其连接到训练数据。 如果训练数据集是一个包含训练数据文件的目录，请使用“训练数据文件的名称”指定训练数据文件名。 如果训练数据集是单个文件，请将“训练数据文件的名称”保留为空。

3. 在“VW 参数”文本框中，键入 Vowpal Wabbit 可执行文件的命令行参数。

     例如，可以添加 `–l` 来指定学习速率，或者添加 `-b` 来指示哈希位数 。  

     有关详细信息，请参阅 [Vowpal Wabbit 参数](#supported-and-unsupported-parameters)一节。  

4. 训练数据文件的名称：键入包含输入数据的文件的名称。 此参数仅在训练数据集是目录时使用。

5. 指定文件类型：指示训练数据使用的格式。 Vowpal Wabbit 支持以下两种输入文件格式：  

    - VW 表示 Vowpal Wabbit 使用的内部格式。 有关详细信息，请参阅 [Vowpal Wabbit Wiki 页面](https://github.com/JohnLangford/vowpal_wabbit/wiki/Input-format)。 
    - SVMLight 是其他一些机器学习工具使用的格式。 

6. 输出可读模型文件：如果希望模块将可读模型保存到运行记录中，请选择该选项。 此参数对应于 VW 命令行中的 `--readable_model` 参数。  

7. 输出反向哈希文件：如果希望模块将反向哈希函数保存到运行记录中的一个文件中，请选择该选项。 此参数对应于 VW 命令行中的 `--invert_hash` 参数。  

8. 提交管道。

### <a name="retrain-an-existing-vowpal-wabbit-model"></a>重新训练现有的 Vowpal Wabbit 模型

Vowpal Wabbit 通过向现有模型中添加新数据来支持增量训练。 可通过两种方法获取现有的模型以进行重新训练：

+ 使用同一管道中另一个“训练 Vowpal Wabbit 模型”模块的输出。  
  
+ 在设计器左侧导航窗格的“数据集”类别中找到一个已保存模型，并将其拖动到管道中。  

1. 将“训练 Vowpal Wabbit 模型”模块添加到管道。  
2. 将以前已训练的模型连接到模块的“预训练 Vowpal Wabbit 模型”输入端口。
3. 将新的训练数据连接到模块的“训练数据”输入端口。
4. 在“训练 Vowpal Wabbit 模型”的参数窗格中，指定新的训练数据的格式，如果输入的数据集是一个目录，也要指定训练数据文件名。
5. 如果需要在运行记录中保存对应的文件，请选择“输出可读模型文件”和“输出反向哈希文件”选项。

6. 提交管道。  
7. 选择该模块并选择右窗格中“输出 + 日志”选项卡下的“注册数据集”，以在 Azure 机器学习工作区中保留更新后的模型 。  如果你不指定新名称，则更新后的模型将覆盖现有的已保存模型。

## <a name="technical-notes"></a>技术说明

本部分包含实现详情、使用技巧和常见问题解答。

### <a name="advantages-of-vowpal-wabbit"></a>Vowpal Wabbit 的优势

Vowpal Wabbit 通过非线性特征（如 n 元语法）提供极快的学习。  

Vowpal Wabbit 使用*在线学习*技术，如随机梯度下降 (SGD)，以一次一条记录的形式拟合模型。 因此，它将循环访问原始数据的速度非常快，并可以比大多数其他模型更快地开发的最佳预测因子。 这种方法也可避免让所有训练数据读入内存。  

Vowpal Wabbit 将所有数据都转换为哈希值，而不仅仅是文本数据，但其他类别的变量。 使用哈希可以更有效地查找回归权重，这对于有效的随机梯度下降而言至关重要。  

###  <a name="supported-and-unsupported-parameters"></a>支持的和不支持的参数 

本节介绍 Azure 机器学习设计器中对 Vowpal Wabbit 命令行参数的支持。 

通常，所有参数（除有限的一组参数外）均受支持。 有关参数的完整列表，请参阅 [Vowpal Wabbit Wiki 页面](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments)。    

不支持以下参数：

-   [https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments](https://github.com/JohnLangford/vowpal_wabbit/wiki/Command-line-arguments) 中指定的输入/输出选项  
  
     模块已自动配置这些属性。  
  
-   此外，不允许任何生成多个输出或接受多个输入的选项。 这些选项包括 `--cbt`、`--lda` 和 `--wap`  。  
  
-   仅支持监督式学习算法。 因此，这些选项不受支持：`–active`、`--rank`、`--search` 等 。 

### <a name="restrictions"></a>限制

由于该服务的目标是支持经验丰富的 Vowpal Wabbit 用户，因此必须使用 Vowpal Wabbit 本机文本格式（而不是其他模块使用的数据集格式）提前准备输入数据。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
