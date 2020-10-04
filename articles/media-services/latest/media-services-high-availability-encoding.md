---
title: 媒体服务和视频点播 (VOD) 的高可用性
description: 本文概述了可用于促进 VOD 应用程序的高可用性的 Azure 服务。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: conceptual
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 0b6233552501fbe1578f3abe4e203d725ecddb4b
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707789"
---
# <a name="high-availability-with-media-services-and-video-on-demand-vod"></a>媒体服务和视频点播 (VOD) 的高可用性

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="high-availability-for-vod"></a>VOD 的高可用性

Azure 体系结构文档中有一个名为 [Geodes](/azure/architecture/patterns/geodes) 的高可用性设计模式。 它介绍了如何将重复的资源部署到不同的地理区域，以提供可伸缩性和复原能力。  你可以使用 Azure 服务创建这样的体系结构，以涵盖许多高可用性设计注意事项，例如冗余、运行状况监视、负载均衡以及数据备份与恢复。  下面介绍了一个这样的体系结构，详述了解决方案中使用的每个服务，以及如何使用各个服务为 VOD 应用程序创建高可用性体系结构。

### <a name="sample"></a>示例

有一个示例可供你用来熟悉媒体服务和视频点播 (VOD) 的高可用性。 它还更详细地介绍了如何将服务用于 VOD 方案。  不应将该示例以当前形式用于生产。  在将示例代码集成到生产应用程序之前，请仔细查看示例代码和自述文件，尤其是有关[故障模式](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)的部分。  对于视频点播 (VOD) 的高可用性的生产实现，还应仔细查看其内容交付网络 (CDN) 战略。  查看 [GitHub 上的代码](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)。

## <a name="overview-of-services"></a>服务概述

此示例体系结构中使用的服务包括：

| 图标 | “属性” | 说明 |
| :--: | ---- | ----------- |
|![这是 Media services 帐户图标。](media/media-services-high-availability-encoding/azure-media-services.svg)| 媒体服务帐户 | **描述：**<br>媒体服务帐户是在 Azure 中管理、加密、编码、分析和流式传输媒体内容的起点。 它与某个 Azure 存储帐户资源相关联。 该帐户和所有关联的存储必须位于同一 Azure 订阅中。<br><br>**VOD 使用：**<br>可以使用这些服务对视频和音频资产进行编码和传送操作。  为实现高可用性，你需要设置至少两个媒体服务帐户，每个帐户在不同的区域中。 [详细了解 Azure 媒体服务](media-services-overview.md)。 |
|![这是存储帐户图标。](media/media-services-high-availability-encoding/storage-account.svg)| 存储帐户 | **描述：**<br>Azure 存储帐户包含所有 Azure 存储数据对象：Blob、文件、队列、表和磁盘。 可以通过 HTTP 或 HTTPS 从世界上的任何位置访问数据。<br><br>每个区域中的每个媒体服务帐户在同一区域中都有一个存储帐户。<br><br>**VOD 使用：**<br>可以存储用于 VOD 处理和流式处理的输入和输出数据。 [详细了解 Azure 存储](../../storage/common/storage-introduction.md)。 |
|![这是 Azure 存储队列图标。](media/media-services-high-availability-encoding/storage-account-queue.svg)| Azure 存储队列 | **描述：**<br>Azure 队列存储是一项可存储大量消息的服务，用户可以通过经验证的呼叫，使用 HTTP 或 HTTPS 从世界任何地方访问这些消息。<br><br>**VOD 使用：**<br>队列可用来发送和接收消息，以协调不同模块中的活动。 该示例使用 Azure 存储队列，但 Azure 提供其他类型的队列（例如服务总线和 Service Fabric Reliable Queues），它们可能更适合你的需求。 [详细了解 Azure 队列](../../storage/queues/storage-queues-introduction.md)。 |
|![这是 Azure Cosmos DB 图标。](media/media-services-high-availability-encoding/azure-cosmos-db.svg)| Azure Cosmos DB  | **描述：**<br>Azure Cosmos DB 是 Microsoft 的全球分布式多模型数据库服务，可在全球任意数量的 Azure 区域之间独立缩放吞吐量和存储。<br><br>**VOD 使用：**<br>可以使用表来存储作业输出状态记录并跟踪每个媒体服务实例的运行状况状态。 还可以跟踪/记录对媒体服务 API 的每个调用的状态。 [详细了解 Azure Cosmos DB](../../cosmos-db/introduction.md)。  |
|![这是托管标识图标。](media/media-services-high-availability-encoding/managed-identity.svg)| 托管标识 | **描述：**<br>托管实例是 Azure AD 的一项功能，它在 Azure AD 中提供自动托管的标识。 可以使用此标识向支持 Azure AD 身份验证的任何服务（包括 Key Vault）证明身份，无需在代码中存储凭据。<br><br>**VOD 使用：**<br>Azure Functions 可以使用托管标识向媒体服务实例证明身份，以便连接到 Key Vault。 [详细了解托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。 |
|![这是 Key Vault 图标。](media/media-services-high-availability-encoding/key-vault.svg)| Key Vault | **描述：**<br>Azure Key Vault 可以用来安全地存储令牌、密码、证书、API 密钥和其他机密，并对其访问进行严格控制。 它还可以用作密钥管理解决方案。 可以通过 Azure Key Vault 轻松创建和控制用于加密数据的加密密钥。 它可以轻松预配、管理和部署公用和专用传输层安全性/安全套接字层 (TLS/SSL) 证书，这些证书可以与 Azure 以及内部的已连接资源配合使用。 机密和密钥可以通过软件或 FIPS 140-2 第2级验证的 Hsm 来保护。<br><br>**VOD 使用：**<br>Key Vault 可用于为应用程序的服务主体设置访问策略。  它可用于将连接字符串存储到存储帐户。 我们使用 Key Vault 将连接字符串存储到存储帐户和 Cosmos DB。 你还可以使用 Key Vault 来存储整体群集配置。 对于每个媒体服务实例，你可以存储订阅 ID、资源组名称和帐户名称。 有关更多详细信息，请参阅它在示例中的使用方式。 [详细了解 Key Vault](../../key-vault/general/overview.md)。 |
|![这是 Azure Functions 图标。](media/media-services-high-availability-encoding/function-app.svg)| Azure Functions | **描述：**<br>使用 Azure Functions 运行小段代码（称为“函数”）且不需要担心应用程序基础结构。 [详细了解 Azure Functions](../../azure-functions/functions-overview.md)。<br><br>**VOD 使用：**<br>Azure Functions 可用于存储/承载 VOD 应用程序的模块。  VOD 应用程序的模块可能包括：<br><br>**作业计划模块**<br>作业计划模块可用于将新作业提交到媒体服务群集（不同区域中的两个或更多实例）。 它将跟踪每个媒体服务实例的运行状况状态，并将新作业提交到下一个正常的实例。<br><br>**作业状态模块**<br>作业状态模块将侦听来自 Azure 事件网格服务的作业输出状态事件。 它会将事件存储到事件存储中，以最大程度地减少模块的其余部分调用媒体服务 API 的次数。<br><br>**实例运行状况模块**<br>此模块将跟踪提交的作业并确定每个媒体服务实例的运行状况状态。 它将跟踪已完成的作业、失败的作业和从未完成的作业。<br><br>**预配模块**<br>此模块将预配已处理的资产。 它会将资产数据复制到所有媒体服务实例，并设置 Azure Front Door 服务，以确保即使某些媒体服务实例不可用，也可以对资产进行流式处理。 它还会设置流式处理定位符。<br><br>**作业验证模块**<br>此模块将跟踪每个已提交的作业，重新提交失败的作业，并在作业成功完成后执行作业数据清理。  |
|![这是 "应用服务" 图标。](media/media-services-high-availability-encoding/application-service.svg)| 应用服务（和计划）  | **描述：**<br>Azure 应用服务是一项基于 HTTP 的服务，用于托管 Web 应用程序、REST API 和移动后端  。 它支持 .NET、.NET Core、Java、Ruby、Node.js、PHP 或 Python。 在基于 Windows 和 Linux 的环境中，应用程序都可以运行和缩放。<br><br>**VOD 使用：**<br>每个模块都由一个应用服务承载。 [详细了解应用服务](../../app-service/overview.md)。 |
|![这是 Azure 前门图标。](media/media-services-high-availability-encoding/azure-front-door.svg)| Azure Front Door | **描述：**<br>Azure 前门用于通过优化以实现最佳性能和快速全局故障转移来定义、管理和监视 web 流量的全局路由，实现高可用性。<br><br>**VOD 使用：**<br>可以使用 Azure 前门将流量路由到流式处理终结点。 [详细了解 Azure 前门](../../frontdoor/front-door-overview.md)。  |
|![这是 Azure 事件网格图标。](media/media-services-high-availability-encoding/event-grid-subscription.svg)| Azure 事件网格 | **描述：**<br>为基于事件的体系结构而创建，事件网格包含来自 Azure 服务的对事件的内置支持，如存储 blob 和资源组。 它还支持自定义主题事件。 可以使用筛选器将特定事件路由到不同的终结点，多播到多个终结点，并确保事件可靠传送。 它天生跨每个区域中的多个容错域以及跨可用性区域分布，因而最大限度地提高了可用性。<br><br>**VOD 使用：**<br>可以使用事件网格跟踪所有应用程序事件，并存储它们以持久保存作业状态。 [详细了解 Azure 事件网格](../../event-grid/overview.md)。 |
|![这是 Application Insights 图标。](media/media-services-high-availability-encoding/application-insights.svg)| Application Insights | **描述：** <br>Application Insights 是 Azure Monitor 的一项功能，是面向开发人员和 DevOps 专业人员的一项可扩展的应用程序性能管理 (APM) 服务。 它用来监视实时应用程序。 它检测性能异常，包括了分析工具来诊断问题并了解用户在应用中执行了哪些操作。 Application Insights 有助于持续提高性能与可用性。<br><br>**VOD 使用：**<br>所有日志都可以发送到 Application Insights。 可以通过搜索已成功创建非作业消息来查看是什么实例处理了各个作业。 它可能包含所有已提交的作业元数据，包括唯一标识符和实例名称信息。 [详细了解 Application Insights](../../azure-monitor/app/app-insights-overview.md)。 |
## <a name="architecture"></a>体系结构

此概要关系图显示了为你开始实现高可用性和媒体服务而提供的示例的体系结构。

[ ![点播视频 (VOD) 概要体系结构关系图](media/media-services-high-availability-encoding/high-availability-architecture.svg) ](media/media-services-high-availability-encoding/high-availability-architecture.svg#lightbox)

## <a name="best-practices"></a>最佳做法

### <a name="regions"></a>区域

* [创建](https://review.docs.microsoft.com/azure/media-services/latest/create-account-cli-how-to)两个（或更多）Azure 媒体服务帐户。 这两个帐户必须位于不同的区域。 有关详细信息，请参阅 [部署 Azure 媒体服务服务的区域](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)。
* 将媒体上传到计划在其中提交作业的同一区域。 有关如何开始编码的详细信息，请参阅[从 HTTPS URL 创建作业输入](https://review.docs.microsoft.com/azure/media-services/latest/job-input-from-http-how-to)或[从本地文件创建作业输入](https://review.docs.microsoft.com/azure/media-services/latest/job-input-from-local-file-how-to)。
* 如果随后需要将[作业](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)重新提交到另一个区域，你可以使用 `JobInputHttp` 或 `Copy-Blob` 将数据从源资产容器复制到备用区域中的资产容器。

### <a name="monitoring"></a>监视

* 通过 Azure 事件网格订阅每个帐户中的 `JobStateChange` 消息。
    * 通过 Azure 门户或 CLI [注册事件](https://review.docs.microsoft.com/azure/media-services/latest/reacting-to-media-services-events)（也可以使用事件网格管理 SDK 进行注册）
    * 使用 [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)（原生支持媒体服务事件）。
    * 你还可以通过 Azure Functions 使用事件网格事件。

    更多相关信息：

    * 请参阅[音频分析示例](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)，该示例展示了如何使用 Azure 事件网格监视作业，包括添加回退以防止 Azure 事件网格消息因某种原因而延迟。
    * 查看[媒体服务事件的 Azure 事件网格架构](https://review.docs.microsoft.com/azure/media-services/latest/media-services-event-schemas)。

* 当你创建[作业](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)时：
    * 从当前使用的帐户的列表中随机选择一个帐户（此列表通常包含两个帐户，但如果检测到问题，它可能只包含一个帐户）。 如果列表为空，则引发警报，以便操作员能够进行调查。
    * 创建记录以跟踪每个正在进行的作业以及所使用的区域/帐户。
* 当 `JobStateChange` 处理程序收到作业已达到计划状态的通知时，请记录其进入计划状态的时间以及所使用的区域/帐户。
* 当 `JobStateChange` 处理程序收到作业已达到处理状态的通知时，请将该作业的记录标记为正在处理，并记录它进入处理状态的时间。
* 当 `JobStateChange` 处理程序获得作业已达到最终状态（完成/出错/已取消）的通知时，请相应地标记作业的记录。
* 设置定期查看作业记录的单独进程
    * 如果给定区域中处于计划状态的作业在合理的时间内没有进入正在处理状态，请从当前所用的帐户列表中删除该区域。 取决于你的业务需求，你可以决定立即取消这些作业，然后将它们重新提交给其他区域。 你也可以耐心等待这些作业转到下一个状态。
    * 如果某个区域已从帐户列表中删除，请先监视该区域的恢复状况，然后再将其添加回列表中。 可以通过区域上的现有作业来监视区域运行状况（如果未取消并重新提交这些作业），方法是在一段时间后将帐户重新添加到列表中，并通过操作员监视有关可能影响 Azure 媒体服务的中断的 Azure 通信。

## <a name="next-steps"></a>后续步骤

* 查看[代码示例](/samples/browse/?products=azure-media-services)
