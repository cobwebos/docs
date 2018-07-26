---
title: 使用 AES Azure 媒体服务动态加密 | Microsoft Docs
description: 借助 Microsoft Azure 媒体服务，可以传送使用 AES 128 位加密密钥加密的内容。 媒体服务还提供密钥传送服务，将加密密钥传送给已授权的用户。 本主题说明如何使用 AES-128 动态加密以及如何使用密钥传送服务。
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: juliako
ms.openlocfilehash: 3e5de521570a587b049702dabd3e3692c4227796
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114787"
---
# <a name="use-aes-128-dynamic-encryption-and-the-key-delivery-service"></a>使用 AES-128 动态加密和密钥传递服务

借助媒体服务，可以传送使用 AES 通过 128 位加密密钥加密的 HTTP Live Streaming (HLS)、MPEG-DASH 和平滑流。 媒体服务还提供密钥传送服务，将加密密钥传送给已授权的用户。 如果希望媒体服务加密某个资产，需要将加密密钥与 StreamingLocator 相关联，并配置内容密钥策略。 当播放器请求流时，媒体服务将使用指定的密钥通过 AES 加密来动态加密内容。 为解密流，播放器从密钥传送服务请求密钥。 为了确定是否已授权用户获取密钥，服务将评估你为密钥指定的内容密钥策略。

本文基于 [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) 示例。 该示例演示如何创建一个编码转换，该转换使用内置的预设来进行自适应比特率编码，并直接从 [HTTPs 源 URL](job-input-from-http-how-to.md) 引入文件。 然后，使用 AES (ClearKey) 加密发布输出资产。 该示例的输出是 Azure Media Player 的 URL，其中包括播放内容所需的 DASH 清单和 AES 令牌。 该示例将 JWT 令牌的过期时间设置为 1 小时。 可以打开浏览器并粘贴生成的 URL 来启动 Azure Media Player 演示页，其中已经填充了该 URL 和令牌（采用以下格式：```https://ampdemo.azureedge.net/?url= {dash Manifest URL} &aes=true&aestoken=Bearer%3D{ JWT Token here}```）。

> [!NOTE]
> 可以使用多个加密类型（AES-128、PlayReady、Widevine、FairPlay）来加密每个资产。 请参阅[流式处理协议和加密类型](content-protection-overview.md#streaming-protocols-and-encryption-types)，以了解有效的组合方式。

## <a name="prerequisites"></a>先决条件

以下是完成本教程所需具备的条件。

* 查看[内容保护概述](content-protection-overview.md)一文。
* 安装 Visual Studio Code 或 Visual Studio
* 按照[本快速入门](create-account-cli-quickstart.md)所述，创建新的 Azure 媒体服务帐户。
* 根据[访问 API](access-api-cli-how-to.md) 中所述，获取使用媒体服务 API 时所需的凭据

## <a name="download-code"></a>下载代码

使用以下命令，将包含本文中所述完整 .NET 示例的 GitHub 存储库克隆到计算机：

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
“使用 AES-128 加密”示例位于 [EncryptWithAES](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES) 文件夹中。

> [!NOTE]
> 每次运行应用程序时，该示例就将创建唯一的资源。 通常，你会重复使用现有的资源，例如转换和策略（如果现有资源具有所需的配置）。 

## <a name="start-using-media-services-apis-with-net-sdk"></a>开始结合使用媒体服务 API 与 .NET SDK

若要开始将媒体服务 API 与 .NET 结合使用，需要创建 AzureMediaServicesClient 对象。 若要创建对象，需要提供客户端所需凭据以使用 Azure AD 连接到 Azure。 在本文开头克隆的代码中，**GetCredentialsAsync** 函数根据本地配置文件中提供的凭据创建 ServiceClientCredentials 对象。 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>创建输出资产  

输出[资产](https://docs.microsoft.com/rest/api/media/assets)会存储作业编码的结果。  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>获取或创建编码转换

创建新[转换](https://docs.microsoft.com/rest/api/media/transforms)实例时，需要指定希望生成的输出内容。 所需参数是 TransformOutput 对象，如以下代码所示。 每个 TransformOutput 包含一个预设。 预设介绍了视频和/或音频处理操作的分步说明，这些操作将用于生成所需的 TransformOutput。 本文中的示例使用名为 AdaptiveStreaming 的内置预设。 此预设将输入的视频编码为基于输入的分辨率和比特率自动生成的比特率阶梯（比特率 - 分辨率对），并通过与每个比特率 - 分辨率对相对应的 H.264 视频和 AAC 音频生成 ISO MP4 文件。 

在创建新的[转换](https://docs.microsoft.com/rest/api/media/transforms)之前，应该先检查是否已存在使用 **Get** 方法的转换，如以下代码中所示。  在 Media Services v3**获取**实体上的方法返回**null**如果实体不存在 （不区分大小写的名称检查）。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>提交作业

如上所述，[转换](https://docs.microsoft.com/rest/api/media/transforms)对象为脚本，[作业则是](https://docs.microsoft.com/rest/api/media/jobs)对媒体服务的实际请求，请求将转换应用到给定输入视频或音频内容。 作业指定输入视频位置和输出位置等信息。

本教程基于直接从 [HTTPs 源 URL](job-input-from-http-how-to.md) 引入的文件创建作业的输入。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>等待作业完成

此作业需要一些时间才能完成，完成时可发出通知。 以下代码示例显示如何轮询服务以获取[作业](https://docs.microsoft.com/rest/api/media/jobs)状态。 对于生产应用程序，由于可能出现延迟，并不建议将轮询作为最佳做法。 如果在帐户上过度使用轮询，轮询会受到限制。 开发者应改用事件网格。 请参阅[将事件路由到自定义 Web 终结点](job-state-events-cli-how-to.md)。

作业通常将经历以下状态：“已计划”、“已排队”、“正在处理”、“已完成”（最终状态）。 如果作业出错，则显示“错误”状态。 如果作业正处于取消过程中，则显示“正在取消”，完成时则显示“已取消”。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#WaitForJobToFinish)]

## <a name="create-a-contentkeypolicy"></a>创建 ContentKeyPolicy

内容密钥提供对资产的安全访问。 需要创建一个 **ContentKeyPolicy**，用于配置如何将内容密钥传送到终端客户端。 内容密钥与 **StreamingLocator** 相关联。 媒体服务还提供密钥传送服务，将加密密钥传送给已授权的用户。 

当播放器请求流时，媒体服务将使用指定的密钥通过 AES 加密来动态加密内容（本例中使用 AES 加密）。为解密流，播放器从密钥传送服务请求密钥。 为了确定是否已授权用户获取密钥，服务将评估你为密钥指定的内容密钥策略。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaminglocator"></a>创建 StreamingLocator

完成编码并设置内容密钥策略后，下一步是使输出资产中的视频可供客户端播放。 通过两个步骤实现此目的： 

1. 创建 [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators)
2. 生成客户端可以使用的流式处理 URL。 

创建 **StreamingLocator** 的过程称为发布。 默认情况下，除非配置可选的开始和结束时间，否则调用 API 后，StreamingLocator 立即生效，并持续到其被删除为止。 

创建 [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) 时，需要指定所需的 StreamingPolicyName。 本教程使用某个 PredefinedStreamingPolicies 来告知 Azure 媒体服务如何发布流式处理的内容。 此示例中应用了 AES 信封加密（也称为 ClearKey 加密，因为密钥是通过 HTTPS 而不是 DRM 许可证传送到播放客户端的）。

> [!IMPORTANT]
> 使用自定义的 [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) 时，应为媒体服务帐户设计有限的一组此类策略，并在需要同样的加密选项和协议时重新将这些策略用于 StreamingLocators。 媒体服务帐户具有对应于 StreamingPolicy 条目数的配额。 不应为每个 StreamingLocator 创建新的 StreamingPolicy。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>获取测试令牌
        
本教程在内容密钥策略中指定使用令牌限制。 令牌限制策略必须附带由安全令牌服务 (STS) 颁发的令牌。 媒体服务支持采用 [JSON Web 令牌](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) 格式的令牌，我们在示例中配置了此格式。

ContentKeyPolicy 中使用了 ContentKeyIdentifierClaim，这意味着，提供给密钥传送服务的令牌必须包含 ContentKey 的标识符。 本示例未指定内容密钥，在创建 StreamingLocator 时，系统会创建一个随机内容密钥。 若要生成测试令牌，必须获取要放入 ContentKeyIdentifierClaim 声明中的 ContentKeyId。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetToken)]

## <a name="build-a-dash-streaming-url"></a>生成 DASH 流 URL

创建 [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) 后，可以获取流 URL。 若要生成 URL，需要连接 [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) 主机名称和 **StreamingLocator** 路径。 在此示例中，使用默认的 StreamingEndpoint。 首次创建媒体服务帐户时，默认 StreamingEndpoint 处于停止状态，因此需要调用 Start。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#GetMPEGStreamingUrl)]

## <a name="clean-up-resources-in-your-media-services-account"></a>清理媒体服务帐户中的资源

通常情况下，除了打算重复使用的对象，用户应清理所有内容（通常将重复使用转换并保留 StreamingLocators 等）。 如果希望帐户在试验后保持干净状态，则应删除不打算重复使用的资源。  例如，以下代码可删除作业。

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithAES/Program.cs#CleanUp)]

## <a name="next-steps"></a>后续步骤

了解如何[使用 DRM 提供保护](protect-with-drm.md)
