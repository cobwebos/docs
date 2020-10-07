---
title: 快速入门：语言理解 (LUIS) SDK 客户端库
description: 通过此快速入门，使用 C#、Python 或 JavaScript 创建并查询带有 LUIS SDK 客户端库的 LUIS 应用。
ms.topic: quickstart
ms.date: 09/14/2020
ms.service: cognitive-services
ms.subservice: language-understanding
keywords: Azure, 人工智能, ai, 自然语言处理, nlp, LUIS, azure luis, 自然语言理解, ai 聊天机器人, 聊天机器人生成器, 理解自然语言
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-diberry-3core
ms.openlocfilehash: c2bc607fb5a5b5b38249453a1d0fce833547191f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327254"
---
# <a name="quickstart-language-understanding-luis-sdk-client-libraries-to-create-and-query-your-luis-app"></a>快速入门：语言理解 (LUIS) SDK 客户端库，用于创建和查询 LUIS 应用

通过此快速入门，使用 C#、Python 或 JavaScript 创建并查询带有 LUIS SDK 客户端库的 Azure LUIS 人工智能 (AI) 应用。

通过语言理解 (LUIS)，你可在用户的自然语言对话文本中应用自然语言处理 (NLP)，以预测整体含义并提炼出相关的详细信息。

* 使用创作 SDK 客户端库可以创建、编辑、训练和发布 LUIS 应用。 * 使用预测运行时 SDK 客户端库可查询发布的应用。

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>清理资源

可从 [LUIS 门户](https://www.luis.ai)删除应用并从 [Azure 门户](https://portal.azure.com/)删除 Azure 资源。

## <a name="troubleshooting"></a>疑难解答

* 对客户端库进行身份验证 - 身份验证错误通常表明使用了错误的密钥和终结点。 为了方便起见，此快速入门使用预测运行时的创作密钥和终结点，但只有在不超出每月配额的情况下才有效。 如果无法使用创作密钥和终结点，则需要在访问预测运行时 SDK 客户端库时使用预测运行时密钥和终结点。
* 创建实体 - 如果在创建本教程中使用的嵌套式机器学习实体时遇到错误，请确保已复制代码且未更改代码以创建其他实体。
* 创建示例言语 - 如果在创建本教程中使用的标记示例言语时遇到错误，请确保已复制代码且未更改代码以创建其他标记示例。
* 训练 - 如果收到一条训练错误，则通常表示该应用为空（没有带示例言语的意图），或表示该应用的意图或实体格式错误。
* 杂项错误 - 由于代码使用文本和 JSON 对象调用客户端库，所以请确保你没有更改代码。

其他错误 - 如果遇到以上列表中未涵盖的错误，请在此页面底部提供反馈，告知我们。 包括所安装的客户端库的编程语言和版本。

## <a name="next-steps"></a>后续步骤

* [什么是语言理解 (LUIS) API？](what-is-luis.md)
* [新增功能](whats-new.md)
* [意向](luis-concept-intent.md)、[实体](luis-concept-entity-types.md)、[示例言语](luis-concept-utterance.md)和[预生成实体](luis-reference-prebuilt-entities.md)
* 可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code) 上找到此示例的源代码。
* 了解自然语言：[自然语言理解 (NLU) 和自然语言处理 (NLP)](artificial-intelligence.md)
* 机器人：[AI 聊天机器人](luis-csharp-tutorial-bf-v4.md "聊天机器人生成器教程")
