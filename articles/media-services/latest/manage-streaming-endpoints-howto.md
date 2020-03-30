---
title: 使用 Azure 媒体服务 v3 管理流式处理终结点
description: 本文演示如何使用 Azure 媒体服务 v3 管理流式处理终结点。
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2020
ms.author: juliako
ms.openlocfilehash: 75ba2ad87eabd7ff6b0625ad95ab24a8ae58dd0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461038"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>使用媒体服务 v3 管理流式处理终结点

创建媒体服务帐户时，**默认**[流式处理终结点](streaming-endpoint-concept.md)将添加到处于 **"已停止"** 状态的帐户。 要开始流式传输内容并利用[动态打包](dynamic-packaging-overview.md)和[动态加密](content-protection-overview.md)，要从中流式传输内容的流式处理终结点必须处于 **"正在运行"** 状态。

本文介绍如何使用不同的技术在流式处理终结点上执行[Start](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)命令。 
 
> [!NOTE]
> 仅当流式处理终结点处于运行状态时才进行计费。
    
## <a name="prerequisites"></a>先决条件

检讨： 

* [媒体服务概念](concepts-overview.md)
* [流式处理终结点概念](streaming-endpoint-concept.md)
* [动态打包](dynamic-packaging-overview.md)

## <a name="use-rest"></a>使用 REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

有关详细信息，请参阅： 

* [启动流式处理终结点](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)参考文档。
* 启动流式处理终结点是异步操作。 

    有关如何监视长时间运行的操作的信息，请参阅[长时间运行的操作](media-services-apis-overview.md)。
* 此[Postman 集合](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json)包含多个 REST 操作的示例，包括有关如何启动流式处理终结点的示例。

## <a name="use-the-azure-portal"></a>使用 Azure 门户 
 
1. 登录到 Azure[门户](https://portal.azure.com/)。
1. 转到 Azure 媒体服务帐户。
1. 在左侧窗格中，选择**流式处理终结点**。
1. 选择要启动的流式处理终结点，然后选择 **"开始**"。

## <a name="use-the-azure-cli"></a>使用 Azure CLI

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

有关详细信息，请参阅[az ams 流终结点开始](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start)。

## <a name="use-sdks"></a>使用 SDK

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

请参阅完整的[Java 代码示例](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128)。

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

请参阅完整的[.NET 代码示例](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112)。

---

## <a name="next-steps"></a>后续步骤

* [媒体服务 v3 OpenAPI 规范（摇摆）](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [流式处理终结点操作](https://docs.microsoft.com/rest/api/media/streamingendpoints)
