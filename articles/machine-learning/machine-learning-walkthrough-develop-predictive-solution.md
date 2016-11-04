---
title: 使用机器学习的信用风险预测解决方案 | Microsoft Docs
description: 详细演练中将介绍如何创建预测分析解决方案，用于在 Azure 机器学习工作室中进行信用风险评估。
keywords: 信用风险, 预测分析解决方案, 风险评估
services: machine-learning
documentationcenter: ''
author: garyericson
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/16/2016
ms.author: garye

---
# 演练：开发预测分析解决方案，用于在 Azure 机器学习中评估信用风险
假设你需要根据他们提供的贷款申请相关信息预测个人的信用风险。

当然，信用风险评估是一个复杂的问题，但我们将稍微简化一下问题的参数。然后，我们可以使用它作为示例，了解如何使用 Microsoft Azure 机器学习工作室和机器学习 Web 服务来创建此类预测分析解决方案。

在此详细演练中，我们将执行以下过程：在机器学习工作室开发预测分析模型，然后将其部署为 Azure 机器学习 Web 服务。首先使用公开的信用风险数据，在这些数据的基础之上开发和定型一个预测模型，然后将该模型部署为其他人可用来进行信用风险评估的 Web 服务。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

<!-- -->

> [!TIP]
> 若要下载并打印“Microsoft Azure 机器学习工作室功能概述”示意图，请参阅 [Azure Machine 机器学习工作室功能概述](machine-learning-studio-overview-diagram.md)。
> 
> 

若要创建信用风险评估解决方案，我们将遵循以下步骤︰

1. [创建机器学习工作区](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [上载现有数据](machine-learning-walkthrough-2-upload-data.md)
3. [创建新试验](machine-learning-walkthrough-3-create-new-experiment.md)
4. [定型和评估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [部署 Web 服务](machine-learning-walkthrough-5-publish-web-service.md)
6. [访问 Web 服务](machine-learning-walkthrough-6-access-web-service.md)

本演练基于“[Cortana Intelligence 库](http://gallery.cortanaintelligence.com/)”中简化版的[二进制分类︰信用风险预测](http://go.microsoft.com/fwlink/?LinkID=525270)示例试验。

<!---HONumber=AcomDC_0921_2016-->