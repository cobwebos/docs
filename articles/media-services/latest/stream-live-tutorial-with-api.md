---
title: 通过 Azure 媒体服务 v3 进行实时流式传输 | Microsoft Docs
description: 本教程详述了使用 .NET Core 通过媒体服务 v3 进行实时流式传输的步骤。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 12/28/2018
ms.author: juliako
ms.openlocfilehash: 858c062c2b3d61b38247e323bf70d2768d33b257
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969329"
---
# <a name="tutorial-stream-live-with-media-services-v3-using-apis"></a>教程：使用 API 通过媒体服务 v3 进行实时流式传输

在 Azure 媒体服务中，[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) 负责处理实时传送视频流内容。 LiveEvent 提供输入终结点（引入 URL），然后由你将该终结点提供给实时编码器。 LiveEvent 从实时编码器接收实时输入流，并通过一个或多个 [StreamingEndpoints](https://docs.microsoft.com/rest/api/media/streamingendpoints) 使其可用于流式处理。 LiveEvents 还提供可用于预览的预览终结点（预览 URL），并在进一步处理和传递流之前对流进行验证。 本教程演示如何使用 .NET Core 创建**直通**类型的直播事件。 

> [!NOTE]
> 确保在继续操作之前查看[使用媒体服务 v3 的实时传送视频流](live-streaming-overview.md)。 

本教程介绍如何：    

> [!div class="checklist"]
> * 访问媒体服务 API
> * 配置示例应用
> * 检查执行实时传送视频流的代码
> * 使用 [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) 在 http://ampdemo.azureedge.net 观看事件
> * 清理资源

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>先决条件

以下是完成本教程所需具备的条件。

- 安装 Visual Studio Code 或 Visual Studio。
- 在本地安装并使用 CLI，本文要求使用 Azure CLI 2.0 或更高版本。 运行 `az --version` 即可确定你拥有的版本。 如需进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。 

    目前，并非所有[媒体服务 v3 CLI](https://aka.ms/ams-v3-cli-ref) 命令都可在 Azure Cloud Shell 中运行。 建议在本地使用 CLI。

- [创建媒体服务帐户](create-account-cli-how-to.md)。

    请务必记住用于资源组名称和媒体服务帐户名称的值

- 一个用于广播事件的相机或设备（例如便携式计算机）。
- 一个本地实时编码器，用于将信号从相机转换为发送至媒体服务实时传送视频流服务的流。 流必须为 **RTMP** 或“平滑流式处理”格式。

## <a name="download-the-sample"></a>下载示例

使用以下命令将包含流式处理 .NET 示例的 GitHub 存储库克隆到计算机：  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```

实时传送视频流示例位于 [Live](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/Live/MediaV3LiveApp) 文件夹中。

> [!IMPORTANT]
> 此示例为每个资源使用唯一的后缀。 如果取消调试操作或者中途终止应用，则最终会在帐户中有多个 LiveEvent。 <br/>
> 确保停止正在运行的 LiveEvent， 否则需**付费**！

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="examine-the-code-that-performs-live-streaming"></a>检查执行实时传送视频流的代码

此部分讨论 MediaV3LiveApp 项目的 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs) 文件中定义的函数。

此示例为每个资源创建唯一的后缀，因此即使在没有清理的情况下运行示例多次，也不会有名称冲突。

> [!IMPORTANT]
> 此示例为每个资源使用唯一的后缀。 如果取消调试操作或者中途终止应用，则最终会在帐户中有多个 LiveEvent。 <br/>
> 确保停止正在运行的 LiveEvent， 否则需**付费**！
 
### <a name="start-using-media-services-apis-with-net-sdk"></a>开始结合使用媒体服务 API 与 .NET SDK

若要开始将媒体服务 API 与 .NET 结合使用，需要创建 AzureMediaServicesClient 对象。 若要创建对象，需要提供客户端所需凭据以使用 Azure AD 连接到 Azure。 在本文开头克隆的代码中，**GetCredentialsAsync** 函数根据本地配置文件中提供的凭据创建 ServiceClientCredentials 对象。 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>创建直播事件

此部分介绍如何创建“直通”类型的 LiveEvent（LiveEventEncodingType 设置为“无”）。 若要创建为实时编码启用的 LiveEvent，请将 LiveEventEncodingType 设置为“标准”。 

可能需要在创建直播事件时指定的一些其他事项包括：

* 媒体服务位置 
* 直播事件的流式处理协议（目前支持 RTMP 和平滑流式处理协议）
       
    运行 LiveEvent 或其关联的 LiveOutput 时，不能更改协议选项。 如果需要其他协议，应当为每个流式处理协议创建单独的 LiveEvent。  
* 对引入和预览的 IP 限制。 可定义允许向该 LiveEvent 引入视频的 IP 地址。 允许的 IP 地址可以指定为单个 IP 地址（例如“10.0.0.1”）、使用一个 IP 地址和 CIDR 子网掩码的 IP 范围（例如“10.0.0.1/22”）或使用一个 IP 地址和点分十进制子网掩码的 IP 范围（例如“10.0.0.1(255.255.252.0)”）。
    
    如果未指定 IP 地址并且没有规则定义，则不会允许任何 IP 地址。 若要允许任何 IP 地址，请创建一个规则并设置 0.0.0.0/0。

创建事件时，可以将其启动方式指定为自动启动。 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>获取引入 URL

创建 LiveEvent 后，可以获得要提供给实时编码器的引入 URL。 编码器将使用这些 URL 来输入实时流。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>获取预览 URL

使用 previewEndpoint 预览来自编码器的输入并验证其是否已确实收到。

> [!IMPORTANT]
> 确保视频流向预览 URL，然后再继续操作！

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-liveevents-and-liveoutputs"></a>创建并管理 LiveEvent 和 LiveOutput

将流传输到 LiveEvent 后，可以通过创建资产、LiveOutput 和 StreamingLocator 来启动流式传输事件。 这会存档流，并使观看者可通过 StreamingEndpoint 使用该流。 

#### <a name="create-an-asset"></a>创建资产

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateAsset)]

创建供 LiveOutput 使用的资产。

#### <a name="create-a-liveoutput"></a>创建 LiveOutput

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaminglocator"></a>创建 StreamingLocator

> [!NOTE]
> 创建媒体服务帐户后，会将一个处于“已停止”状态的**默认**流式处理终结点添加到帐户。 若要开始流式传输内容并利用动态打包和动态加密，要从中流式传输内容的流式处理终结点必须处于“正在运行”状态。 

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CreateStreamingLocator)]

```csharp

// Get the url to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="cleaning-up-resources-in-your-media-services-account"></a>清理媒体服务帐户中的资源

如果已完成流式处理事件，并想要清理先前设置的资源，请遵循以下过程。

* 停止从编码器推送流。
* 停止 LiveEvent。 停止 LiveEvent 后，不会产生任何费用。 当需要重新启动它时，它会采用相同的引入 URL，因此，无需重新配置编码器。
* 除非要继续以点播流形式提供直播事件的存档，否则可以停止 StreamingEndpoint。 如果 LiveEvent 处于停止状态，不会产生任何费用。

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/Live/MediaV3LiveApp/Program.cs#CleanupAccount)]   

## <a name="watch-the-event"></a>观看事件

若要观看事件，请复制流式传输 URL（在运行[创建 StreamingLocator](#create-a-streaminglocator) 中所述的代码时获得），然后使用所选播放器。 可以使用 [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) 来测试 http://ampdemo.azureedge.net 中的流。 

停止时，实时事件会自动转换为点播内容。 即使你停止并删除了事件，只要没有删除资产，用户也能够按需将已存档内容作为视频进行流式传输。 如果资产被某个事件使用，则无法将其删除，必须先删除该事件。 

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组中的任何一个资源（包括为本教程创建的媒体服务和存储帐户），请删除之前创建的资源组。

执行以下 CLI 命令：

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> 让 LiveEvent 保持运行状态会产生账单费用。 注意，如果项目/节目崩溃或因某种原因而关闭，则可能导致 LiveEvent 始终处于产生费用的运行状态。

## <a name="next-steps"></a>后续步骤

[对文件进行流式处理](stream-files-tutorial-with-api.md)

