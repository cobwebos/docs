---
title: 自定义语音识别入门 - 语音服务
titleSuffix: Azure Cognitive Services
description: 自定义语音识别是一组联机工具，可用于针对你的应用程序、工具和产品评估并改进我们的语音转文本准确度。 只需准备几个测试性音频文件即可开始。 请遵循以下链接开始创建自定义语音转文本体验。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 5401957cb527d43e041345ac9e67e7f7a69ed0d0
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725527"
---
# <a name="what-is-custom-speech"></a>什么是自定义语音识别？

[自定义语音识别](https://aka.ms/customspeech)是一组联机工具，可用于针对你的应用程序、工具和产品评估并改进 Microsoft 的语音转文本准确度。 只需准备几个测试性音频文件即可开始。 请遵循以下链接开始创建自定义语音转文本体验。

## <a name="whats-in-custom-speech"></a>什么是自定义语音识别？

在开始使用自定义语音识别执行任何操作之前，需要一个 Azure 帐户和一个语音服务订阅。 有了帐户后，即可准备数据、训练和测试模型、查看识别质量、评估准确度，并最终部署和使用自定义语音转文本模型。

此图突出显示了[自定义语音识别门户](https://aka.ms/customspeech)的组件。 使用以下链接详细了解每个步骤。

![突出显示组成自定义语音识别门户的不同组件。](./media/custom-speech/custom-speech-overview.png)

1. [订阅和创建项目](#set-up-your-azure-account) - 创建 Azure 帐户并订阅语音服务。 使用此统一订阅可以访问语音转文本、文本转语音、语音翻译和[自定义语音识别门户](https://speech.microsoft.com/customspeech)。 然后，可以使用语音服务订阅创建第一个“自定义语音识别”项目。

2. [上传测试数据](how-to-custom-speech-test-data.md) - 上传测试数据（音频文件），以便针对你的应用程序、工具和产品评估 Microsoft 的语音转文本产品/服务。

3. [检查识别质量](how-to-custom-speech-inspect-data.md) - 使用[自定义语音识别门户](https://speech.microsoft.com/customspeech)播放上传的音频，检查测试数据的语音识别质量。 如需进行量化度量，请参阅[检查数据](how-to-custom-speech-inspect-data.md)。

4. [评估准确度](how-to-custom-speech-evaluate-data.md) - 评估语音转文本模型的准确度。 [自定义语音识别门户](https://speech.microsoft.com/customspeech)会提供误字率，该指标可以用来确定是否需要更多的训练。  如果对准确度满意，可以直接使用语音服务 API。 如果希望提高准确度 5% - 20%（相对平均值），请在门户中使用“训练”选项卡  上传更多的训练数据，例如人为标记的听录和相关的文本。

5. [提高准确性](how-to-custom-speech-improve-accuracy.md)-在战略上选择其他定型数据，以根据你的方案提高语音到文本模型的质量。

6. [训练模型](how-to-custom-speech-train-model.md) - 提供编写的脚本（10-1,000 小时）和相关的文本 (<200 MB) 以及音频测试数据，以便提高语音转文本模型的准确度。 该数据有助于训练语音转文本模型。 训练并重新测试后，如果对结果感到满意，则可部署模型。

7. [部署模型](how-to-custom-speech-deploy-model.md) - 为语音转文本模型创建自定义终结点，并在应用程序、工具或产品中使用它。

## <a name="set-up-your-azure-account"></a>设置 Azure 帐户

需要语音服务订阅，然后才能使用[自定义语音门户](https://speech.microsoft.com/customspeech)创建自定义模型。 按照以下说明创建标准语音服务订阅：[创建语音订阅](get-started.md#new-resource)。

> [!NOTE]
> 请务必创建标准 (S0) 订阅。免费试用 (F0) 订阅不受支持。

创建 Azure 帐户和语音服务订阅后，需要登录到[自定义语音门户](https://speech.microsoft.com/customspeech)并连接订阅。

1. 从 Azure 门户获取语音服务订阅密钥。
2. 登录到[自定义语音门户](https://aka.ms/custom-speech)。
3. 选择需在其上工作的订阅并创建语音项目。
4. 若要修改订阅，请使用顶部导航栏中的**齿轮**图标。

## <a name="how-to-create-a-project"></a>如何创建项目

数据、模型、测试和终结点等内容在[自定义语音门户](https://speech.microsoft.com/customspeech)中组织成**项目**。 每个项目特定于域和国家/地区或语言。 例如，可以为使用美式英语的呼叫中心创建一个项目。

若要创建第一个项目，请选择“语音转文本/自定义语音”选项卡，然后单击“新建项目”。******** 遵照向导中的说明创建项目。 创建项目后，应该会看到四个选项卡：**数据**、**测试**、**定型**和**部署**。 使用[后续步骤](#next-steps)中提供的链接了解如何使用每个选项卡。

> [!IMPORTANT]
> [自定义语音门户](https://aka.ms/custom-speech)最近已更新！ 如果在 CRIS.ai 门户或 Api 中创建了以前的数据、模型、测试和已发布的终结点，则需要在新门户中创建一个新项目以连接到这些旧实体。

## <a name="next-steps"></a>后续步骤

* [准备和测试数据](how-to-custom-speech-test-data.md)
* [检查数据](how-to-custom-speech-inspect-data.md)
* [评估数据](how-to-custom-speech-evaluate-data.md)
* [训练模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
