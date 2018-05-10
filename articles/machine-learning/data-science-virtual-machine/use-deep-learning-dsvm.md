---
title: Azure 上的深度学习数据科学虚拟机中的数据科学 | Microsoft Docs
description: 如何通过深度学习数据科学 VM 执行几个常见的数据科学任务。
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 2053ed8cc420183d493097eeb2cd2ad93c82c70c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="using-the-deep-learning-virtual-machine"></a>使用深度学习虚拟机

预配深度学习虚拟机 (DLVM) 后，便可以开始构建深层神经网络模型以生成域中的 AI 应用程序，如计算机视觉和语言理解等。 

深度学习 VM 上针对 AI 提供了多个工具。 [“深度学习和人工智能框架”页](dsvm-deep-learning-ai-frameworks.md)包含有关如何使用这些工具的详细信息。 

## <a name="deep-learning-tutorials-and-walkthroughs"></a>深度学习教程和演练

除了基于框架的示例外，我们还提供了一套已在 DLVM 上验证的综合演练。 这些演练可帮助你快速开始开发域中的深度学习应用程序，如图像和文本/语言理解等。 我们会继续添加更多跨不同域和技术的端到端教程。   


- [在不同的框架中运行神经网络](https://github.com/ilkarman/DeepLearningFrameworks)：一个综合演练，演示如何将代码从一个框架迁移到另一个框架。 它还演示了如何跨框架比较模型和运行时性能。 

- [生成端到端解决方案以检测映像中产品的操作方法指南](https://github.com/Azure/cortana-intelligence-product-detection-from-images)：映像检测是一种技术，可以在映像中查找对象并对其进行分类。 此技术有可能在多个实际生活业务域中带来巨大回报。 例如，零售商可以使用此技术确定客户已从货架上选取哪个产品。 从而，此信息可帮助商店管理产品库存。 

- [从 PubMed 摘要提取命名实体](https://docs.microsoft.com/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition) 本教程介绍如何从非结构化文本中提取命名实体（如药品名称或疾病名称）。 它在 1800 万 PubMed 摘要的文本语料库上修整定制词嵌入，使用该模型生成长短期记忆 (LSTM) 递归神经网络模型用于实体提取，并表明该特定于域的词嵌入模型的性能优于用于实体提取的通用词嵌入模型。

- [深度音频学习](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) 本教程介绍如何在[城市声音数据集](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)中修整用于音频事件检测的深度学习模型，并提供有关如何使用音频数据的概述。

- [文本文档分类](https://github.com/anargyri/lstm_han)：本演练演示如何构建和训练两种不同的神经网络体系结构：分层网络和长短期记忆网络 (LSTM)。 这些神经网络使用用于深度学习的 Keras API 对文本文档进行分类。 Keras 是以下三个最受欢迎的深度学习框架的前端：Microsoft Cognitive Toolkit、TensorFlow 和 Theano。

## <a name="next-steps"></a>后续步骤

[示例页](dsvm-samples-and-walkthroughs.md)提供了有关 VM 上预加载的每个框架的代码示例的信息，可帮助你快速入门。 
