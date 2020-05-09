---
title: 快速入门：使用 Go 发送和接收事件 - Azure 事件中心
description: 快速入门：本文演示了创建 Go 应用程序的过程，可使用该应用程序从 Azure 事件中心发送事件。
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: e5f52d0ddbf9a66d974732d6d98ca8a5b09cc2d0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "73720590"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>快速入门：使用 Go 向/从事件中心发送/接收事件
Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

本教程介绍如何编写用于向/从事件中心发送/接收事件的 Go 应用程序。 

> [!NOTE]
> 可以从 [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs) 下载此用作示例的快速入门，将 `EventHubConnectionString` 和 `EventHubName` 字符串替换为事件中心值，并运行它。 或者，可以按照本教程中的步骤创建自己的解决方案。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

- 已本地安装 Go。 若有必要，请按照[以下说明操作](https://golang.org/doc/install)。
- 有效的 Azure 帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][]。
- **创建事件中心命名空间和事件中心**。 使用 [Azure 门户](https://portal.azure.com)创建事件中心类型的命名空间，并获取应用程序与事件中心进行通信所需的管理凭据。 要创建命名空间和事件中心，请按照[此文](event-hubs-create.md)中的步骤操作。

## <a name="send-events"></a>发送事件
本部分介绍如何创建一个向事件中心发送事件的 Go 应用程序。 

### <a name="install-go-package"></a>安装 Go 包

使用 `go get` 或 `dep` 获取事件中心的 Go 包。 例如：

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>在代码文件中导入包

若要导入 Go 包，请使用下面的代码示例：

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>创建服务主体

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

### <a name="create-event-hubs-client"></a>创建事件中心客户端

以下代码创建事件中心客户端：

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>编写发送消息的代码

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
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>附加信息

获取事件中心内的分区 ID：

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

运行应用程序将事件发送到事件中心。 

祝贺你！ 现在已向事件中心发送消息。

## <a name="receive-events"></a>接收事件

### <a name="create-a-storage-account-and-container"></a>创建存储帐户和容器

使用 Azure 存储容器在接收器之间共享分区租用和事件流中的检查点等状态。 可以使用 Go SDK 创建存储帐户和容器，但也可以按照[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)中的说明进行创建。

[Go 示例存储库](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)中和教程对应的示例中提供了使用 Go SDK 创建存储项目的示例。

### <a name="go-packages"></a>Go 包

若要接收消息，请使用 `go get` 或 `dep` 获取事件中心的 Go 包：

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>在代码文件中导入包

若要导入 Go 包，请使用下面的代码示例：

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>创建服务主体

按照[使用 Azure CLI 2.0 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)中的说明创建新的服务主体。 使用以下名称将提供的凭据保存在环境中：预先配置了 Azure SDK for Go 和事件中心包，以便查找这些变量名称。

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

接下来，为使用这些凭据的事件中心客户端创建授权提供程序：

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>获取元数据结构

使用 Azure Go SDK 获取 Azure 环境相关元数据的结构。 稍后的操作将使用此结构来查找正确的终结点。

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>创建凭据帮助程序 

创建使用上述 Azure Active Directory (AAD) 凭据的凭据帮助程序，为存储创建共享访问签名 (SAS) 凭据。 最后一个参数指示构造函数使用与之前所用相同的环境变量：

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>创建检查指针和租赁方 

创建一个负责将分区租赁给特定接收方的**租赁方**，并创建一个负责编写消息流检查点，使其他接收方可以开始从正确偏移量读取的**检查指针**。

目前，提供使用相同存储容器的单个 StorageLeaserCheckpointer 来管理租用和检查点  。 除存储帐户和容器名称外，StorageLeaserCheckpointer 还需要上一步中创建的凭据和 Azure 结构，才能正确访问容器  。

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>构造事件处理器主机

现已拥有构造 EventProcessorHost 所需的部分，如下所示。 同一 **StorageLeaserCheckpointer** 同时用作租赁方和检查指针，如前所述：

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>创建处理程序 

现在创建一个处理程序并将其注册到事件处理器主机。 启动主机时，它将向传入消息应用此设置和其他任何指定的处理程序：

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>编写接收消息的代码

一切就绪后，便可使用 `Start(context)` 启动事件处理器主机，使其永久运行，或使用 `StartNonBlocking(context)` 使其仅在消息可用时运行。

本教程将如下启动并运行，请参阅 GitHub 示例，了解使用 `StartNonBlocking` 的示例：

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>后续步骤
请阅读以下文章：

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure 事件中心的功能和术语](event-hubs-features.md)
- [事件中心常见问题解答](event-hubs-faq.md)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
