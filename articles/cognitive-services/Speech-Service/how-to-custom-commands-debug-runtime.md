---
title: 运行自定义命令应用程序时调试错误
titleSuffix: Azure Cognitive Services
description: 本文介绍如何在自定义命令应用程序中调试运行时错误。
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 1c9b0b48c7862990cfa2c8ba38bde0851058a228
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86023017"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>运行自定义命令应用程序时调试错误

本文介绍在运行自定义命令应用程序时出现错误时如何进行调试。 

## <a name="connection-failed"></a>连接失败

如果你从[客户端应用程序（通过语音 SDK）](./how-to-custom-commands-setup-speech-sdk.md)或[Windows 语音助手客户端](./how-to-custom-commands-developer-flow-test.md)运行自定义命令应用程序，你可能会遇到如下所示的连接错误：

| 错误代码 | 详细信息 |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure： WebSocket 升级失败，出现身份验证错误 |
| [1002](#error-1002)] | 当需要状态代码 "101" 时，服务器返回了状态代码 "404"。 |

### <a name="error-401"></a>错误 401
- 客户端应用程序中指定的区域与自定义命令应用程序的区域不匹配

- 语音资源密钥无效
    
    请确保语音资源键正确。

### <a name="error-1002"></a>错误1002 
- 自定义命令应用程序未发布
    
    在门户中发布应用程序。

- 自定义命令 applicationId 无效

    请确保自定义命令应用程序 ID 是正确的。
 语音资源外的自定义命令应用程序

    请确保在语音资源下创建自定义命令应用程序。

有关排查连接问题的详细信息，请参阅[Windows 语音助手客户端故障排除](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting)


## <a name="dialog-is-canceled"></a>对话框已取消

运行自定义命令应用程序时，会在出现一些错误时取消该对话框。

- 如果在门户中测试应用程序，它会直接显示取消说明，并 earcon 错误。 

- 如果使用[Windows 语音助手客户端](./how-to-custom-commands-developer-flow-test.md)运行应用程序，则会出现错误 earcon。 可以在**活动日志**下找到**事件： CancelledDialog** 。

- 如果你使用的是客户端应用程序示例[客户端应用程序（使用 SPEECH SDK）](./how-to-custom-commands-setup-speech-sdk.md)，它将会 earcon 错误。 可以在 "**状态**" 下找到**事件： CancelledDialog** 。

- 如果要构建自己的客户端应用程序，你始终可以设计所需的逻辑来处理 CancelledDialog 事件。

CancelledDialog 事件由取消代码和说明组成，如下所示：

| 取消代码 | 取消说明 |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | 超出允许的最大数目后未进行任何进度 |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | 超出了识别器使用配额 |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | 与识别器的连接失败 |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | 无法通过当前订阅访问此应用程序 |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | 输入超出了识别器支持的最大长度 |
| [RecognizerNotFound](#recognizer-not-found) | 找不到识别器 |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | 识别器的查询无效 |
| [RecognizerError](#recognizer-return-an-error) | 识别器返回错误 |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>超出允许的最大数目后未进行任何进度
当所需的槽在一定数量的时间之后未成功更新时，将取消该对话框。 最大内部版本号为3。

### <a name="recognizer-usage-quota-exceeded"></a>超出了识别器使用配额
语言理解（LUIS）对资源使用限制。 通常，原因可能是 "识别器使用配额超出错误"： 
- 你的 LUIS 创作超出限制

    将预测资源添加到自定义命令应用程序： 
    1. 中转到 "**设置**"、"LUIS 资源"
    1. 从**预测资源**中选择预测资源，或单击 "**创建新资源**" 

- LUIS 预测资源超出限制

    如果你使用的是 F0 预测资源，则它的限制为10千/月，5个查询/秒。

有关 LUIS 资源限制的详细信息，请参阅[语言理解资源使用情况和限制](https://docs.microsoft.com/azure/cognitive-services/luis/luis-limits#resource-usage-and-limits)

### <a name="connection-to-the-recognizer-failed"></a>与识别器的连接失败
通常，这意味着暂时性连接无法语言理解（LUIS）识别器。 请重试，并解决问题。

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>无法通过当前订阅访问此应用程序
你的订阅无权访问 LUIS 应用程序。 

### <a name="input-exceeds-the-maximum-supported-length"></a>输入超出支持的最大长度
输入超过500个字符。 对于输入查询文本，我们仅允许最多500个字符。

### <a name="invalid-query-for-the-recognizer"></a>识别器的查询无效
输入超过500个字符。 对于输入查询文本，我们仅允许最多500个字符。

### <a name="recognizer-return-an-error"></a>识别器返回错误
LUIS 识别器在尝试识别输入时返回了错误。

### <a name="recognizer-not-found"></a>找不到识别器
在您的自定义命令对话框模型中找不到指定的识别器类型。 目前仅支持[语言理解（LUIS）识别器](https://www.luis.ai/)。

## <a name="other-common-errors"></a>其他常见错误
### <a name="unexpected-response"></a>意外响应
意外的响应可能导致了多个问题。 首先要检查以下内容：
- "是/否" 示例句子

    由于在使用 with 确认功能时，我们目前不支持 "是/否"。 不会检测到示例句子中定义的 "是/否"。

- 命令中的类似方法和示例句子

    当两个命令共享类似意向和示例句子时，LUIS 识别准确性可能会受到影响。 您可以尝试使命令功能和示例句子尽可能不同。

    若要获得提高识别准确性的最佳实践，请参阅[LUIS 最佳实践](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices)。

- 对话框已取消
    
    在上述部分中检查取消的原因。

### <a name="error-while-rendering-the-template"></a>呈现模板时出错
语音响应中使用了未定义的参数。 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>对象引用未设置为对象的实例
在 "**将活动发送到客户端**" 操作中定义的 JSON 有效负载中，有一个空参数。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [请参阅 GitHub 上的示例](https://aka.ms/speech/cc-samples)