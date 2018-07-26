---
title: Durable Functions 中的人机交互和超时 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 中处理人机交互和超时。
services: functions
author: kashimiz
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/11/2018
ms.author: azfuncdf
ms.openlocfilehash: a62baf64e35dfad55f76138e2f1aaef65dd434be
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036299"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Durable Functions 中的人机交互 - 电话验证示例

此示例演示如何生成涉及人机交互的 [Durable Functions](durable-functions-overview.md) 业务流程。 每当自动化过程中需要真人时，该过程需要能够以异步方式向人员发送通知和接收响应。 它还需要考虑该人员没有时间的可能。 （最后这一部分，超时变得很重要。）

这个示例实现了基于短信的电话验证系统。 验证客户的电话号码或进行多重身份验证 (MFA) 时，经常使用这些类型的流。 这是一个强有力的例子，因为整个实现是采用几个小型函数完成的。 无需外部数据存储（如数据库）。

## <a name="prerequisites"></a>先决条件

* [安装 Durable Functions](durable-functions-install.md)。
* 完成 [Hello 序列](durable-functions-sequence.md)演练。

## <a name="scenario-overview"></a>方案概述

电话验证用于验证应用程序的最终用户不是垃圾邮件发送者，且他们提供的是真实身份。 多重身份验证是保护用户帐户免受黑客攻击的常见用例。 实现自己的电话验证需要面临的挑战是，它需要与人进行有状态交互。 最终用户通常会获得一些代码（例如一个 4 位数字），且必须在合理的时间内响应。

普通的 Azure Functions 是无状态的（正如其他平台上的许多其他云终结点一样），因此这些类型的交互需要在外部的数据库或某种其他永久性存储中显式管理状态。 此外，需要将交互分解为多个可一起进行协调的函数。 例如，需要至少一个函数用于确定代码、将其持久保存在某个位置并发送至用户的电话。 此外，还需要至少一个其他函数来接收用户的响应，并以某种方式映射回原始函数调用，以实现代码验证。 超时也是保证安全的一个重要方面。 这可能很快就会变得相当复杂。

如果使用 Durable Functions，可大大降低此方案的复杂性。 如此示例中所示，业务流程协调程序函数可以轻松地管理有状态交互，且无需任何外部数据存储。 由于业务流程协调程序函数是持久的，因此这些交互流也非常可靠。

## <a name="configuring-twilio-integration"></a>配置 Twilio 集成

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>函数

本文通过示例应用介绍了以下函数：

* E4_SmsPhoneVerification
* E4_SendSmsChallenge

以下各部分介绍了用于 C# 脚本和 JavaScript 的配置和代码。 文章末尾展示了用于 Visual Studio 开发的代码。
 
## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>SMS 验证业务流程（Visual Studio Code 和 Azure 门户的示例代码） 

E4_SmsPhoneVerification 函数对业务流程协调程序函数使用标准的 function.json。

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

实现该函数的代码如下：

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript（仅限 Functions v2）

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

启动后，该业务流程协调程序函数执行以下任务：

1. 获取要向其发送短信通知的电话号码。
2. 调用 E4_SendSmsChallenge，向用户发送短信，并返回预期的 4 位数质询代码。
3. 创建可从当前时间开始触发 90 秒的持久计时器。
4. 与计时器一起，等待来自用户的 SmsChallengeResponse 事件。

用户会收到一条含 4 位数代码的短信。 用户需要在 90 秒内将相同的 4 位数代码发送回业务流程协调程序函数实例，以便完成验证过程。 如果提交的代码不正确，可额外尝试 3 次进行更正（在相同的 90 秒时间段内）。

> [!NOTE]
> 起初可能并不明显，但这个业务流程协调程序函数是完全确定的函数。 这是因为 `CurrentUtcDateTime` 属性用于计算计时器到期时间，且此属性在每次在业务流程协调程序代码中的此时点进行重播均返回相同的值。 必须确保对 `winner` 的每次重复调用均产生相同的 `Task.WhenAny` 结果。

> [!WARNING]
> 如果不再需要计时器到期，请务必[取消计时器](durable-functions-timers.md)，正如在上面的示例中收到质询响应后一样。

## <a name="send-the-sms-message"></a>发送短信

E4_SendSmsChallenge 函数使用 Twilio 绑定向最终用户发送包含 4 位数代码的短信。 function.json 定义如下：

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

以下代码可生成 4 位数质询代码和发送短信：

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript（仅限 Functions v2）

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

E4_SendSmsChallenge 函数仅被调用一次，即使进程崩溃或进行重播也是如此。 因为不希望最终用户收到多条短信，所以这种设定非常合适。 `challengeCode` 返回值可自动保留，以便业务流程协调程序函数始终了解正确的代码。

## <a name="run-the-sample"></a>运行示例

使用示例中包含的 HTTP 触发型函数，可以通过发送以下 HTTP POST 请求来启动业务流程：

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```
```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

   > [!NOTE]
   > 目前，JavaScript 业务流程启动器函数不能返回实例管理 URI。 此功能将在以后的版本中添加。

业务流程协调程序函数可接收提供的电话号码，并立即向其发送一条短信，其中包含随机生成的 4 位数验证代码 &mdash; 例如，2168。 然后函数等待 90 秒，获取响应。

若要使用该代码进行答复，可使用另一函数中的 `RaiseEventAsync`，或调用上面的 202 响应中引用的 sendEventUrl HTTP POST webhook，同时将 `{eventName}` 替换为事件的名称 `SmsChallengeResponse`：

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

如果在计时器到期前发送此代码，业务流程完成，且 `output` 字段设置为 `true`，表明验证成功。

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```
```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

如果计时器到期，或者 4 次输入错误的代码，可查询状态并看到业务流程函数输出为 `false`，表明电话验证失败。

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Visual Studio 示例代码

下面是 Visual Studio 项目中以单个 C# 文件形式提供的业务流程：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>后续步骤

本示例演示了 Durable Functions（特别是 `WaitForExternalEvent` 和 `CreateTimer`）的一些高级功能。 你已了解如何将这些功能与 `Task.WaitAny` 结合，实现可靠的超时系统，这通常对与真人进行交互非常有用。 可以通过阅读一系列深入讨论了特定主题的文章来了解有关如何使用 Durable Functions 的详细信息。

> [!div class="nextstepaction"]
> [转到此系列中的第一篇文章](durable-functions-bindings.md)
