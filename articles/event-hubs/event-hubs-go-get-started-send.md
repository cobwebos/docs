---
title: 使用 Go 将事件发送到 Azure 事件中心 | Microsoft Docs
description: 使用 Go 将事件发送到事件中心入门
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/18/2018
ms.author: shvija
ms.openlocfilehash: f5e30a103b09613caee8e9912a89a5bc2d390f65
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49458082"
---
# <a name="send-events-to-event-hubs-using-go"></a>使用 Go 将事件发送到事件中心

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

本教程介绍如何从 Go 编写的应用程序中将事件发送到事件中心。 

> [!NOTE]
> 可以从 [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs) 下载此用作示例的快速入门，将 `EventHubConnectionString` 和 `EventHubName` 字符串替换为事件中心值，并运行它。 或者，可以按照本教程中的步骤创建自己的解决方案。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

* 已本地安装 Go。 若有必要，请按照[以下说明操作](https://golang.org/doc/install)。
* 现有事件中心命名空间和事件中心。 请按[本文](event-hubs-create.md)中的说明创建以下实体。

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>创建事件中心命名空间和事件中心
第一步是使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 若要创建命名空间和事件中心，请按照[本文](event-hubs-create.md)中的步骤进行操作，然后继续执行本教程的以下步骤。

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

运行应用程序将事件发送到事件中心。 

祝贺你！ 现在已向事件中心发送消息。

## <a name="next-steps"></a>后续步骤
在此快速入门中，已使用 Go 向事件中心发送消息。 若要了解如何使用 Go 从事件中心接收事件，请参阅[从事件中心接收事件 - Go](event-hubs-go-get-started-receive-eph.md)。

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
