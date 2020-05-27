---
title: 负责机器学习 (ML)
titleSuffix: Azure Machine Learning
description: 了解什么是负责 ML，以及如何使用 Azure 机器学习将其付诸实践
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/08/2020
ms.openlocfilehash: 3cef3c2179019f6d84de5596e61abaf8d7d3182c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595006"
---
# <a name="responsible-machine-learning-ml"></a>负责机器学习 (ML)

本文介绍了什么是负责 ML，以及如何使用 Azure 机器学习将其付诸实践。

在 AI 系统的整个开发和使用过程中，信任必须是核心。 具体包括信任平台、过程和模型。 在 Microsoft，负责 ML 包含以下价值观和原则：

- 了解机器学习模型
  - 解释和说明模型行为
  - 评估和缓解模型不公平性
- 保护人员及其数据
  - 通过差异隐私防止数据泄露  
- 控制端到端机器学习过程
  - 用数据表记录机器学习生命周期

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="负责 ML 支柱":::

随着人工智能和自治系统越来越多地融入社会结构，积极主动地努力预测和缓解这些技术带来的意外后果是很重要的。

## <a name="interpret-and-explain-model-behavior"></a>解释和说明模型行为

难以解释的系统或黑盒系统可能会出问题，因为利益干系人（如系统开发人员、监管人员、用户和业务决策者）难以理解系统做出某些决策的原因。 有些 AI 系统比其他系统更容易解释，有时需要在更高准确度的系统和更容易解释的系统之间进行取舍。

若要生成可解释的 AI 系统，请使用 Microsoft 生成的开放源代码包 [InterpretML](https://github.com/interpretml/interpret)。 [可以在 Azure 机器学习中使用 InterpretML](how-to-machine-learning-interpretability.md)，以[解释和说明机器学习模型](how-to-machine-learning-interpretability-aml.md)，包括[自动化机器学习模型](how-to-machine-learning-interpretability-automl.md)。

## <a name="assess-and-mitigate-model-unfairness"></a>评估和缓解模型不公平性

随着 AI 系统越来越多地参与到社会的日常决策中，这些系统能够很好地为每个人提供公平的结果是极为重要的。

AI 系统中的不公平性可能会导致以下意外后果：

- 对个人隐瞒机会、资源或信息。
- 强化偏见和刻板印象。

公平性的许多方面无法通过指标来捕获或表示。 有一些工具和做法可以在设计和开发时提高 AI 系统的公平性。

减少 AI 系统的不公平性的两个关键步骤是评估和缓解。 建议使用 [FairLearn](https://github.com/fairlearn/fairlearn)，这是一个开放源代码包，可评估和缓解 AI 系统的潜在不公平性。 若要详细了解公平性和 FairLearn 包，请参阅 [ML 中的公平性](./concept-fairness-ml.md)一文。

## <a name="prevent-data-exposure-with-differential-privacy"></a>通过差异隐私防止数据泄露

当数据用于分析时，重要的是数据在整个使用过程中保持私密和机密性。 差异隐私是一组系统和做法，有助于保持个人数据的安全性和私密性。

在传统方案中，原始数据存储在文件和数据库中。 用户通常在分析数据时使用原始数据。 这是一个问题，因为可能会侵犯个人隐私。 差异隐私尝试通过向数据添加“噪音”或随机性来处理此问题，这样用户就无法识别任何单独的数据点。

实现差异隐私系统较为棘手。 [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) 是一个开放源代码项目，其中包含用于生成全局差异隐私系统的不同组件。 若要详细了解差异隐私和 WhiteNoise 项目，请参阅[使用差异隐私和 WhiteNoise 保护数据隐私](./concept-differential-privacy.md)一文。

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>用数据表记录机器学习生命周期

在机器学习过程中记录正确的信息是在每个阶段做出负责决策的关键所在。 使用数据表，可以记录在机器学习生命周期内使用和创建的机器学习资产。

模型往往被认为是“黑盒”，而且往往很少有关于它们的信息。 由于机器学习系统变得越来越普遍，且被用于做出决策，因此使用数据表就是向开发更负责的机器学习系统迈进。

不妨在数据表中记录一些模型信息：

- 预期用途
- 模型体系结构
- 使用的训练数据
- 使用的评估数据
- 训练模型性能指标
- 公平性信息。

请参阅下面的示例，了解如何使用 Azure 机器学习 SDK 来实现[模型数据表](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb)。

## <a name="additional-resources"></a>其他资源

- 详细了解有关机器学习系统记录的[关于 ML](https://www.partnershiponai.org/about-ml/) 指南集。