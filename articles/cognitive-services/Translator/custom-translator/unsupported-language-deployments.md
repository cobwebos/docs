---
title: 不支持的语言部署 - 自定义转换器
titleSuffix: Azure Cognitive Services
description: 本文介绍如何在 Azure 认知服务自定义转换器中部署不支持的语言对。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: swmachan
ms.openlocfilehash: 3c5c74fc853b5a2425a58e1704aad43350cba212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837448"
---
# <a name="unsupported-language-deployments"></a>不支持的语言部署

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

随着 Microsoft 翻译中心即将停用，Microsoft 将取消部署当前通过该中心部署的所有模型。 你们中的许多人在 Hub 中部署了模型，这些模型的语言对在自定义转换器中不受支持。  我们不希望处于这种情况的用户无法翻译其内容。

现在，我们有一个流程，允许您通过自定义转换器部署不支持的模型。  此过程使您能够继续使用最新的 V3 API 翻译内容。  这些模型将被托管，直到您选择取消部署它们或语言对在自定义翻译中可用。  本文介绍了部署具有不支持的语言对的模型的过程。

## <a name="prerequisites"></a>先决条件

为了使模型成为部署的候选者，它们必须满足以下条件：
* 包含模型的项目必须使用迁移工具从中心迁移到自定义转换器。  迁移项目和工作区的过程[可以在这里](how-to-migrate.md)找到。
* 迁移发生时，模型必须处于已部署的状态。  
* 模型的语言对必须是自定义翻译器中不受支持的语言对。  支持英语或从英语支持语言，但该对本身不包含英语的语言对，是不支持的语言部署的候选语言。  例如，法语到德语对的 Hub 模型被视为不受支持的语言对，即使法语到英语和英语到德语都支持语言对。

## <a name="process"></a>进程
从计划部署的"中心"迁移模型后，可以通过访问工作区的 **"设置"** 页并滚动到页面末尾，在页面末尾看到**不支持的"翻译中心培训"** 部分来查找模型。  仅当项目符合上述先决条件时，才会显示此部分。

![如何从 Hub 迁移](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

在 **"不支持的翻译中心培训"** 选择页中，"**未请求培训"** 选项卡包含符合部署条件的模型。  选择要部署的模型并提交请求。   在 4 月 30 日部署截止时间之前，您可以选择所需的部署模型。
 
![如何从 Hub 迁移](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

提交后，该模型将不再在 **"未请求的培训"** 选项卡上可用，而是显示在 **"请求训练"** 选项卡上。 您可以随时查看您请求的培训。

![如何从 Hub 迁移](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>下一步是什么？

一旦中心停用并且所有模型都未部署，您为部署选择的模型将保存。  您必须在 5 月 24 日之前提交部署不支持的模型的请求。  我们将在 6 月 15 日部署这些模型，届时它们将通过转换器 V3 API 进行访问。  此外，它们将通过 V2 API 提供，直到 7 月 1 日。  

有关中心弃用中重要日期的详细信息，[请在此处](https://www.microsoft.com/translator/business/hub/)检查 。
部署后，将收取正常的托管费用。  有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)。  

与标准自定义转换器模型不同，集线器模型将仅在单个区域中可用，因此不会收取多区域托管费用。  部署后，您可以随时通过迁移的自定义转换器项目取消部署中心模型。

## <a name="next-steps"></a>后续步骤

- [训练模型](how-to-train-model.md)。
- 通过 [Microsoft 文本翻译 API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 开始使用已部署的自定义翻译模型。
