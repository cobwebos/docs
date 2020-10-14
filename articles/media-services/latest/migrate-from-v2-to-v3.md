---
title: 从 Azure 媒体服务 v2 迁移到 v3 | Microsoft Docs
description: 本文介绍了 Azure 媒体服务 v3 中引入的更改，并说明了两个版本之间的差异。 本文还提供了从媒体服务 v2 迁移到 v3 的迁移指南。
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure 媒体服务, 流, 广播, 实时, 脱机
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 48ce6edc3d071d84c3921f85c2e9798b804d0279
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017737"
---
# <a name="migration-guidance-for-moving-from-media-services-v2-to-v3"></a>有关从媒体服务 v2 迁移到 v3 的指导

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>通过将此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Migrate+from+Azure+Media+Services+v2+to+v3%22&locale=en-us`) 复制并粘贴到 RSS 源阅读器中获取有关何时重新访问此页以获得更新的通知。

本文提供了从媒体服务 v2 迁移到 v3 的迁移指南。

如果你目前基于[旧版媒体服务 v2 API](../previous/media-services-overview.md) 开发了一个视频服务，则在迁移到 v3 API 之前，应查看以下指导原则和注意事项。 v3 API 中的许多优势和新功能可以改进开发体验和媒体服务的功能。 但是，如本文的[已知问题](#known-issues)部分中所述，API 版本的变化也带来了一些限制。 在媒体服务团队不断改进 v3 API 并解决版本差距的过程中，本页面会得到维护。 

## <a name="prerequisites"></a>先决条件

* 查看[媒体服务 v2 与 v3](media-services-v2-vs-v3.md)
* [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="benefits-of-media-services-v3"></a>媒体服务 v3 的优势
  
### <a name="api-is-more-approachable"></a>API 更便于访问

*  v3 基于一个统一的 API 接口，该接口公开了基于 Azure 资源管理器构建的管理和操作功能。 Azure 资源管理器模板可用于创建和部署转换、流式处理终结点、直播活动等等。
* [OpenAPI 规范（以前称为 Swagger）](https://aka.ms/ams-v3-rest-sdk)文档。
    公开所有服务组件的架构，包括基于文件的编码。
* 适用于 [.NET](/dotnet/api/overview/azure/mediaservices/management)、.NET Core、[Node.js](/javascript/api/overview/azure/mediaservices/management)、[Python](/python/api/overview/azure/mediaservices/management)、[Java](/java/api/overview/azure/mediaservices/management)、[Go](https://aka.ms/ams-v3-go-ref) 和 Ruby 的 SDK。
* 简化脚本支持的 [Azure CLI](/cli/azure/ams) 集成。

### <a name="new-features"></a>新增功能

* 对于基于文件的作业处理，可以使用 HTTP(S) URL 作为输入。<br/>无需事先在 Azure 中存储内容，无需创建资产。
* 为基于文件的作业处理引入了[转换](transforms-jobs-concept.md)的概念。 使用转换可以生成可重用的配置、创建 Azure 资源管理器模板，并隔离多个客户或租户之间的处理设置。
* 一个资产可以有多个[流式处理定位符](streaming-locators-concept.md)，其中每个流式处理定位符有不同的[动态打包](dynamic-packaging-overview.md)和动态加密设置。
* [内容保护](content-key-policy-concept.md)支持多密钥功能。
* 使用媒体服务将单比特率贡献源转码为具有多比特率的输出流时，可以流式传输最长 24 小时的直播活动。
* 直播活动支持新的低延迟实时传送视频流。 有关详细信息，请参阅[延迟](live-event-latency.md)。
* 直播活动预览版支持[动态打包](dynamic-packaging-overview.md)和动态加密。 这样，用户便可以使用预览版中的内容保护以及 DASH 和 HLS 打包。
* 与 v2 API 中的节目实体相比，实时输出更易于使用。 
* 改进的 RTMP 支持（提高了稳定性并提供了更多的源编码器支持）。
* RTMPS 安全引入。<br/>创建直播活动时，将获得 4 个引入 URL。 这 4 个引入 URL 几乎是相同的，具有相同的流式处理令牌 (AppId)，仅端口号部分不同。 其中两个 URL 是 RTMPS 的主要和备份 URL。   
* 可对实体使用基于角色的访问控制 (RBAC)。 

## <a name="known-issues"></a>已知问题

*  目前，可以使用 [Azure 门户](https://portal.azure.com/)执行以下操作：

    * 管理媒体服务 v3 [直播活动](live-events-outputs-concept.md)、 
    * 查看（而不是管理）v3 [资产](assets-concept.md)、 
    * [获取有关访问 API 的信息](./access-api-howto.md)。 

    对于其他所有管理任务（例如，[转换和作业](transforms-jobs-concept.md)和[内容保护](content-protection-overview.md)），请使用 [REST API](/rest/api/media/)、[CLI](/cli/azure/ams) 或某个受支持的 [SDK](media-services-apis-overview.md#sdks)。
* 需要在帐户中预配媒体预留单位 (MRU) 才能控制作业的并发和性能，特别是涉及到视频或音频分析的那些作业。 有关详细信息，请参阅[缩放媒体处理](../previous/media-services-scale-media-processing-overview.md)。 可以使用[适用于媒体服务 v3 的 CLI 2.0](media-reserved-units-cli-how-to.md)、使用 [Azure 门户](../previous/media-services-portal-scale-media-processing.md)或 [v2 API](../previous/media-services-dotnet-encoding-units.md) 来管理 MRU。 无论使用的是媒体服务 v2 还是 v3 API，都需要预配 MRU。
* 无法使用 v2 API 来管理通过 v3 API 创建的媒体服务实体。  
* 并非 V2 API 中的所有实体都自动出现在 V3 API 中。  以下是这两个版本中不兼容的实体的示例：  
    * 在 v2 中创建作业和任务不会在 v3 中显示，因为它们不与转换相关联。 建议切换到 v3 转换和作业。 在切换过程中，需要花费一段相对较短的时间来监视即时 v2 作业。
    * 无法持续使用 v3 来管理通过 v2 创建的频道和节目（映射到 v3 中的直播活动和实时输出）。 建议在方便停止频道时切换到 v3 直播活动和实时输出。<br/>目前，无法持续迁移正在运行的频道。  

> [!NOTE]
> 在媒体服务团队不断改进 v3 API 并解决版本差距的过程中，本页面会得到维护。

## <a name="ask-questions-give-feedback-get-updates"></a>提出问题、提供反馈、获取更新

查看 [Azure 媒体服务社区](media-services-community.md)文章，了解可以提出问题、提供反馈和获取有关媒体服务的更新的不同方法。

## <a name="next-steps"></a>后续步骤

[教程：基于 URL 对远程文件进行编码并流式传输视频 - .NET](stream-files-dotnet-quickstart.md)