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
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: 1cde28bff48bc239e22fe47a882e3218bd2d938b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278563"
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

了解[V3 预测终结点](luis-migration-api-v3.md)。

### <a name="rest-apis"></a>REST API

创作和预测终结点 API 都可从 REST Api 获取：

|类型|版本|
|--|--|
|创作|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[预览 V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|预测|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="language-based-sdks"></a>基于语言的 Sdk

|语言 |参考文档|package|示例|快速入门|
|--|--|--|--|--|
|C#|[创作](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[预测](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[NuGet 创作](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[NuGet 预测](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[.Net SDK 示例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/LUIS)|[创建和管理应用](sdk-csharp-quickstart-authoring-app.md)<br>[查询预测终结点](sdk-csharp-quickstart-query-prediction-endpoint.md)|
|Go|[创作和预测](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)|[创作](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/go)<br>[预测](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/go)|[使用 REST 创作和预测](luis-get-started-get-intent-from-rest.md)|
|Java|[创作和预测](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Maven 创作](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Maven 预测](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|[创作](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/java)<br>[预测](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/java)|[创作和预测](luis-get-started-get-intent-from-rest.md)
|Node.js|[创作](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[预测](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[NPM 创作](https://www.npmjs.com/package/azure-cognitiveservices-luis-authoring)<br>[NPM 预测](https://www.npmjs.com/package/azure-cognitiveservices-luis-runtime)|[创作](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/change-model/node)<br>[预测](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/quickstarts/analyze-text/node)|[使用 REST 创作和预测](luis-get-started-get-intent-from-rest.md)|
|Python|[创作和预测](sdk-python-quickstart-authoring-app.md)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[创作](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/LUIS/application_quickstart.py)|[创作](sdk-python-quickstart-authoring-app.md)<br>[使用 REST 进行预测](luis-get-started-get-intent-from-rest.md)


### <a name="containers"></a>容器

语言理解（LUIS）提供一个[容器](luis-container-howto.md)，用于提供应用程序的本地版本和包含版本。 

### <a name="export-and-import-formats"></a>导出和导入格式

语言理解提供了以 JSON 格式、`.LU` （[LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)）格式以及语言理解容器的压缩包来管理应用及其模型的功能。 

导入和导出这些格式可从 Api 和 LUIS 门户中获取。 门户提供了导入和导出作为应用列表和版本列表的一部分。 

## <a name="other-tools-and-sdks"></a>其他工具和 Sdk

机器人框架作为使用[Azure 机器人服务](https://dev.botframework.com/)的各种语言和服务提供的[SDK](https://github.com/Microsoft/botframework) 。 

机器人框架提供[多种工具](https://github.com/microsoft/botbuilder-tools)来帮助语言理解，其中包括：

* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown) -使用 markdown 文件构建 LUIS 语言理解模型
* [LUIS CLI](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUIS) -创建和管理 LUIS.ai 应用程序
* [调度](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Dispatch)-管理父和子应用
* [LUISGen](https://github.com/microsoft/botbuilder-tools/blob/master/packages/LUISGen) -自动生成 LUIS C#意向和实体的支持/Typescript 类。
* [机器人模拟器](https://github.com/Microsoft/BotFramework-Emulator/releases)-一种桌面应用程序，允许机器人开发人员测试和调试使用 BOT Framework SDK 生成的机器人


## <a name="next-steps"></a>后续步骤

* 了解常见的[HTTP 错误代码](luis-reference-response-codes.md)
* 所有 Api 和 Sdk 的[参考文档](https://docs.microsoft.com/azure/index#pivot=sdkstools)
* [机器人框架](https://github.com/Microsoft/botbuilder-dotnet)和[Azure 机器人服务](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [认知容器](../cognitive-services-container-support.md)