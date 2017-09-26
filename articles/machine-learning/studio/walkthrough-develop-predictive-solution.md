---
title: "使用机器学习的信用风险预测解决方案 | Microsoft Docs"
description: "详细演练中介绍如何创建预测分析解决方案，用于在 Azure 机器学习工作室中进行信用风险评估。"
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
ms.date: 03/23/2017
ms.author: garye
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fe504d826b6c40099f1f8706ef7e8780eed5cf9a
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="walkthrough-develop-a-predictive-analytics-solution-for-credit-risk-assessment-in-azure-machine-learning"></a>演练：开发预测分析解决方案，用于在 Azure 机器学习中评估信用风险

在本演练中，我们将深入探讨在机器学习工作室中开发预测分析解决方案的过程。 我们会在机器学习工作室中开发一个简单模型，然后将其部署为 Azure 机器学习 Web 服务，以便模型在其中使用新数据进行预测。 

本演练假设用户此前至少使用过机器学习工作室一次，且对机器学习概念有一些了解。 但不假设用户精通其中任一领域。

如果以前从来没用过 **Azure 机器学习工作室**，则可能一开始需要学习[在 Azure 机器学习工作室中创建第一个数据科学试验](create-experiment.md)这一教程。 该教程指导用户首次完成机器学习工作室的使用。 教程中会介绍各种基础知识：如何将模块拖放到试验中、如何将模块连接到一起、如何运行试验，以及如何查看结果。 另一个可有助于入门的工具是一个示意图，该示意图概述了机器学习工作室的各种功能。 可在此处下载并打印该图：[Azure 机器学习工作室功能的概要示意图](studio-overview-diagram.md)。
 
如果用户基本上是刚接触机器学习领域，我们有一个视频系列可能会有所帮助。 视频名为[适合初学者的数据科学](data-science-for-beginners-the-5-questions-data-science-answers.md)，视频中使用日常语言和概念对机器学习进行了精彩的介绍。


[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
 

## <a name="the-problem"></a>问题

假设用户需要根据他们提供的贷款申请相关信息预测个人的信用风险。  

信用风险评估是个较为复杂的过程，但在本演练中我们可将其适当简化。 我们将使用它作为示例，展示如何使用 Microsoft Azure 机器学习来创建预测分析解决方案。 为此，我们将使用 Azure 机器学习工作室和机器学习 Web 服务。  

## <a name="the-solution"></a>解决方案

在本详细演练中，我们从开放使用的信用风险数据入手，并根据该数据开发和定型预测模型。 然后将模型部署为 Web 服务，以便其他用户可使用它来进行信用风险评估。

要创建该信用风险评估解决方案，我们将执行以下步骤：  

1. [创建机器学习工作区](walkthrough-1-create-ml-workspace.md)
2. [上传现有数据](walkthrough-2-upload-data.md)
3. [创建试验](walkthrough-3-create-new-experiment.md)
4. [定型和评估模型](walkthrough-4-train-and-evaluate-models.md)
5. [部署 Web 服务](walkthrough-5-publish-web-service.md)
6. [访问 Web 服务](walkthrough-6-access-web-service.md)

> [!TIP] 
> 可在 [Cortana Intelligence 库](https://gallery.cortanaintelligence.com)中找到本演练中开发的试验的工作副本。 请转到**[演练 - 信用风险预测](https://gallery.cortanaintelligence.com/Experiment/Walkthrough-Credit-risk-prediction-1)**并单击“在工作室中打开”将试验副本下载到机器学习工作室的工作区。
> 
> 本演练基于简化版的[二进制分类：信用风险评估](http://go.microsoft.com/fwlink/?LinkID=525270)示例试验，也可在[库](http://gallery.cortanaintelligence.com/)中获取。

