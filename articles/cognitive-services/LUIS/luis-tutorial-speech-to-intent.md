---
title: 将语音 C# SDK 与 LUIS 配合使用
titleSuffix: Azure Cognitive Services
description: 借助语音服务，可使用单个请求接收音频并返回 LUIS 预测 JSON 对象。 本文中，你将在 Visual Studio 中下载和使用 C# 项目，从而对麦克风讲话并接收 LUIS 预测信息。 该项目使用语音 NuGet 包（该包已作为参考包含在内）。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: f98d640f032fed5f91df8e9d4fb55d3f20550339
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2018
ms.locfileid: "48883918"
---
# <a name="integrate-speech-service"></a>语音服务集成
借助[语音服务](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/)，可使用单个请求接收音频并返回 LUIS 预测 JSON 对象。 本文中，你将在 Visual Studio 中下载和使用 C# 项目，从而对麦克风讲话并接收 LUIS 预测信息。 该项目使用语音 [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) 包（该包已作为参考包含在内）。 

本文需要一个免费的 [LUIS][LUIS] 网站帐户才能导入应用程序。

## <a name="create-luis-endpoint-key"></a>创建 LUIS 终结点密钥
在 Azure 门户中，[创建](luis-how-to-azure-subscription.md#create-luis-endpoint-key)语言理解 (LUIS) 密钥。 

## <a name="import-human-resources-luis-app"></a>导入人力资源 LUIS 应用
意向和本文中的话语来自人力资源 LUIS 应用（可在 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) Github 存储库中获取）。 下载 [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/HumanResources.json) 文件，以 `.json` 扩展名保存该文件，并将其[导入](luis-how-to-start-new-app.md#import-new-app)到 LUIS。 

此应用具有与人力资源域相关的意向、实体和话语。 话语示例包括：

|示例陈述|
|--|
|John Smith 的经理是谁？|
|John Smith 管理谁？|
|窗体 123456 在哪里？|
|我有带薪假吗？|


## <a name="add-keyphrase-prebuilt-entity"></a>添加 KeyPhrase 预构建的实体
导入应用后，依次选择“实体”和“管理预构建的实体”。 添加 KeyPhrase 实体。 KeyPhrase 实体从话语中提取关键主题。

## <a name="train-and-publish-the-app"></a>训练并发布应用
1. 在右上方的导航栏中，选择“训练”按钮来训练 LUIS 应用。

2. 选择右上方栏中的“管理”，然后在左侧导航栏中选择“密钥和终结点”。 

3. 在“密钥和终结点”页上，分配在[创建 LUIS 终结点密钥](#create-luis-endpoint-key)部分中创建的 LUIS 密钥。

  在此页上，收集在[创建 LUIS 终结点密钥](#create-luis-endpoint-key)部分中创建的 LUIS 密钥的应用 ID、发布区域和订阅 ID。 需要修改代码，然后才能在文本的稍后位置使用这些值。 
  
  请勿使用免费的初学者密钥进行此练习。 只能使用在 Azure 门户中创建的语言理解密钥进行此练习。 

  https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=


4. 通过选择右上方栏中的“发布”按钮发布 LUIS 应用。 

## <a name="audio-device"></a>音频设备
本文使用计算机上的音频设备。 它可以是带麦克风的耳机，也可以是内置音频设备。 请检查音频输入电平，确认要使音频设备检测到你说的话，是否要比平时说话的声音大。 

## <a name="download-the-luis-sample-project"></a>下载 LUIS 示例项目
 复制或下载 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) 存储库。 使用 Visual Studio 打开[“语音转意向”项目](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition)，并还原 NuGet 包。 VS 解决方案文件位于 .\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln。

语音 SDK 已作为参考包括在内。 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Visual Studio 2017 显示 Microsoft.CognitiveServices.Speech NuGet 包的屏幕截图")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>修改 C# 代码
打开 `Program.cs` 文件并更改以下变量：

|变量名称|目的|
|--|--|
|LUIS_assigned_endpoint_key|与“发布”页中终结点 URL 分配的 subscription-key 值相对应|
|LUIS_endpoint_key_region|与终结点 URL 的第一个子域相对应，例如 `westus`|
|LUIS_app_ID|与 apps/ 后面的终结点 URL 的路由相对应|

`Program.cs` 文件已映射人力资源意向。

构建并运行应用程序。 

## <a name="test-code-with-utterance"></a>使用意向测试代码
对着麦克风说：“在雷德蒙德，谁是已批准的牙医？”。

[!code-console[Command line response from spoken utterance](~/samples-luis/documentation-samples/tutorial-speech-intent-recognition/console-output.txt "Command line response from spoken utterance")]

找到正确的意向（即“GetEmployeeBenefits”），其置信度为 85%。 返回 keyPhrase 实体。 

语音 SDK 返回整个 LUIS 响应。 

## <a name="clean-up-resources"></a>清理资源
不再需要时，请删除 LUIS 人力资源应用。 为此，请选择应用，然后在列表上方的上下文工具栏中选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

请记住在使用完示例代码后删除“LUIS 示例”目录。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将 LUIS 与机器人集成](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website