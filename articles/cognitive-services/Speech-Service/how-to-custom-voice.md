---
title: 通过自定义语音语音服务改善合成
titleSuffix: Azure Cognitive Services
description: 自定义语音是一组在线工具，使用它可为自有品牌创建可识别的独一无二的语音。 只需准备好几个音频文件和关联的听录内容即可完全入门。 请遵循以下链接开始创建自定义语音转文本体验。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 2aacf8881a57f0677177cd341a0ddc63224b26b6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81402060"
---
# <a name="get-started-with-custom-voice"></a>自定义语音入门

[自定义语音](https://aka.ms/customvoice)是一组在线工具，使用它可为自有品牌创建可识别的独一无二的语音。 只需准备好几个音频文件和关联的听录内容即可完全入门。 请遵循以下链接开始创建自定义文本转语音体验。

## <a name="whats-in-custom-voice"></a>什么是自定义语音？

在开始使用自定义语音之前，你将需要一个 Azure 帐户和一个语音服务订阅。 创建帐户后，可以准备数据、训练和测试模型、评估语音质量，并最终部署自定义语音模型。

下图突出显示了使用[自定义语音门户](https://aka.ms/customvoice)创建自定义语音模型的步骤。 请使用链接了解详细信息。

![自定义语音体系结构图](media/custom-voice/custom-voice-diagram.png)

1. [订阅和创建项目](#set-up-your-azure-account)-创建 Azure 帐户并创建语音服务订阅。 使用此统一订阅可以访问语音转文本、文本转语音、语音翻译和自定义语音门户。 然后，使用语音服务订阅创建第一个自定义语音项目。

2. [上传数据](how-to-custom-voice-create-voice.md#upload-your-datasets) - 使用自定义语音门户或自定义语音 API 上传数据（音频和文本）。 在门户中，可以调查和评估发音评分以及信噪比。 有关详细信息，请参阅[如何为自定义语音准备数据](how-to-custom-voice-prepare-data.md)。

3. [训练模型](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – 使用数据创建自定义的“文本转语音”语音模型。 可在不同的语言中训练模型。 训练并测试模型后，如果你对结果感到满意，则可以部署该模型。

4. [部署模型](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) - 为文本语音模型创建自定义终结点，并使用该终结点在产品、工具和应用程序中进行语音合成。

## <a name="custom-neural-voices"></a>自定义神经声音

此神经语音自定义功能当前以公共预览版的状态限制为所选客户。 填写此[应用程序窗体](https://go.microsoft.com/fwlink/?linkid=2108737)开始。

> [!NOTE]
> 作为 Microsoft 对设计责任 AI 的承诺的一部分，我们的目的是保护个人和社会的权利，并促进透明的人工计算机交互。 出于此原因，自定义的神经语音通常不适用于所有客户。 只有在查看了你的应用程序后，你可能会获得对技术的访问权限，并且你已承诺将其与我们的道德原则配合使用。 详细了解[应用程序的过程](https://aka.ms/custom-neural-gating-overview)。

## <a name="set-up-your-azure-account"></a>设置 Azure 帐户

需要语音服务订阅，然后才能使用自定义语音门户创建自定义模型。 按照这些说明在 Azure 中创建语音服务订阅。 如果你没有 Azure 帐户，可以注册一个新帐户。  

创建 Azure 帐户和语音服务订阅后，需要登录到自定义语音门户并连接订阅。

1. 从 Azure 门户获取语音服务订阅密钥。
2. 登录到[自定义语音门户](https://aka.ms/custom-voice)。
3. 选择你的订阅并创建语音项目。
4. 若要切换到另一个语音订阅，请使用顶部导航栏中的齿轮图标。

> [!NOTE]
> 自定义语音服务不支持 30 天免费试用密钥。 必须先在 Azure 中创建 F0 或 S0 密钥才能使用该服务。

## <a name="how-to-create-a-project"></a>如何创建项目

数据、模型、测试和终结点等内容在自定义语音门户中组织成**项目**。 每个项目特定于某个国家/语言，以及要创建的语音的性别。 例如，你可以创建一个项目，用于在美国（en-us）中使用英语的呼叫中心的聊天机器人。

若要创建第一个项目，请选择“文本转语音/自定义语音”选项卡，然后单击“新建项目”。******** 遵照向导中的说明创建项目。 创建项目后，你将看到四个选项卡：**数据**、**培训**、**测试**和**部署**。 使用[后续步骤](#next-steps)中提供的链接了解如何使用每个选项卡。

> [!IMPORTANT]
> [自定义语音门户](https://aka.ms/custom-voice)最近已更新！ 如果在 CRIS.ai 门户或 Api 中创建了以前的数据、模型、测试和已发布的终结点，则需要在新门户中创建一个新项目以连接到这些旧实体。

## <a name="next-steps"></a>后续步骤

- [准备自定义语音数据](how-to-custom-voice-prepare-data.md)
- [创建自定义语音](how-to-custom-voice-create-voice.md)
- [指南：录制语音示例](record-custom-voice-samples.md)
