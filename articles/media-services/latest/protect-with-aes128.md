---
title: 用 AES 加密视频-128
titleSuffix: Azure Media Services
description: 了解如何使用 AES 128 位加密加密视频，以及如何在 Azure 媒体服务中使用密钥传送服务。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: juliako
ms.openlocfilehash: 126700e6290650221a9cb9711b22472301409fca
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974166"
---
# <a name="tutorial-encrypt-video-with-aes-128-and-use-the-key-delivery-service"></a>教程：使用 AES-128 加密视频并使用密钥传送服务

> [!NOTE]
> 尽管本教程使用了 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) 示例，但 [REST API](https://docs.microsoft.com/rest/api/media/liveevents)、[CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest) 或其他受支持的 [SDK](media-services-apis-overview.md#sdks) 的常规步骤是相同的。

借助媒体服务，可以传送使用 AES 通过 128 位加密密钥加密的 HTTP Live Streaming (HLS)、MPEG-DASH 和平滑流。 媒体服务还提供密钥传送服务，将加密密钥传送给已授权的用户。 如果希望媒体服务动态加密视频，请将加密密钥与流式处理定位符相关联，同时配置内容密钥策略。 当播放器请求流时，媒体服务将使用指定的密钥通过 AES-128 动态加密你的内容。 为解密流，播放器从密钥传送服务请求密钥。 为了确定是否已授权用户获取密钥，服务将评估你为密钥指定的内容密钥策略。

可以使用多个加密类型（AES-128、PlayReady、Widevine、FairPlay）来加密每个资产。 请参阅[流式处理协议和加密类型](content-protection-overview.md#streaming-protocols-and-encryption-types)，以了解有效的组合方式。 另请参阅[如何用 DRM 进行保护](protect-with-drm.md)。

此示例的输出包含用于播放内容所需的 Azure Media Player、清单 URL 和 AES 令牌的 URL。 此示例将 JSON Web Token （JWT）令牌的到期时间设置为1小时。 可以打开浏览器并粘贴生成的 URL 来启动 Azure Media Player 演示页，其中已经填充了该 URL 和令牌（采用以下格式：```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```）。

本教程演示如何：

> [!div class="checklist"]
> * 下载本文中所述的[EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES)示例。
> * 开始在 .NET SDK 中使用媒体服务 Api。
> * 创建输出资产。
> * 创建编码转换。
> * 提交作业。
> * 等待作业完成。
> * 创建内容密钥策略
> * 将策略配置为使用 JWT 令牌限制。
> * 创建流式处理定位符。
> * 配置流式处理定位符，以通过 AES （ClearKey）加密视频。
> * 获取测试令牌。
> * 生成流 URL。
> * 清理资源。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必备组件

以下是完成本教程所需具备的条件。

* 查看[内容保护概述](content-protection-overview.md)一文。
* 安装 Visual Studio Code 或 Visual Studio。
* [创建媒体服务帐户](create-account-cli-quickstart.md)。
* 使用[访问 api](access-api-cli-how-to.md)获取使用媒体服务 api 时所需的凭据。

## <a name="download-code"></a>下载代码

使用以下命令，将包含本文中所述完整 .NET 示例的 GitHub 存储库克隆到计算机：

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```

“使用 AES-128 加密”示例位于 [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) 文件夹中。

> [!NOTE]
> 该示例将在每次运行应用时创建唯一资源。 通常，将重复使用现有资源，如转换和策略（如果现有资源具有所需的配置）。

## <a name="start-using-media-services-apis-with-net-sdk"></a>开始结合使用媒体服务 API 与 .NET SDK

若要开始在 .NET 中使用媒体服务 Api，请创建一个**AzureMediaServicesClient**对象。 若要创建对象，需要提供客户端所需凭据以使用 Azure AD 连接到 Azure。 在本文开头克隆的代码中，**GetCredentialsAsync** 函数根据本地配置文件中提供的凭据创建 ServiceClientCredentials 对象。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>创建输出资产  

输出[资产](https://docs.microsoft.com/rest/api/media/assets)会存储作业编码的结果。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]

## <a name="get-or-create-an-encoding-transform"></a>获取或创建编码转换

创建新[转换](https://docs.microsoft.com/rest/api/media/transforms)实例时，需要指定希望生成的输出内容。 所需参数是 TransformOutput 对象，如以下代码所示。 每个 TransformOutput 包含一个预设。 预设介绍了视频和/或音频处理操作的分步说明，这些操作将用于生成所需的 TransformOutput。 本文中的示例使用名为 AdaptiveStreaming 的内置预设。 预设根据输入分辨率和比特率将输入视频编码为自动生成的比特率阶梯（比特率-分辨率对），然后使用与每个比特率解析对相对应的 H-p 视频和 AAC 音频生成 ISO 个文件。

在创建新转换之前，请先使用**Get**方法检查是否已存在一个[转换](https://docs.microsoft.com/rest/api/media/transforms)，如下面的代码所示。 在 Media Services v3**获取**实体上的方法返回**null**如果实体不存在 （不区分大小写的名称检查）。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>提交作业

如上所述，[转换](https://docs.microsoft.com/rest/api/media/transforms)对象为脚本，[作业则](https://docs.microsoft.com/rest/api/media/jobs)是对媒体服务的实际请求，请求将转换应用到给定输入视频或音频内容。 Job 指定输入视频位置和输出位置等信息。

在本教程中，我们将基于直接从[HTTPs 源 URL](job-input-from-http-how-to.md)引入的文件创建作业的输入。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>等待作业完成

该作业需要一些时间才能完成操作。 在该过程中，你应能够接收通知。 以下代码示例显示如何轮询服务以获取[作业](https://docs.microsoft.com/rest/api/media/jobs)状态。 对于生产应用程序，由于可能出现延迟，并不建议将轮询作为最佳做法。 如果在帐户上过度使用轮询，轮询会受到限制。 开发者应改用事件网格。 有关详细信息，请参阅[将事件路由到自定义 Web 终结点](job-state-events-cli-how-to.md)。

作业通常将经历以下状态：“已计划”、“已排队”、“正在处理”、“已完成”（最终状态）。 如果作业出错，则显示“错误”状态。 如果作业正处于取消过程中，则显示“正在取消”，完成时则显示“已取消”。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>创建内容密钥策略

内容密钥提供对资产的安全访问。 你需要创建一个**内容密钥策略**，用于配置如何将内容密钥传递到最终客户端。 内容密钥与**流式处理定位符**相关联。 媒体服务还提供密钥传送服务，将加密密钥传送给已授权的用户。

当播放器请求流时，媒体服务将使用指定的密钥来动态加密你的内容（在这种情况下，使用 AES 加密。）为了解密流，播放器将从密钥传送服务请求密钥。 为了确定是否已授权用户获取密钥，服务将评估你为密钥指定的内容密钥策略。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>创建流定位符

完成编码并设置内容密钥策略后，下一步是使输出资产中的视频可供客户端播放。 可以通过两个步骤来实现视频：

1. 创建[流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators)。
2. 生成客户端可以使用的流式处理 URL。

创建**流式处理定位符**的过程称为发布。 默认情况下，**流式处理定位符**将在你进行 API 调用后立即生效。 除非你配置了可选的开始和结束时间，否则它会一直持续到删除。

创建[流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators)时，需要指定所需的**StreamingPolicyName**。 在本教程中，我们将使用 PredefinedStreamingPolicies 中的一个，它告知 Azure 媒体服务如何发布内容以进行流式处理。 在此示例中，应用 AES 信封加密（此加密也称为 ClearKey 加密，因为密钥通过 HTTPS 传递到播放客户端，而不是 DRM 许可证）。

> [!IMPORTANT]
> 使用自定义的 [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) 时，应为媒体服务帐户设计有限的一组此类策略，并在需要同样的加密选项和协议时重新将这些策略用于流式处理定位符。 媒体服务帐户具有对应于 StreamingPolicy 条目数的配额。 不应为每个流式处理定位符创建新的 StreamingPolicy。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>获取测试令牌

本教程在内容密钥策略中指定使用令牌限制。 令牌限制策略必须附带由安全令牌服务 (STS) 颁发的令牌。 媒体服务支持采用[JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)格式的令牌，这就是我们在示例中配置的内容。

ContentKeyIdentifierClaim 用于**内容密钥策略**，这意味着提供给密钥传送服务的令牌必须具有内容密钥的标识符。 在此示例中，我们在创建流式处理定位符时未指定内容密钥，系统为我们创建了一个随机密钥。 若要生成测试令牌，必须获取 ContentKeyId 以放入 ContentKeyIdentifierClaim 声明。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>生成 DASH 流 URL

现在已经创建了[流式处理定位符](https://docs.microsoft.com/rest/api/media/streaminglocators)，可以获取流式处理 url。 若要生成 URL，需要连接[StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints)主机名和**流式处理定位符**路径。 此示例使用默认的**流式处理终结点**。 首次创建媒体服务帐户时，默认的**流式处理终结点**处于停止状态，因此需要调用 **Start**。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>清理媒体服务帐户中的资源

通常情况下，您应清理您计划重用的对象之外的所有内容（通常情况下，您将重复使用转换、流式处理定位符等）。 如果希望帐户在试验后保持干净状态，则删除不打算重复使用的资源。 例如，以下代码可删除作业：

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组中的任何一个资源（包括为本教程创建的媒体服务和存储帐户），请删除之前创建的资源组。

执行以下 CLI 命令：

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="additional-notes"></a>附加说明

* Widevine 是 Google Inc. 提供的一项服务，受 Google，Inc. 的服务条款和隐私策略的约束。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 DRM 提供保护](protect-with-drm.md)
