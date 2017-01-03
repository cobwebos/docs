---
title: "使用机器学习的信用风险预测解决方案 | Microsoft Docs"
description: "详细演练中将介绍如何创建预测分析解决方案，用于在 Azure 机器学习工作室中进行信用风险评估。"
keywords: "信用风险, 预测分析解决方案, 风险评估"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 43300854-a14e-4cd2-9bb1-c55c779e0e93
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 07cb3fe0d5688d5b63fe3312cad14c2274a58a09
ms.openlocfilehash: e98a64910f28da0a8a9b4a58c717c40d791ccf00


---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>演练：开发预测分析解决方案，用于在 Azure 机器学习中评估信用风险

在本演练中，我们将详细探讨在机器学习工作室中开发解决方案的过程。 我们将在机器学习工作室中开发预测分析模型，然后将其部署为 Azure 机器学习 Web 服务，以便模型在其中使用新数据进行预测。 

> [!TIP]
> 本演练假设用户此前至少使用过机器学习工作室一次，且对机器学习概念有一些了解，虽然不是特别熟悉。
> 
>如果以前从来没用过 **Azure 机器学习工作室**，则可能一开始需要学习[在 Azure 机器学习工作室中创建第一个数据科学试验](machine-learning-create-experiment.md)这一教程。 该教程会对第一次使用机器学习工作室的用户进行指导，介绍各种基础知识：如何将模块拖放到试验中、如何将模块连接到一起、如何运行试验，以及如何查看结果。
>
>如果不熟悉机器学习，可先观看视频系列：[适合初学者的数据科学](machine-learning-data-science-for-beginners-the-5-questions-data-science-answers.md)。 该视频系列使用日常语言和概念对机器学习进行了很好的介绍。
> 

## <a name="the-problem"></a>问题

假设你需要根据他们提供的贷款申请相关信息预测个人的信用风险。  

当然，信用风险评估是一个复杂的问题，但我们将稍微简化一下问题的参数。 然后，我们会使用它作为示例，了解如何使用 Microsoft Azure 机器学习工作室和机器学习 Web 服务来创建此类预测分析解决方案。  

## <a name="the-solution"></a>解决方案

在这个详细的演练中，我们会首先使用公开的信用风险数据，在这些数据的基础之上开发和定型一个预测模型，然后将该模型部署为其他人可用来进行信用风险评估的 Web 服务。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

若要创建信用风险评估解决方案，我们将遵循以下步骤︰  

1. [创建机器学习工作区](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [上载现有数据](machine-learning-walkthrough-2-upload-data.md)
3. [创建新试验](machine-learning-walkthrough-3-create-new-experiment.md)
4. [定型和评估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [部署 Web 服务](machine-learning-walkthrough-5-publish-web-service.md)
6. [访问 Web 服务](machine-learning-walkthrough-6-access-web-service.md)

本演练基于 [Cortana Intelligence 库](http://go.microsoft.com/fwlink/?LinkID=525270)中简化版的[二进制分类：信用风险预测](http://gallery.cortanaintelligence.com/)示例试验。


> [!TIP]
> 若要下载和打印 Microsoft Azure 机器学习工作室功能概述示意图，请参阅 [Overview diagram of Azure Machine Learning Studio capabilities](machine-learning-studio-overview-diagram.md)（Azure Machine 机器学习工作室功能概述）。
> 
> 



<!--HONumber=Dec16_HO3-->


