---
title: "使用 R 生成的机器学习 Web 服务示例 | Microsoft Docs"
description: "找到一组使用 R 代码和机器学习创建的有用 Web 服务示例，并发布到 Azure 应用商店。"
keywords: "csharp, r 代码, Web 服务示例"
services: machine-learning
documentationcenter: 
author: jaymathe
manager: jhubbard
editor: cgronlun
ms.assetid: 97d66cb7-6a84-4ae9-8095-0b5f5ba82d7f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: jaymathe
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c3e94cf69d55de9c31a48de35e86a1ae9149a63


---
# <a name="web-services-examples-using-r-code-on-azure-machine-learning-and-published-to-microsoft-azure-marketplace"></a>有关在 Azure 机器学习中使用 R 代码并将其发布到 Microsoft Azure 应用商店的 Web 服务示例
在本文中，Web 服务示例使用 Azure 机器学习创建，并发布到 Azure 应用商店。 每个 Web 服务示例均附加了综合文档，这嵌入了用于测试服务的示例数据集，并解释了用户可以如何自行创建类似的服务。 

在 Azure 机器学习工作室中，用户可以编写 R 代码，并且单击几下鼠标可将其作为应用程序和设备的 Web 服务发布以在全世界使用。 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

从生成提供统计功能的简单计算器到创建自定义文本挖掘的观点分析预测指标，新手和有经验的 R 用户均可受益于 Azure 机器学习可操作 R 代码的简单之处。 虽然这些 Web 服务可由用户通过移动应用或网站使用，但这些 Web 服务示例的目的在于显示机器学习可如何操作 R 脚本以用于分析目的，并可用于基于 R 代码创建 Web 服务。

每个示例包含 Web 服务使用的 C# 示例。

![Azure 机器学习中的 R 代码图：用于专门用途或发布到 Azure 应用商店的 R 解决方案。][1]

请考虑以下方案。

## <a name="scenario-1-generic-model"></a>方案 1：常规模型
用户使用可应用到新用户数据的常规模型，例如时序数据的基本预测或具有高级分析的自定义生成的 R 方法。 此用户将模型作为 Web 服务发布，供其他人与数据一起使用。

* [二元分类器](machine-learning-r-csharp-binary-classifier.md)
* [群集模型](machine-learning-r-csharp-cluster-model.md)
* [多变量线性回归](machine-learning-r-csharp-multivariate-linear-regression.md)
* [预测 - 指数平滑](machine-learning-r-csharp-forecasting-exponential-smoothing.md)
* [ETS + STL 预测](machine-learning-r-csharp-retail-demand-forecasting.md)
* [预测 - 自动回归集成移动平均 (ARIMA)](machine-learning-r-csharp-arima.md)
* [生存分析](machine-learning-r-csharp-survival-analysis.md)

## <a name="scenario-2-trained-model-specific-data"></a>方案 2：训练模型 – 特定数据
用户具有通过 R 代码提供有用预测的数据，例如通过 K 平均值算法集合的大型个性调查表示例可预测用户的个性类型，或者是通过调查分析 R 程序包用于预测个人患肺癌风险的健康调查数据。 用户通过 Web 服务发布预测新用户结果的数据。

## <a name="scenario-3-trained-model-generic-data"></a>方案 3：训练模型 – 一般数据
用户具有一般数据（例如文本集），允许跨不同类型的用例和方案广泛生成和应用 Web 服务。

* [基于词典的情绪分析](machine-learning-r-csharp-lexicon-based-sentiment-analysis.md)

## <a name="scenario-4-advanced-calculator"></a>方案 4：高级计算器
用户提供高级计算或模拟，无需任何训练模型或使模型适用于用户数据。

* [比例测试的差异](machine-learning-r-csharp-difference-in-two-proportions.md)
* [正态分布套件](machine-learning-r-csharp-normal-distribution.md)
* [二项式分布套件](machine-learning-r-csharp-binomial-distribution.md)

## <a name="faq"></a>常见问题
有关使用 Web 服务或发布到应用商店的常见问题，请参阅[此处](machine-learning-marketplace-faq.md)。

[1]: ./media/machine-learning-r-csharp-web-service-examples/machine-learning-r-code-options-for-using-and-sharing-cloud.png






<!--HONumber=Nov16_HO3-->


