---
title: Azure Durable Functions 单元测试
description: 了解如何进行 Durable Functions 单元测试。
services: functions
author: kadimitr
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/28/2018
ms.author: kadimitr
ms.openlocfilehash: 7de9a6f0d4dfcb45932b89504c0d38c3c70283e9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33762754"
---
# <a name="durable-functions-unit-testing"></a>Durable Functions 单元测试

单元测试是现代软件开发实践中的重要组成部分。 单元测试可验证业务逻辑行为，防止将来引入无法察觉的中断性变更。 Durable Functions 的复杂性很容易增大，因此，引入单元测试有助于避免中断性变更。 以下部分介绍如何对三种函数类型执行单元测试 - 业务流程客户端、业务流程协调程序和活动函数。 

## <a name="prerequisites"></a>先决条件

学习本文中的示例需要了解以下概念和框架： 

* 单元测试

* Durable Functions 

* [xUnit](https://xunit.github.io/) - 测试框架

* [moq](https://github.com/moq/moq4) - 模拟框架


## <a name="base-classes-for-mocking"></a>用于模拟的基类 

支持通过 Durable Functions 中的两个抽象类进行单元测试：

* [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) 

* [DurableOrchestrationContextBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContextBase.html)

这些类是定义业务流程客户端和业务流程协调程序方法的 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 和 [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) 的基类。 模拟将会设置基类方法的预期行为，使单元测试能够验证业务逻辑。 可以通过一个两步工作流对业务流程客户端和业务流程协调程序中的业务逻辑进行单元测试：

1. 定义业务流程客户端和业务流程协调程序的签名时，使用基类而不是具体的实现。
2. 在单元测试中模拟基类的行为，并验证业务逻辑。 

在以下段落中，可以找到有关对使用业务流程客户端绑定和业务流程协调程序触发器绑定的函数进行测试的更多详细信息。

## <a name="unit-testing-trigger-functions"></a>对触发器函数进行单元测试

在此部分，单元测试将会验证用于启动新业务流程的以下 HTTP 触发器函数的逻辑。

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

单元测试任务是验证响应有效负载中提供的 `Retry-After` 标头的值。 因此，单元测试将模拟某些 [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) 方法，以确保行为可预测。 

首先需要基类的模拟，即 [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html)。 该模拟可以是实现 [DurableOrchestrationClientBase](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClientBase.html) 的新类。 但是，使用 [moq](https://github.com/moq/moq4) 之类的模拟框架可以简化过程：    

```csharp
    // Mock DurableOrchestrationClientBase
    var durableOrchestrationClientBaseMock = new Mock<DurableOrchestrationClientBase>();
```

然后，模拟 `StartNewAsync` 方法来返回已知的实例 ID。

```csharp
    // Mock StartNewAsync method
    durableOrchestrationClientBaseMock.
        Setup(x => x.StartNewAsync(functionName, It.IsAny<object>())).
        ReturnsAsync(instanceId);
```

接下来模拟 `CreateCheckStatusResponse`，以便始终返回空白的 HTTP 200 响应。

```csharp
    // Mock CreateCheckStatusResponse method
    durableOrchestrationClientBaseMock
        .Setup(x => x.CreateCheckStatusResponse(It.IsAny<HttpRequestMessage>(), instanceId))
        .Returns(new HttpResponseMessage
        {
            StatusCode = HttpStatusCode.OK,
            Content = new StringContent(string.Empty),
        });
```

另外还要模拟 `TraceWriter`：

```csharp
    // Mock TraceWriter
    var traceWriterMock = new Mock<TraceWriter>(TraceLevel.Info);

```  

现在，从单元测试调用 `Run` 方法：

```csharp
    // Call Orchestration trigger function
    var result = await HttpStart.Run(
        new HttpRequestMessage()
        {
            Content = new StringContent("{}", Encoding.UTF8, "application/json"),
            RequestUri = new Uri("http://localhost:7071/orchestrators/E1_HelloSequence"),
        },
        durableOrchestrationClientBaseMock.Object, 
        functionName,
        traceWriterMock.Object);
 ``` 

 最后一步是将输出与预期值进行比较：

```csharp
    // Validate that output is not null
    Assert.NotNull(result.Headers.RetryAfter);

    // Validate output's Retry-After header value
    Assert.Equal(TimeSpan.FromSeconds(10), result.Headers.RetryAfter.Delta);
```

合并所有步骤后，单元测试将获得以下代码： 

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HttpStartTests.cs)]

## <a name="unit-testing-orchestrator-functions"></a>对业务流程协调程序函数进行单元测试

业务流程协调程序包含的业务逻辑通常要多得多，因此，它们的单元测试更有趣。

在本部分，单元测试将验证 `E1_HelloSequence` 业务流程协调程序函数的输出：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

单元测试代码首先会创建模拟：

```csharp
    var durableOrchestrationContextMock = new Mock<DurableOrchestrationContextBase>();
```

然后模拟活动方法调用：

```csharp
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Tokyo")).ReturnsAsync("Hello Tokyo!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "Seattle")).ReturnsAsync("Hello Seattle!");
    durableOrchestrationContextMock.Setup(x => x.CallActivityAsync<string>("E1_SayHello", "London")).ReturnsAsync("Hello London!");
```

接下来，单元测试调用 `HelloSequence.Run` 方法：

```csharp
    var result = await HelloSequence.Run(durableOrchestrationContextMock.Object);
```

最后验证输出：

```csharp
    Assert.Equal(3, result.Count);
    Assert.Equal("Hello Tokyo!", result[0]);
    Assert.Equal("Hello Seattle!", result[1]);
    Assert.Equal("Hello London!", result[2]);
```

合并所有步骤后，单元测试将获得以下代码：

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceOrchestratorTests.cs)]

## <a name="unit-testing-activity-functions"></a>对活动函数进行单元测试

可以像测试非持久性函数一样对活动函数进行单元测试。 活动函数没有用于模拟的基类。 因此，单元测试直接使用参数类型。

在本部分，单元测试将验证 `E1_SayHello` 活动函数的行为：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

另外，单元测试将验证输出格式：

[!code-csharp[Main](~/samples-durable-functions/samples/VSSample.Tests/HelloSequenceActivityTests.cs)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解 xUnit](http://xunit.github.io/docs/getting-started-dotnet-core)

> [详细了解 moq](https://github.com/Moq/moq4/wiki/Quickstart)