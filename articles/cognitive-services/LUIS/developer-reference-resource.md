---
title: 开发人员资源-语言理解
titleSuffix: Azure Cognitive Services
description: 开发人员同时具有 REST Api 和 Sdk 语言理解。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: diberry
ms.openlocfilehash: 97684a4668c1d495fa690674f7bd94e064737bd4
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273946"
---
# <a name="developer-resources-for-language-understanding"></a>语言理解的开发人员资源

开发人员可以使用 REST Api 和 Sdk 进行语言理解。 

## <a name="azure-resource-management"></a>Azure 资源管理

使用 Azure 认知服务管理层来创建、编辑、列出和删除语言理解或认知服务资源。

查找基于工具的参考文档：

* [Azure CLI](https://docs.microsoft.com/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)

## <a name="language-understanding-authoring-and-prediction-requests"></a>语言理解创作和预测请求

可以从需要创建的 Azure 资源访问语言理解服务。 有两个资源：创作和预测终结点资源。 这两个资源都允许您控制 LUIS 资源。 

### <a name="rest-apis"></a>REST API

创作和预测终结点 API 都可从 REST Api 获取：

* 创作[参考文档](https://go.microsoft.com/fwlink/?linkid=2092087)
* 预测运行时[参考文档](https://go.microsoft.com/fwlink/?linkid=2092356)

### <a name="language-based-sdks"></a>基于语言的 Sdk

|语言 |参考文档|package|示例|快速入门|
|--|--|--|--|--|
|C#|[创作](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[预测](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet 创作](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet 预测](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net SDK 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[创建和管理应用](sdk-csharp-quickstart-authoring-app.md)<br>[查询预测终结点](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Go|[创作和预测](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[创作](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[预测](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[使用 REST 创作](luis-get-started-go-add-utterance.md)<br>[使用 REST 进行预测](luis-get-started-go-get-intent.md)|
|Java|[创作和预测](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven 创作](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven 预测](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[创作](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[预测](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[创作](luis-get-started-java-add-utterance.md)<br>[预测](luis-get-started-java-get-intent.md)
|Node.js|[创作](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[预测](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM 创作](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[NPM 预测](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[创作](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[预测](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[使用 REST 创作](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-get-intent)<br>[使用 REST 进行预测](https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-node-add-utterance)|
|Python|[创作和预测](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/?view=azure-python)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[创作](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[创作](sdk-python-quickstart-authoring-app.md)<br>[使用 REST 进行预测](luis-get-started-python-get-intent.md)

## <a name="other-tools-and-sdks"></a>其他工具和 Sdk

机器人框架作为使用[Azure 机器人服务](https://dev.botframework.com/)的各种语言和服务提供的[SDK](https://github.com/Microsoft/botframework) 。 

机器人框架提供[多种工具](https://github.com/microsoft/botbuilder-tools)来帮助语言理解，其中包括：

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -使用 markdown 文件构建 LUIS 语言理解模型
* [LUIS Cli](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) -创建和管理 LUIS.ai 应用程序
* [调度](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)-管理父和子应用
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) -自动生成 LUIS C#意向和实体的支持/Typescript 类。
* [机器人模拟器](https://github.com/Microsoft/BotFramework-Emulator/releases)-一种桌面应用程序，允许机器人开发人员测试和调试使用 BOT Framework SDK 生成的机器人


## <a name="next-steps"></a>后续步骤

了解常见的[HTTP 错误代码](luis-reference-response-codes.md)