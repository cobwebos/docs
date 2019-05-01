---
title: 不支持的语言部署的自定义转换器
titleSuffix: Azure Cognitive Services
description: 如何部署自定义转换器不支持的语言对。
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/24/2019
ms.author: v-pawal
ms.openlocfilehash: 09fbd771d945646fe385508779d38e4abb2ee293
ms.sourcegitcommit: daf6538427ea6effef898f2ee3d857e5fa2dccbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64476502"
---
# <a name="unsupported-language-deployments"></a>不支持的语言部署

<!--Custom Translator provides the highest-quality translations possible using the latest techniques in neural machine learning. While Microsoft intends to make neural training available in all languages, there are some limitations that prevent us from being able to offer neural machine translation in all language pairs.-->  

使用 Microsoft Translator 中心即将停用时，Microsoft 将会取消部署当前部署通过中心的所有模型。 有许多您的自定义转换器不支持其语言对中心内部署的模型。  我们不希望在此情况下用户有没有办法转换其内容。

我们现在有一个进程，可通过自定义转换器将不受支持的模型部署。  此过程，可继续翻译内容使用最新的 V3 API。  将托管这些模型，直到你选择取消部署或语言对就会出现在自定义转换器。  此文章介绍了部署具有不受支持的语言对模型的过程。

## <a name="prerequisites"></a>必备组件

为了使您要部署的候选项的模型，它们必须满足以下条件：
* 包含该模型的项目必须已迁移从中心到自定义转换器使用迁移工具。  找不到迁移项目和工作区的过程[此处](how-to-migrate.md)。
* 在迁移时，模型必须是已部署状态。  
* 该模型的语言对必须是自定义转换器不支持的语言对。  语言对在其中一种语言支持到或从英语，但对本身不包括英语、 是不受支持的语言部署的候选对象。  例如，法语的德语语言对中心模型被视为一个不受支持的语言对甚至到英语和德语是到英语但法语受支持的语言对。

## <a name="process"></a>进程
迁移中心中的适用于部署的模型后，您可以通过转到找到它们**设置**工作区和滚动到页面将在其中看到末尾页**不受支持转换器中心培训**部分。  如果你有满足上述先决条件的项目，才会显示此部分。

![如何从 Hub 迁移](media/unsupported-language-deployments/unsupported-translator-hub-trainings.jpg)

内**不受支持的转换器中心培训**选择页面**取消请求培训**选项卡包含适合于部署的模型。  选择你想要部署和提交请求的模型。   在 4 月 30 日的部署截止时间之前，您可以选择根据需要部署的模型数量。
 
![如何从 Hub 迁移](media/unsupported-language-deployments/unsupported-translator-hub-trainings-list.jpg)

提交后，该模型将不再可在上找到**取消请求培训**选项卡上，将改为显示在**请求培训**选项卡。可以在任何时候查看请求的培训。

![如何从 Hub 迁移](media/unsupported-language-deployments/request-unsupported-trainings.jpg) 

## <a name="whats-next"></a>后续步骤

为部署选择的模型将保存后停止使用中心和所有模型都都取消部署。  必须等到 5 月 24 提交请求以获得的不受支持的模型部署。  我们将部署在 6 月 15 日的位置，它们将是一种可通过翻译 V3 API 访问这些模型。  此外，它们将可通过第 2 版 API 年 7 月 1 日前。  

有关详细信息中不推荐使用的中心检查重要日期[此处](https://www.microsoft.com/translator/business/hub/)。
部署后，正常托管费用将适用。  请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)有关详细信息。  

标准自定义转换器与模型不同，中心模型仅可在单个区域中，因此多区域托管费用将不适用。  部署完成后，你将能够在任何时候通过已迁移的自定义转换器项目取消部署中心模型。

## <a name="next-steps"></a>后续步骤

- [训练模型](how-to-train-model.md)。
- 通过 [Microsoft 文本翻译 API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) 开始使用已部署的自定义翻译模型。
