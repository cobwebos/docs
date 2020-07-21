---
title: 通过媒体服务和视频点播（VOD）实现高可用性
description: 本文概述了可用于为 VOD 应用程序提供高可用性的 Azure 服务。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/15/2020
ms.author: inhenkel
ms.openlocfilehash: 9be5aa48b140ee9eb43141d6699109ef12ebf949
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519938"
---
# <a name="high-availability-with-media-services-and-video-on-demand-vod"></a>通过媒体服务和视频点播（VOD）实现高可用性

## <a name="high-availability-for-vod"></a>VOD 的高可用性

Azure 体系结构文档中有一个名为[Geodes](https://docs.microsoft.com/azure/architecture/patterns/geodes)的高可用性设计模式。 其中介绍了如何将重复的资源部署到不同的地理区域，以提供可伸缩性和复原能力。  可以使用 Azure 服务创建此类体系结构，以涵盖许多高可用性设计注意事项，如冗余、运行状况监视、负载平衡和数据备份与恢复。  下面对此类体系结构进行了介绍，其中包含解决方案中使用的每个服务的详细信息，以及如何使用各个服务来为 VOD 应用程序创建高可用性体系结构。

### <a name="sample"></a>示例

有一个示例可供你用来熟悉 Media Services 和视频点播（VOD）的高可用性。 它还详细介绍了如何将服务用于 VOD 方案。  该示例不应在生产中用于当前窗体。  仔细查看示例代码和自述文件，尤其是在将其集成到生产应用程序之前，有关[失败模式](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)的部分。  视频点播（VOD）的高可用性的生产实现还应仔细查看其内容交付网络（CDN）策略。  查看[GitHub 上的代码](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)。

## <a name="overview-of-services"></a>服务概述

此示例体系结构中使用的服务包括：

| 图标 | 名称 | 说明 |
| :--: | ---- | ----------- |
|![image](media/media-services-high-availability-encoding/azure-media-services.svg)| 媒体服务帐户 | **说明：**<br>媒体服务帐户是在 Azure 中管理、加密、编码、分析和流式传输媒体内容的起点。 它与 Azure 存储帐户资源关联。 帐户和所有关联的存储必须位于同一 Azure 订阅中。<br><br>**VOD 使用：**<br>您可以使用这些服务对您的视频和音频资产进行编码和传送。  为实现高可用性，需要设置至少两个媒体服务帐户，每个帐户在不同的区域中。 [了解有关 Azure 媒体服务的详细信息](media-services-overview.md)。 |
|![image](media/media-services-high-availability-encoding/storage-account.svg)| 存储帐户 | **说明：**<br>Azure 存储帐户包含所有 Azure 存储数据对象： blob、文件、队列、表和磁盘。 数据可通过 HTTP 或 HTTPS 从世界上的任何位置进行访问。<br><br>每个区域中的每个媒体服务帐户在同一区域中都有一个存储帐户。<br><br>**VOD 使用：**<br>可以存储用于 VOD 处理和流式处理的输入和输出数据。 [了解有关 Azure 存储的详细信息](https://docs.microsoft.com/azure/storage/common/storage-introduction)。 |
|![image](media/media-services-high-availability-encoding/storage-account-queue.svg)| Azure 存储队列 | **说明：**<br>Azure 队列存储是一项可存储大量消息的服务，用户可以通过经验证的呼叫，使用 HTTP 或 HTTPS 从世界任何地方访问这些消息。<br><br>**VOD 使用：**<br>队列可用于发送和接收消息，以协调不同模块中的活动。 该示例使用 Azure 存储队列，但 Azure 提供其他类型的队列（如服务总线），并 Service Fabric 可靠队列，这可以更好地满足你的需求。 [了解有关 Azure 队列的详细信息](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction)。 |
|![image](media/media-services-high-availability-encoding/azure-cosmos-db.svg)| Azure Cosmos DB  | **说明：**<br>Azure Cosmos DB 是 Microsoft 的全球分布式多模型数据库服务，可在全球任意数量的 Azure 区域之间独立缩放吞吐量和存储。<br><br>**VOD 使用：**<br>表可用于存储作业输出状态记录以及跟踪每个媒体服务实例的运行状况状态。 还可以跟踪/记录每次调用媒体服务 API 时的状态。 [详细了解 Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)。  |
|![image](media/media-services-high-availability-encoding/managed-identity.svg)| 托管标识 | **说明：**<br>托管标识是 Azure AD 的一项功能，它在 Azure AD 中提供自动管理的标识。 它可用于向支持 Azure AD 身份验证的任何服务进行身份验证，包括 Key Vault，而无需在代码中存储凭据。<br><br>**VOD 使用：**<br>Azure Functions 可以使用托管标识向 Media Services 实例进行身份验证，以便连接到 Key Vault。 [了解有关托管标识的详细信息](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。 |
|![image](media/media-services-high-availability-encoding/key-vault.svg)| Key Vault | **说明：**<br>Azure Key Vault 可用于安全地存储和严格控制对令牌、密码、证书、API 密钥和其他机密的访问。 它还可用作密钥管理解决方案。 可以通过 Azure Key Vault 轻松创建和控制用于加密数据的加密密钥。 它可以轻松设置、管理和部署公共和专用传输层安全性/安全套接字层（TLS/SSL）证书，以便与 Azure 和内部连接的资源配合使用。 机密和密钥可以通过软件或 FIPS 140-2 第2级验证的 Hsm 来保护。<br><br>**VOD 使用：**<br>Key Vault 可用于为应用程序的服务主体设置访问策略。  它可用于将连接字符串存储到存储帐户。 我们使用 Key Vault 将连接字符串存储到存储帐户和 cosmos db。 你还可以使用 Key Vault 来存储总体群集配置。 对于每个媒体服务实例，可以存储订阅 ID、资源组名称和帐户名称。 有关更多详细信息，请参阅示例中的使用方式。 [详细了解 Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview)。 |
|![image](media/media-services-high-availability-encoding/function-app.svg)| Azure Functions | **说明：**<br>运行小段代码（称为 "函数"），无需担心 Azure Functions 的应用程序基础结构。 [详细了解 Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview)。<br><br>**VOD 使用：**<br>Azure Functions 可用于存储 VOD 应用程序的模块。  VOD 应用程序的模块包括：<br><br>**作业计划模块**<br>作业计划模块可用于将新作业提交到媒体服务群集（不同区域中的两个或更多实例）。 它将跟踪每个媒体服务实例的运行状况状态，并将新作业提交到下一个正常运行的实例。<br><br>**作业状态模块**<br>作业状态模块正在侦听来自 Azure 事件网格服务的作业输出状态事件。 它会将事件存储到事件存储中，以最大程度地减少模块的其余部分调用媒体服务 Api。<br><br>**实例运行状况模块**<br>此模块将跟踪提交的作业并确定每个媒体服务实例的运行状况状态。 它将跟踪已完成的作业、失败的作业和从未完成的作业。<br><br>**预配模块**<br>此模块将设置已处理的资产。 它会将资产数据复制到所有 Media Services 实例，并设置 Azure 前门服务，以确保即使某些媒体服务实例不可用，也可以对资产进行流式处理。 它还会设置流式处理定位符。<br><br>**作业验证模块**<br>此模块将跟踪每个已提交作业，重新提交失败的作业，并在作业成功完成后执行作业数据清理。  |
|![image](media/media-services-high-availability-encoding/application-service.svg)| 应用服务（和计划）  | **说明：**<br>Azure 应用服务是一项基于 HTTP 的服务，用于托管 Web 应用程序、REST API 和移动后端  。 它支持 .NET、.NET Core、Java、Ruby、Node.js、PHP 或 Python。 应用程序在基于 Windows 和 Linux 的环境上运行和缩放。<br><br>**VOD 使用：**<br>每个模块都由应用服务托管。 [了解有关应用服务的详细信息](https://docs.microsoft.com/azure/app-service/overview)。 |
|![image](media/media-services-high-availability-encoding/azure-front-door.svg)| Azure Front Door | **说明：**<br>Azure 前门用于通过优化以实现最佳性能和快速全局故障转移来定义、管理和监视 web 流量的全局路由，实现高可用性。<br><br>**VOD 使用：**<br>可以使用 Azure 前门将流量路由到流式处理终结点。 [详细了解 Azure 前门](https://docs.microsoft.com/azure/frontdoor/front-door-overview)。  |
|![image](media/media-services-high-availability-encoding/event-grid-subscription.svg)| Azure 事件网格 | **说明：**<br>事件网格为基于事件的体系结构而创建，内置了对来自 Azure 服务（如存储 blob 和资源组）的事件的支持。 它还支持自定义主题事件。 筛选器可用于将特定事件路由到不同的终结点，多播到多个终结点，并确保可靠地传递事件。 它通过本机方式在每个区域中的多个容错域之间分布，并跨可用性区域实现可用性的最大化。<br><br>**VOD 使用：**<br>可以使用事件网格跟踪所有应用程序事件，并将其存储为持久保存作业状态。 [详细了解 Azure 事件网格](https://docs.microsoft.com/azure/event-grid/overview)。 |
|![image](media/media-services-high-availability-encoding/application-insights.svg)| Application Insights | **说明：** <br>Application Insights 是 Azure Monitor 的一项功能，是面向开发人员和 DevOps 专业人员的可扩展应用程序性能管理 (APM) 服务。 它用于监视实时应用程序。 它检测到性能异常，并包括分析工具来诊断问题并了解用户对应用执行的操作。 Application Insights 有助于持续提高性能与可用性。<br><br>**VOD 使用：**<br>所有日志都可以发送到 Application Insights。 可以通过搜索成功创建的作业消息来查看处理每个作业的实例。 它可能包含所有提交的作业元数据，包括唯一标识符和实例名称信息。 [详细了解 Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)。 |
## <a name="architecture"></a>体系结构

此高级关系图显示了为你开始实现高可用性和媒体服务而提供的示例的体系结构。

[视频点播 ![ （VOD）高级体系结构示意图 ](media/media-services-high-availability-encoding/high-availability-architecture.svg)](media/media-services-high-availability-encoding/high-availability-architecture.svg#lightbox)

## <a name="best-practices"></a>最佳实践

### <a name="regions"></a>区域

* [创建](https://review.docs.microsoft.com/azure/media-services/latest/create-account-cli-how-to)两个（或多个） Azure 媒体服务帐户。 这两个帐户需要在不同的区域中。 有关详细信息，请参阅[部署 Azure 媒体服务服务的区域](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。
* 将你的媒体上传到计划从中提交作业的同一区域。 有关如何开始编码的详细信息，请参阅[从 HTTPS URL 创建作业输入](https://review.docs.microsoft.com/azure/media-services/latest/job-input-from-http-how-to)或[从本地文件创建作业输入](https://review.docs.microsoft.com/azure/media-services/latest/job-input-from-local-file-how-to)。
* 如果随后需要将[作业](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)重新提交到另一个区域，则可以使用 `JobInputHttp` 或使用将 `Copy-Blob` 源资产容器中的数据复制到备用区域中的资产容器。

### <a name="monitoring"></a>监视

* `JobStateChange`通过 Azure 事件网格订阅每个帐户中的消息。
    * 通过 Azure 门户或 CLI[注册事件](https://review.docs.microsoft.com/azure/media-services/latest/reacting-to-media-services-events)（也可以通过 EVENTGRID 管理 SDK 来完成）
    * 使用[EVENTGRID SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) （本机支持媒体服务事件）。
    * 还可以通过 Azure Functions 使用事件网格事件。

    更多相关信息：

    * 请参阅[音频分析示例](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)，其中展示了如何使用 Azure 事件网格监视作业，包括添加回退以防 Azure 事件网格消息由于某种原因而延迟。
    * 查看[Media Services 事件的 Azure 事件网格架构](https://review.docs.microsoft.com/azure/media-services/latest/media-services-event-schemas)。

* 创建[作业](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)时：
    * 从当前使用的帐户列表中随机选择一个帐户（此列表通常包含两个帐户，但如果检测到问题，则它可能只包含一个帐户）。 如果列表为空，则引发警报，以便操作员能够调查。
    * 创建记录以跟踪每个即时作业以及所使用的区域/帐户。
* 当 `JobStateChange` 处理程序收到作业已达到计划状态的通知时，记录其输入计划状态的时间以及所使用的区域/帐户。
* 当 `JobStateChange` 处理程序收到作业已达到处理状态的通知时，将该作业的记录标记为 "正在处理"，并记录它进入处理状态的时间。
* 当 `JobStateChange` 处理程序收到作业已达到最终状态（完成/误码/已取消）的通知时，请相应地标记作业的记录。
* 有一个单独的进程，该进程定期查看作业记录
    * 如果作业处于计划状态，并且在给定区域的合理时间内未提前处理状态，请从当前使用的帐户列表中删除该区域。 根据你的业务要求，你可以立即取消这些作业并将其重新提交到另一个区域。 或者，您可以将它们更多时间移到下一个状态。
    * 如果已从帐户列表中删除某个区域，请在将其重新添加到列表之前监视该区域的恢复。 可以通过区域中的现有作业来监视区域运行状况（如果尚未取消并重新提交），方法是在一段时间后将该帐户添加回列表，并通过操作员监视 Azure 通信（有关可能会影响 Azure 媒体服务的中断）。

## <a name="next-steps"></a>后续步骤

* [构建点播视频跨区域流式处理](media-services-high-availability-streaming.md)
* 查看[代码示例](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
