---
title: 使用 Go 从 Azure 事件中心接收事件 | Microsoft Docs
description: 使用 Go 从 Azure 事件中心接收事件入门
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: eaea6adbaef7baf9bb1e617ba0a709cf14edf781
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005572"
---
# <a name="receive-events-from-event-hubs-using-go"></a>使用 Go 从事件中心接收事件

Azure 事件中心是一个具备高度伸缩性的事件管理系统，每秒可处理大量事件，从而使应用程序能够处理和分析连接设备和其他系统所产生的海量数据。 收集到事件中心后，可使用进程内处理程序或通过转发到其他分析系统，接收和处理事件。

若要了解有关事件中心的详细信息，请参阅[事件中心概述][Event Hubs overview]。

本教程介绍如何在 Go 应用程序中从事件中心接收事件。 若要了解如何发送事件，请参阅[相应的发送文章](event-hubs-go-get-started-send.md)。

本教程中的代码取自[以下 GitHub 示例](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs)，可以检查这些代码，查看包括导入语句和变量声明的功能完善的应用程序。

其他示例[在事件中心包存储库](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples)中提供。

## <a name="prerequisites"></a>先决条件

若要完成本教程，需要具备以下先决条件：

* 已本地安装 Go。 若有必要，请按照[以下说明操作](https://golang.org/doc/install)。
* 有效的 Azure 帐户。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户][]。
* 若要接收消息，目标事件中心内必须具有消息。 了解如何发送消息，请参阅[发送教程](event-hubs-go-get-started-send.md)。
* 现有事件中心（请参阅下一部分）。
* 现有存储帐户和容器（请参阅下面的第二部分）。

### <a name="create-an-event-hub"></a>创建事件中心

本教程从现有的事件中心命名空间和事件中心开始。 请按[本文](event-hubs-create.md)中的说明创建以下实体。

### <a name="create-a-storage-account-and-container"></a>创建存储帐户和容器

使用 Azure 存储容器在接收器之间共享分区租用和事件流中的检查点等状态。 可以使用 Go SDK 创建存储帐户和容器，但也可以按照[关于 Azure 存储帐户](../storage/common/storage-create-storage-account.md)中的说明进行创建。

[Go 示例存储库](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)中和教程对应的示例中提供了使用 Go SDK 创建存储项目的示例。

## <a name="receive-messages"></a>接收消息

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

## <a name="import-packages-in-your-code-file"></a>在代码文件中导入包

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

## <a name="create-service-principal"></a>创建服务主体

按照[使用 Azure CLI 2.0 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)中的说明创建新的服务主体。 使用以下名称将提供的凭据保存在环境中。 预先配置 Azure SDK for Go 和事件中心包，以便查找这些变量名称。

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

## <a name="get-metadata-struct"></a>获取元数据结构

使用 Azure Go SDK 获取 Azure 环境相关元数据的结构。 稍后的操作将使用此结构来查找正确的终结点。

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

## <a name="create-credential-helper"></a>创建凭据帮助程序 

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

## <a name="create-checkpointer-and-leaser"></a>创建检查点和租用 

创建租用，负责将分区租给特定接收器；创建检查点，负责编写消息流的检查点，以便其他接收器可开始读取正确的偏移量。

目前，提供使用相同存储容器的单个 StorageLeaserCheckpointer 来管理租用和检查点。 除存储帐户和容器名称外，StorageLeaserCheckpointer 还需要上一步中创建的凭据和 Azure 结构，才能正确访问容器。

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

## <a name="construct-event-processor-host"></a>构造事件处理器主机

现已拥有构造 EventProcessorHost 所需的部分，如下所示。 使用同一 StorageLeaserCheckpointer 作为租用和检查点，如上文中所述：

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

## <a name="create-handler"></a>创建处理程序 

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

## <a name="receive-messages"></a>接收消息

一切就绪后，便可使用 `Start(context)` 启动事件处理器主机，使其永久运行，或使用 `StartNonBlocking(context)` 使其仅在消息可用时运行。

本教程将如下启动并运行，请参阅 GitHub 示例，了解使用 `StartNonBlocking` 的示例：

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="notes"></a>说明

本教程使用单个 EventProcessorHost 实例。 若要增加吞吐量和可靠性，应在不同系统上运行多个 EventProcessorHost 实例。 租用系统可确保仅关联一个接收器，并且接收指定时间指定分区中的消息。

## <a name="next-steps"></a>后续步骤

若要了解有关事件中心的详细信息，请访问以下页面：

* [使用 Go 发送事件](event-hubs-go-get-started-send.md)
* [事件中心概述](event-hubs-about.md)
* [创建事件中心](event-hubs-create.md)
* [事件中心常见问题解答](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
