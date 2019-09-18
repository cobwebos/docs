---
title: 深度学习与机器学习
titleSuffix: Azure Machine Learning
description: 了解深度学习与机器学习以及这两个概念与人工智能的关系。 可在欺诈检测、语音和面部识别、情绪分析和时序预测等方案中应用深度学习。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: cff31916f837141ae54f3c14dd125be6a92a5008
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035505"
---
# <a name="deep-learning-vs-machine-learning"></a>深度学习与机器学习

本文可帮助你比较深度学习与机器学习。 你将了解两个概念是如何进行比较的，以及如何将其调整为更广泛的人工智能。 本文还介绍了如何将深度学习应用于真实方案，如欺诈检测、语音和面部识别、情绪分析以及时序预测。

## <a name="deep-learning-machine-learning-and-ai"></a>深度学习、机器学习和 AI

![关系图：AI 与机器学习和深度学习](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

请考虑以下定义来了解深度学习与机器学习与AI

- **深度学习**是一种基于人工神经网络的机器学习的子集。 _学习过程_是_深度_的，因为人工神经网络的结构包含多个输入、输出和隐藏层。 每个层都包含将输入数据转换为下一层可用于特定预测任务的信息的单位。 由于这种结构，计算机可以通过其自身的数据处理来了解。

- **机器学习**是一种使用技术（如深度学习）的人工智能，使计算机能够使用经验来改进任务。 _学习过程_基于以下步骤：

   1. 将数据送入算法。 （在此步骤中，您可以向模型提供其他信息，例如，通过执行功能提取。）
   1. 使用此数据训练模型。
   1. 测试和部署模型。
   1. 使用部署的模型来执行自动化预测任务。 （换言之，调用并使用部署的模型来接收模型返回的预测。）

- **人工智能（AI）** 是使计算机能够模拟人类智能的一项技术。 它包括机器学习。 
 
了解 AI、机器学习和深度学习之间的关系非常重要。 机器学习是实现人工智能的一种方法。 通过使用机器学习和深度学习技术，您可以生成通常与人类情报相关的任务的计算机系统和应用程序。 这些任务包括视觉感知、语音识别、决策和语言翻译。

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>深度学习和机器学习的技术 

现在，你已了解机器学习和深度学习，接下来请比较这两种方法。 在机器学习中，需要告知算法如何使用详细信息（例如，通过执行特征提取）进行准确预测。 在深度学习中，该算法可以了解如何通过其自身的数据处理进行准确预测，这是一种非常得益的神经网络结构。

下表更详细地比较了这两种方法：

| |所有机器学习 |仅深度学习|
|---|---|---|
|  **数据点的数目** | 可以使用用户提供的小数据量。 | 需要大量训练数据，才能获得简明结论。 |
|  **硬件依赖项** | 可在低端计算机上工作。 它不需要大量的计算能力。 | 取决于高端计算机。 它本身会执行大量的矩阵乘法运算。 GPU 可以有效地优化这些操作。 |
|  **特征化进程** | 需要能够准确地标识和创建用户的功能。 | 学习数据的高级功能，并自行创建新功能。 |
|  **学习方法** | 将任务划分为小部分，然后将接收的结果合并为一个结论。 | 以端到端的方式解决问题。 |
|  **执行时间** | 需要相对较少的时间进行训练，范围从几秒钟到几小时。 | 需要特别长的时间进行训练，因为深度学习算法涉及多个参数。 |
|  **输出** | 输出通常是一个数值，如分数或分类。 | 输出可以是文本、分数、元素或声音。 |

## <a name="deep-learning-use-cases"></a>深度学习用例

由于人工神经网络结构，深度学习 transact-sql 识别非结构化数据（如图像、声音、视频和文本）的模式。 出于此原因，深度学习正在快速转换许多行业，包括医疗保健、能源、财务和运输。 这些行业现在反思传统的业务流程。 

以下各段介绍了一些最常见的深度学习应用程序。

### <a name="named-entity-recognition"></a>命名实体识别

深度学习网络的一种用途是命名实体识别，这是一种从非结构化和未标记的数据中提取特定类型信息的方法。 此信息可能是人员、地点、公司或物品。 然后，可以将该信息存储在结构化架构中以生成地址列表，或将其用作标识验证引擎的基准。

### <a name="object-detection"></a>对象检测

深度学习已应用于许多对象检测用例中。 对象检测包括两个部分：图像分类和图像本地化。 图像_分类_标识图像的对象，例如汽车或人脉。 图像_本地化_提供了这些对象的特定位置。 

对象检测已在游戏、零售、观光和自行驾驶汽车等行业中使用。

### <a name="image-caption-generation"></a>生成图像标题

与图像识别一样，在图像字幕中，对于给定图像，系统必须生成描述图像内容的标题。 在照片中检测并标记对象时，下一步是将这些标签转换为描述性连贯的句子。 通常，图像字幕系统使用非常大的卷积神经网络来检测照片中的对象，然后使用重复性神经网络（RNN）将标签转换为连贯的句子。

### <a name="machine-translation"></a>机器翻译

机器翻译使用一种语言的词、短语或句子，并自动将其转换为另一种语言。 自动计算机转换已有很长一段时间，但深度学习在两个具体方面获得了令人印象深刻的结果：自动转换文本（以及从语音转换到文本）以及自动转换图像。 

使用适当的数据转换，深层网络可以了解文本、音频和视觉信号。 计算机翻译可用于识别较大的音频文件中的声音片段，并将口述的单词或图像转录为文本。

### <a name="text-analytics"></a>文本分析

深度学习的一项重要任务是电子发现。 公司使用基于深度学习的文本分析来检测内幕交易以及与政府法规的符合性。 篱资金使用文本分析来向下钻取到大型文档存储库，以深入了解未来的投资性能和市场情绪。 基于深度学习的文本分析的使用案例围绕着分析大量文本数据和执行分析或产出聚合的能力。

## <a name="artificial-neural-networks"></a>人工神经网络

人工神经网络由已连接节点的层构成。 深度学习模型使用具有大量层的神经网络。 

以下各节介绍最常用的人工神经网络 typologies。

### <a name="feedforward-neural-network"></a>Feedforward 神经网络

Feedforward 神经网络是最基本的人工神经网络类型。 在 feedforward 网络中，信息从输入层到输出层只进一个方向。 Feedforward 神经网络通过一系列隐藏层来转换输入。 每一层都由一组神经元组成，每个层都在之前与该层中的所有神经元完全连接。 最后一个完全连接的层（输出层）表示生成的预测。

### <a name="recurrent-neural-network"></a>重复性神经网络

重复性神经网络是一种广泛使用的人工神经网络。 这些网络保存层的输出，并将其送回输入层，以帮助预测层的结果。 重复性神经网络具有出色的学习功能。 它们广泛用于复杂任务，如学习手写和识别语言。

### <a name="convolutional-neural-networks"></a>卷积神经网络

卷积神经网络是特别有效的人工神经网络，它提供了独特的体系结构。 图层分为三个维度：宽度、高度和深度。 一层中的神经元不会连接到下一层中的所有神经元，只连接到层神经元的小区域。 最终输出将缩小为沿深度维度组织的一条概率分数矢量。 

卷积神经网络已在图像识别和分类等区域使用。

## <a name="next-steps"></a>后续步骤

以下文章说明了如何在[Azure 机器学习](https://docs.microsoft.com/azure/machine-learning/service/?WT.mc_id=docs-article-lazzeri)中使用深度学习技术：

- [使用 TensorFlow 模型为手写数字分类](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)
- [使用 TensorFlow 估计器和 Keras 对手写数字进行分类](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-keras?WT.mc_id=docs-article-lazzeri)
- [使用 Pytorch 模型对图像进行分类](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)
- [使用 Chainer 模型为手写数字分类](https://docs.microsoft.com/azure/machine-learning/service/how-to-train-chainer?WT.mc_id=docs-article-lazzeri)
