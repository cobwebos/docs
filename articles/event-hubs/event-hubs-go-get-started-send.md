---
title: 使用 Go 将事件发送到 Azure 事件中心 | Microsoft Docs
description: 使用 Go 将事件发送到事件中心入门
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: 40b3aa82c3e9e8ab9a30362c0a41998877655725
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005591"
---
# <a name="send-events-to-event-hubs-using-go"></a>使用 Go 将事件发送到事件中心

Azure 事件中心是一个具备高度伸缩性的事件管理系统，每秒可处理大量事件，从而使应用程序能够处理和分析连接设备和其他系统所产生的海量数据。 收集到事件中心后，可使用进程内处理程序或通过转发到其他分析系统，接收和处理事件。

若要了解有关事件中心的详细信息，请参阅[事件中心概述][Event Hubs overview]。

本教程介绍如何从 Go 编写的应用程序中将事件发送到事件中心。 若要接收事件，请使用 Go eph（事件处理器主机）包，如[相应的接收文章](event-hubs-go-get-started-receive-eph.md)中所述。

本教程中的代码取自[以下 GitHub 示例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs)，可以检查这些代码，查看包括导入语句和变量声明的功能完善的应用程序。

其他示例[在事件中心包存储库](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples)中提供。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

* 已本地安装 Go。 若有必要，请按照[以下说明操作](https://golang.org/doc/install)。
* 有效的 Azure 帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][]。
* 现有事件中心命名空间和事件中心。 请按[本文](event-hubs-create.md)中的说明创建以下实体。

## <a name="install-go-package"></a>安装 Go 包

使用 `go get` 或 `dep` 获取事件中心的 Go 包。 例如：

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

## <a name="import-packages-in-your-code-file"></a>在代码文件中导入包

若要导入 Go 包，请使用下面的代码示例：

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

## <a name="create-service-principal"></a>创建服务主体

按照[使用 Azure CLI 2.0 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)中的说明创建新的服务主体。 使用以下名称将提供的凭据保存在环境中。 同时预先配置 Azure SDK for Go 和事件中心包，查找以下变量名称：

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

现在，为使用这些凭据的事件中心客户端创建授权提供程序：

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="create-event-hubs-client"></a>创建事件中心客户端

以下代码创建事件中心客户端：

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

## <a name="send-messages"></a>发送消息

在以下片段中，使用 (1) 从终端以交互方式发送消息或使用 (2) 在程序中发送消息：

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!")
```

## <a name="extras"></a>附加信息

获取事件中心内的分区 ID：

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n, info.PartitionIDs)
```

## <a name="next-steps"></a>后续步骤

若要了解有关事件中心的详细信息，请访问以下页面：

* [使用 EventProcessorHost 接收事件](event-hubs-go-get-started-receive-eph.md)
* [事件中心概述][Event Hubs overview]
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题解答](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
