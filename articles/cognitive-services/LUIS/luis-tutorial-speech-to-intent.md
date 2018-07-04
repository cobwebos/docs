---
title: 结合使用语音 C# SDK 和 LUIS - Azure | Microsoft Docs
titleSuffix: Azure
description: 使用语音 C# SDK 示例对麦克风讲话，然后获取所返回的 LUIS 意向和实体预测。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/26/2018
ms.author: v-geberr;
ms.openlocfilehash: b681598f953d217ca636fb5c0adc3de4ddbebd60
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37031781"
---
# <a name="integrate-speech-service"></a>语音服务集成
借助[语音服务](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/)，可使用单个请求接收音频并返回 LUIS 预测 JSON 对象。

本文中，你将在 Visual Studio 中下载和使用 C# 项目，从而对麦克风讲话并接收 LUIS 预测信息。 该项目使用语音 [NuGet](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/) 包（该包已作为参考包含在内）。 

本文需要一个免费的 [LUIS][LUIS] 网站帐户才能导入应用程序。

## <a name="create-luis-endpoint-key"></a>创建 LUIS 终结点密钥
在 Azure 门户中，[创建](luis-how-to-azure-subscription.md#create-luis-endpoint-key)语言理解 (LUIS) 密钥。 

## <a name="import-human-resources-luis-app"></a>导入人力资源 LUIS 应用
意向和本文中的话语来自人力资源 LUIS 应用（可在 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) Github 存储库中获取）。 下载 [HumanResources.json](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json) 文件，以 *.json 扩展名保存该文件，并将其[导入](create-new-app.md#import-new-app)到 LUIS。 

此应用具有与人力资源域相关的意向、实体和话语。 话语示例包括：

```
Who is John Smith's manager?
Who does John Smith manage?
Where is Form 123456?
Do I have any paid time off?
```

## <a name="add-keyphrase-prebuilt-entity"></a>添加 KeyPhrase 预构建的实体
导入应用后，依次选择“实体”和“管理预构建的实体”。 添加 KeyPhrase 实体。 KeyPhrase 实体从话语中提取关键主题。

## <a name="train-and-publish-the-app"></a>训练并发布应用
1. 在右上方的导航栏中，选择“训练”按钮来训练 LUIS 应用。

2. 选择“发布”以转到“发布”页面。 

3. 在“发布”页面底部，添加在[创建 LUIS 终结点密钥](#create-luis-endpoint-key)部分中创建的 LUIS 密钥。

4. 通过选择“发布”槽右侧的“发布”按钮发布 LUIS 应用。 

  在“发布”页面上，收集[创建 LUIS 终结点密钥](#create-luis-endpoint-key)部分中创建的 LUIS 密钥的应用 ID、发布区域和订阅 ID。 需要修改代码，然后才能在文本的稍后位置使用这些值。 

  对于所创建的密钥，这些值均包含在“发布”页面底部的终结点 URL 中。 
  
  请勿使用免费的初学者密钥进行此练习。 只能使用在 Azure 门户中创建的语言理解密钥进行此练习。 

  https://**REGION**.api.cognitive.microsoft.com/luis/v2.0/apps/**APPID**?subscription-key=**LUISKEY**&q=

## <a name="audio-device"></a>音频设备
本文使用计算机上的音频设备。 它可以是带麦克风的耳机，也可以是内置音频设备。 请检查音频输入电平，确认要使音频设备检测到你说的话，是否要比平时说话的声音大。 

## <a name="download-the-luis-sample-project"></a>下载 LUIS 示例项目
 复制或下载 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples) 存储库。 使用 Visual Studio 打开[“语音转意向”项目](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-speech-intent-recognition)，并还原 NuGet 包。 VS 解决方案文件位于 .\LUIS-Samples-master\documentation-samples\tutorial-speech-intent-recognition\csharp\csharp_samples.sln。

语音 SDK 已作为参考包括在内。 

[![](./media/luis-tutorial-speech-to-intent/nuget-package.png "Visual Studio 2017 显示 Microsoft.CognitiveServices.Speech NuGet 包的屏幕截图")](./media/luis-tutorial-speech-to-intent/nuget-package.png#lightbox)

## <a name="modify-the-c-code"></a>修改 C# 代码
打开 LUIS_samples.cs 文件并更改以下变量：

|变量名称|目的|
|--|--|
|luisSubscriptionKey|与“发布”页面中终结点 URL 的 subscription-key 值相对应|
|luisRegion|与终结点 URL 的第一个子域相对应|
|luisAppId|与 apps/ 后面的终结点 URL 的路由相对应|

[![](./media/luis-tutorial-speech-to-intent/change-variables.png "Visual Studio 2017 显示 LUIS_samples.cs 变量的屏幕截图")](./media/luis-tutorial-speech-to-intent/change-variables.png#lightbox)

该文件已映射人力资源意向。

[![](./media/luis-tutorial-speech-to-intent/intents.png "Visual Studio 2017 显示 LUIS_samples.cs 意向的屏幕截图")](./media/luis-tutorial-speech-to-intent/intents.png#lightbox)

构建并运行应用。 

## <a name="test-code-with-utterance"></a>使用意向测试代码
选择 1，并对着麦克风说“贾勇的经理是谁”。

```cmd
1. Speech recognition of LUIS intent.
0. Stop.
Your choice: 1
LUIS...
Say something...
ResultId:cc83cebc9d6040d5956880bcdc5f5a98 Status:Recognized IntentId:<GetEmployeeOrgChart> Recognized text:<Who is the manager of John Smith?> Recognized Json:{"DisplayText":"Who is the manager of John Smith?","Duration":25700000,"Offset":9200000,"RecognitionStatus":"Success"}. LanguageUnderstandingJson:{
  "query": "Who is the manager of John Smith?",
  "topScoringIntent": {
    "intent": "GetEmployeeOrgChart",
    "score": 0.617331
  },
  "entities": [
    {
      "entity": "manager of john smith",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 31
    }
  ]
}

Recognition done. Your Choice:

```

找到正确的意向（即“获取员工组织结构图”），其置信度为 61%。 返回 keyPhrase 实体。 

语音 SDK 返回整个 LUIS 响应。 

## <a name="clean-up-resources"></a>清理资源
不再需要时，请删除 LUIS 人力资源应用。 为此，请在应用列表中选择应用名称右侧的省略号图标 (...)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

请记住在使用完示例代码后删除“LUIS 示例”目录。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将 LUIS 与机器人集成](luis-csharp-tutorial-build-bot-framework-sample.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website