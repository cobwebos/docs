---
title: 自定义语音语音服务入门
titleSuffix: Azure Cognitive Services
description: 自定义语音是一组联机工具，可用于评估和改进我们的应用程序、工具和产品的语音到文本的准确性。 开始使用的只是一些测试音频文件。 请按照以下链接开始创建自定义语音到文本体验。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: c8c849cb83ecb1db5e972c660d94c795092c458e
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806005"
---
# <a name="what-is-custom-speech"></a>什么是自定义语音？

[自定义语音](https://aka.ms/customspeech)是一组联机工具，可用于评估和改善 Microsoft 的应用程序、工具和产品的语音到文本准确性。 开始使用的只是一些测试音频文件。 请按照以下链接开始创建自定义语音到文本体验。

## <a name="whats-in-custom-speech"></a>自定义语音有哪些？

你需要一个 Azure 帐户和一个语音服务订阅，然后才能使用自定义语音执行任何操作。 获得帐户后，可以准备数据、训练和测试模型、检查识别质量、评估准确性，并最终部署和使用自定义语音到文本模型。

此图重点介绍组成[自定义语音门户](https://aka.ms/customspeech)的部分。 使用以下链接了解有关每个步骤的详细信息。

![突出显示组成自定义语音门户的不同组件。](./media/custom-speech/custom-speech-overview.png)

1. [订阅和创建项目](#set-up-your-azure-account)-创建 Azure 帐户并订阅语音服务。 此统一订阅使你可以访问语音到文本、文本到语音、语音翻译和[自定义语音门户](https://speech.microsoft.com/customspeech)。 然后，使用语音服务订阅创建第一个自定义语音项目。

2. [上传测试数据](how-to-custom-speech-test-data.md)-上传测试数据（音频文件），以便评估适用于你的应用程序、工具和产品的 Microsoft 语音到文本产品/服务。

3. [检查识别质量](how-to-custom-speech-inspect-data.md)-使用[自定义语音门户](https://speech.microsoft.com/customspeech)播放上传的音频，并检查测试数据的语音识别质量。 对于定量度量，请参阅[检查数据](how-to-custom-speech-inspect-data.md)。

4. [评估准确性](how-to-custom-speech-evaluate-data.md)-评估语音到文本模型的准确性。 [自定义语音门户](https://speech.microsoft.com/customspeech)将提供*Word 错误率*，可用于确定是否需要额外的培训。 如果您对准确性感到满意，则可以直接使用语音服务 Api。 如果希望通过 5%-20% 的相对平均值提高准确性，请使用门户中的 "**培训**" 选项卡上传其他训练数据，如人为的 "记录" 和 "相关文本"。

5. [训练模型](how-to-custom-speech-train-model.md)-通过提供书面记录（10-1000 小时）和相关文本（< 200 MB）以及音频测试数据，提高语音到文本模型的准确性。 此数据可帮助训练语音到文本模型。 完成培训后，请重新测试，如果对结果满意，则可以部署模型。

6. [部署模型](how-to-custom-speech-deploy-model.md)-为语音到文本模型创建自定义终结点，并在应用程序、工具或产品中使用该终结点。

## <a name="set-up-your-azure-account"></a>设置你的 Azure 帐户

需要语音服务订阅，然后才能使用[自定义语音门户](https://speech.microsoft.com/customspeech)创建自定义模型。 按照以下说明创建标准语音服务订阅：[创建语音订阅](get-started.md#try-the-speech-service-using-a-new-azure-account)。

> [!NOTE]
> 请确保创建标准（S0）订阅，不支持免费试用（F0）订阅。

创建 Azure 帐户和语音服务订阅后，需要登录到[自定义语音门户](https://speech.microsoft.com/customspeech)并连接订阅。

1. 从 Azure 门户获取语音服务订阅密钥。
2. 登录到[自定义语音门户](https://aka.ms/custom-speech)。
3. 选择需要使用的订阅，并创建一个语音项目。
4. 如果你想要修改你的订阅，请使用位于顶部导航栏中的**齿轮**图标。

## <a name="how-to-create-a-project"></a>如何创建项目

数据、模型、测试和终结点等内容组织到[自定义语音门户](https://speech.microsoft.com/customspeech)中的**项目**中。 每个项目都是特定于域和国家/地区的。 例如，你可以为在美国中使用英语的呼叫中心创建项目。

若要创建第一个项目，请选择 "**语音到文本"/"自定义" 语音**，然后单击 "**新建项目**"。 按照向导提供的说明创建项目。 创建项目后，应该会看到四个选项卡：**数据**、**测试**、**定型**和**部署**。 使用[后续步骤](#next-steps)中提供的链接来了解如何使用各个选项卡。

## <a name="next-steps"></a>后续步骤

* [准备和测试数据](how-to-custom-speech-test-data.md)
* [检查数据](how-to-custom-speech-inspect-data.md)
* [评估数据](how-to-custom-speech-evaluate-data.md)
* [训练模型](how-to-custom-speech-train-model.md)
* [部署模型](how-to-custom-speech-deploy-model.md)
