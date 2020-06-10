---
title: 有关视频索引器的常见问题解答 - Azure
titleSuffix: Azure Media Services
description: 本文解答 Azure 媒体服务视频索引器常见问题。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/12/2020
ms.author: juliako
ms.openlocfilehash: bb57af37308badcd3123af3348178aaac061f376
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83652180"
---
# <a name="video-indexer-frequently-asked-questions"></a>视频索引器常见问题

本文解答了有关视频索引器的常见问题。

## <a name="general-questions"></a>一般问题

### <a name="what-is-video-indexer"></a>什么是视频索引器？

视频索引器是 Microsoft Azure 媒体服务中的一项人工智能服务。 视频索引器能够协调多个机器学习模型，这些模型可让你从视频中提取深入见解。 为了提供高级且准确的见解，视频索引器将使用多个视频通道：音频、语音和视觉对象。 可以通过多种方式来使用视频索引器的见解，例如改进内容可发现性和可访问性、创建新的盈利机会，或构建利用见解的新体验。 视频索引器提供基于 Web 的界面用于测试、配置和自定义帐户中的模型。 开发人员可以使用基于 REST 的 API 将视频索引器集成到生产系统中。 

### <a name="what-can-i-do-with-video-indexer"></a>我可以使用视频索引器进行哪些操作？

视频索引器可以对媒体文件执行的一些操作包括：

* 识别和提取语音，并识别发言人。
* 识别和提取视频中屏幕上的文本。
* 检测视频文件中的对象。
* 从音轨和视频中屏幕上的文本识别品牌（例如：Microsoft）。
* 从名人数据库和用户定义的人脸数据库中检测和识别人脸。
* 提取音频和视频内容中讨论的但不一定提及的主题。
* 基于音轨创建隐藏式字幕或副标题。

有关详细信息和更多的视频索引器功能，请参阅[概述](video-indexer-overview.md)。

### <a name="how-do-i-get-started-with-video-indexer"></a>如何快速入门视频索引器？

视频索引器附带的免费试用版在基于 Web 的界面中提供 600 分钟的试用，通过 API 提供 2,400 分钟的试用。 你可以[登录到视频索引器的基于 Web 的界面](https://www.videoindexer.ai/)，然后使用任何 Web 标识自行试用，而无需设置 Azure 订阅。 请按照[这个简单的入门实验室](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md)操作，以更好地了解如何使用视频索引器。

若要将大量视频和音频文件编入索引，可将视频索引器连接到付费的 Microsoft Azure 订阅。 可在[定价页](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)上找到定价详细信息。

可在[入门](video-indexer-get-started.md)中找到更多详细信息。

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>我是否需要编码技能才能使用视频索引器？

可以使用视频索引器的基于 Web 的界面来评估、配置和管理帐户，而无需编写任何代码。  准备好开发更复杂的应用程序时，可以使用[视频索引器 API](https://api-portal.videoindexer.ai/) 将视频索引器集成到自己的应用程序、网站或[使用 Azure 逻辑应用或 Azure Functions 等无服务器技术的自定义工作流](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/)中。

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>我是否需要机器学习技能才能使用视频索引器？

否。视频索引器允许将多个机器学习模型集成到一个管道中。 通过视频索引器为视频或音频文件编制索引会检索在一条共享时间线上提取的整组见解，客户无需具备任何机器学习技能或了解算法。

### <a name="what-media-formats-does-video-indexer-support"></a>视频索引器支持哪些媒体格式？

视频索引器支持最常见的媒体格式。 有关更多详细信息，请参阅 [Azure 媒体编码器标准格式](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats)列表。

### <a name="how-do-i-upload-a-media-file-into-video-indexer-and-what-are-the-limitations"></a>如何将媒体文件上传到视频索引器中，有什么限制？

在视频索引器的基于 Web 的门户中，可以使用文件上传对话框上传媒体文件，或者指向直接托管源文件的 URL（参阅[示例](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)）。 使用 iFrame 或嵌入代码托管媒体内容的任何 URL 不起作用（参阅[示例](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)）。 

有关详细信息，请参阅[操作方法指南](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos)。

#### <a name="limitations"></a>限制

* 视频的名称不能超过 80 个字符。
* 如果使用字节数组上传视频，视频的大小限制为 2GB（使用 URL 时为 30GB）。 

若要查看完整列表，请参阅[上传注意事项和限制](upload-index-videos.md#uploading-considerations-and-limitations)。

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>视频索引器从媒体中提取见解需要多长时间？

使用视频索引器 API 和视频索引器基于 Web 的界面为视频或音频文件编制索引所需的时间取决于多个参数，例如文件长度和质量、文件中找到的见解数、可用的[预留单位](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview)数以及是否启用了[流式处理终结点](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview)。 我们建议使用自己的内容运行多个测试文件，并根据平均数获取更好的想法。

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>是否可以创建自定义工作流来自动化视频索引器的流程？

是的，可将视频索引器集成到逻辑应用、Flow 和 [Azure Functions](https://azure.microsoft.com/services/functions/) 等无服务器技术。 在[此处](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/)可以找到有关适用于视频索引器的[逻辑应用](https://azure.microsoft.com/services/logic-apps/)和 [Flow](https://flow.microsoft.com/en-us/) 连接器的更多详细信息。 你可以在[视频索引器示例](https://github.com/Azure-Samples/media-services-video-indexer)存储库中查看由合作伙伴完成的一些自动化项目。

### <a name="in-which-azure-regions-is-video-indexer-available"></a>视频索引器在哪些 Azure 区域是可用的？

可在[区域](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)页上查看提供视频索引器的 Azure 区域。

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>我是否可以为自己的特定用例自定义视频索引器模型？ 

是的。 你可以在视频索引器中自定义一些可用模型，以更好地满足自己的需求。 

例如，我们的“人员”模型支持对现成的一百万张人脸进行名人识别，但你可以对它进行训练，使它识别不在该数据库中的其他人脸。 

有关详细信息，请参阅关于自定义[人员](customize-person-model-overview.md)、[品牌](customize-brands-model-overview.md)和[语言](customize-language-model-overview.md)模型的文章。 

###  <a name="can-i-edit-the-videos-in-my-library"></a>我能编辑自己库中的视频吗？

是的。 按库界面上的“编辑视频”按钮或播放机界面上的“在编辑器中打开”按钮，可转到“项目”选项卡  。你可以创建一个新项目，并添加更多自己库中的视频以一起编辑它们，完成后即可呈现并下载视频。 

如果想深入了解你的新视频，使用视频索引器为其编制索引，这样库中就会显示该视频及其见解。

### <a name="can-i-index-multiple-audio-streams-or-channels"></a>能否为多个音频流或通道编制索引？

如果有多个音频流，视频索引器使用它遇到的第一个流并且仅处理此流。 任何音频流视频索引器进程都使用不同的通道（如果有），并将它们一起像单声道那样处理。 对于流/通道操作，你可以先对文件使用 ffmpeg 命令，再对它编制索引。

### <a name="what-is-the-sla-for-video-indexer"></a>什么是适用于视频索引器的 SLA？

Azure 媒体服务的 SLA 涵盖了视频索引器，可在 [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) 页上找到。 该 SLA 仅适用于视频索引器付费帐户，而不适用于免费试用版。

## <a name="privacy-questions"></a>隐私问题

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>你们是否会存储由视频索引器编制索引的音频和视频文件？

是的，除非使用视频索引器网站或 API 从视频索引器中删除该文件，否则将存储视频和音频文件。 对于免费试用版，编制索引的视频和音频文件将存储在美国东部 Azure 区域。 否则，视频和音频文件将存储在 Azure 订阅的存储帐户中。

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>可以删除存储在视频索引器门户中的文件吗？

是的，始终可以删除视频和音频文件，以及视频索引器从中提取的任何元数据和见解。 从视频索引器中删除某个文件后，该文件及其元数据和见解将从视频索引器中永久删除。 但是，如果你在 Azure 存储中实施了自己的备份解决方案，则该文件将保留在 Azure 存储中。

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>是否可以控制用户对我的视频索引器帐户的访问？

是的，只有帐户管理员可以邀请和取消邀请某人加入其帐户，以及分配编辑特权和只读访问权限。

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>谁有权访问我的已由视频索引器编制索引和/或存储的视频和音频文件，以及提取的元数据和见解？

获得该视频或音频内容及其见解的链接的任何人都可以访问将隐私设置设为公开的视频或音频内容。 只有受邀加入视频或音频内容帐户的用户才能访问将隐私设置设为私密的视频或音频内容。 内容的隐私设置也将应用到视频索引器提取的元数据和见解。 可以在上传视频或音频文件时指定隐私设置。 也可以在编制索引后更改隐私设置。

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Microsoft 对我的已由视频索引器编制索引和/或存储的视频或音频文件，以及提取的元数据和见解拥有哪些访问权限？

根据 [Azure 联机服务条款](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST)，你的内容完全由你拥有，Microsoft 只会根据 OST 和 Microsoft 隐私声明访问你的内容，以及视频索引器从你的内容中提取的元数据和见解。

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>我在视频索引器帐户中生成的自定义模型是否可用于其他帐户？

 不可以，在你的帐户中创建的自定义模型不可用于任何其他帐户。 视频索引器目前允许在帐户中生成自定义[品牌](customize-brands-model-overview.md)、[语言](customize-language-model-overview.md)和[人员](customize-person-model-overview.md)模型。 这些模型仅可用于在其中创建这些模型的帐户。
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>由视频索引器编入索引的内容是否保留在我使用视频索引器的 Azure 区域内？

是的，内容及其见解将保留在 Azure 区域中，除非在 Azure 订阅中手动配置使用多个 Azure 区域。 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>视频索引器的隐私策略是什么？

视频索引器在 [Microsoft 隐私声明](https://privacy.microsoft.com/privacystatement)中有所介绍。 隐私声明解释了 Microsoft 处理的个人数据、Microsoft 的处理方式，以及 Microsoft 出于哪些目的处理个人数据。 若要详细了解隐私保护，请访问 [Microsoft 信任中心](https://www.microsoft.com/trustcenter)。

### <a name="what-certifications-does-video-indexer-have"></a>视频索引器通过了哪些认证？

视频索引器目前已通过 SOC 认证。 若要查看视频索引器通过的认证，请参阅 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure)。

### <a name="what-is-the-difference-between-private-and-public-videos"></a>专用视频和公用视频有何区别？ 

将视频上传到视频索引器时，你可以选择两种隐私设置：专用和公用。 公用视频可供任何人访问，包括匿名用户和身份不明的用户。 专用视频则只供帐户成员访问。 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>我尝试将视频作为公用视频上传，它却被标记为不当或有冒犯性的内容，这是什么意思？ 

将视频上传到视频索引器时，系统通过算法和模型自动执行内容分析，目的在于确保不公开呈现任何不当内容。 如果视频确实可能含有不当的内容，则无法将其设置为公用视频。 不过，帐户成员仍然可以将其作为专用视频来访问（查看视频，下载见解和提取的非自然信号，以及执行其他账户成员可执行的操作）。   

为了将视频设置为供公共访问，可以执行以下操作之一： 

* 构建你自己的接口层（例如应用或网站），并使用它与视频索引器服务交互。 这样，视频在我们的门户中依然是专用的，而你的用户可以通过你的界面与之交互。 例如，你仍可以获得见解或允许在你自己的界面中观看视频。 
* 请求对内容进行人工评审，这将导致删除限制（假定内容并未有不当之处）。 

    如果用户直接将视频索引器网站用作接口层，并用于公共（未经身份验证的）查看，可以探索此选项。 

## <a name="api-questions"></a>API 问题

### <a name="what-apis-does-video-indexer-offer"></a>视频索引器提供哪些 API？

使用视频索引器 API 可以编制索引、提取元数据、管理资产、翻译、嵌入、自定义模型，等等。 若要查找有关使用视频索引器 API 的更多详细信息，请参阅[视频索引器开发人员门户](https://api-portal.videoindexer.ai/)。

### <a name="what-client-sdks-does-video-indexer-offer"></a>视频索引器提供哪些客户端 SDK？

目前不提供客户端 SDK。 视频索引器团队正在努力开发 SDK，并计划在不久后推出。

### <a name="how-do-i-get-started-with-video-indexers-api"></a>如何快速入门视频索引器的 API？

请遵循[教程：视频索引器 API 入门](video-indexer-use-apis.md)进行操作。

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>视频索引器 API 与 Azure 媒体服务 v3 API 之间有什么区别？

目前，视频索引器 API 和 Azure 媒体服务 v3 API 提供的功能存在一定的重叠。 在[此处](compare-video-indexer-with-media-services-presets.md)可以找到有关如何比较这两个服务的详细信息。

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>什么是 API 访问令牌，为何需要使用它？

视频索引器 API 包含授权 API 和操作 API。 授权 API 包含提供访问令牌的调用。 对操作 API 的每个调用应该与匹配调用授权范围的访问令牌相关联。

出于安全目的，需要访问令牌来使用视频索引器 API。 这可确保任何调用都来自你或拥有帐户访问权限的用户。 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>帐户访问令牌、用户访问令牌和视频访问令牌之间的区别是什么？

* 帐户级别 – 使用帐户级访问令牌可在帐户级别或视频级别执行操作。 例如，上传视频、列出所有视频、获取视频见解。
* 用户级别 - 使用用户级访问令牌可在用户级别执行操作。 例如，获取关联的帐户。
* 视频级别 – 使用视频级访问令牌可对特定的视频执行操作。 例如，获取视频见解、下载隐藏式字幕、获取小组件，等等。

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>我需要多久获取一次新的访问令牌？ 访问令牌何时过期？

访问令牌每隔一小时会过期，因此需要每隔一小时生成一次新的访问令牌。 

### <a name="what-are-the-login-options-to-video-indexer-developer-portal"></a>视频索引器开发人员门户有哪些登录选项？

你可以使用 Azure AD、Microsoft 帐户、Google 帐户或 Facebook 帐户登录。 

使用标识提供者注册电子邮件帐户后，不能将此电子邮件帐户用于其他标识提供者。

## <a name="billing-questions"></a>计费问题

### <a name="how-much-does-video-indexer-cost"></a>视频索引器如何收费？

视频索引器使用简单的即用即付定价模型，该模型基于要编制索引的内容输入的持续时间。 可能对编码、流式处理、存储、网络使用情况和编码预留单位收取额外费用。 有关详细信息，请参阅[定价](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)页。

### <a name="when-am-i-billed-for-using-video-indexer"></a>使用视频索引器时，何时计费？

发送要编制索引的视频时，用户将定义索引是视频分析、音频分析还是二者皆有。 这会确定将向哪一个 SKU 收费。 如果在处理过程中出现关键级错误，系统会将错误代码作为响应返回。 在这种情况下，不计费。  关键错误可能是由代码中的 bug 或服务拥有的内部依赖项中的严重故障造成的。 系统并不将错误的身份验证或见解提取等错误视为关键错误，对它们返回一个响应。 对于任何返回了有效（非错误代码）响应的情况，都将计费。
 
### <a name="does-video-indexer-offer-a-free-trial"></a>视频索引器是否提供免费试用版？

是的，视频索引器提供免费试用版，该版本具有完整的服务和 API 功能。 配额是，Web 界面用户可以免费为 600 分钟的视频编制索引，对于 API 用户，则为 2,400 分钟。 

## <a name="next-steps"></a>后续步骤

[概述](video-indexer-overview.md)
