---
title: 门户中的服务管理
titleSuffix: Azure Cognitive Search
description: 使用 Azure 门户管理 Azure 认知搜索服务（Microsoft Azure 上托管的云搜索服务）。
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: azure-portal
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/24/2020
ms.openlocfilehash: 84ddc4b427f6dc168c044f34b41e81e3b0ff19e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935035"
---
# <a name="service-administration-for-azure-cognitive-search-in-the-azure-portal"></a>Azure 门户中 Azure 认知搜索服务管理

> [!div class="op_single_selector"]
>
> * [PowerShell](search-manage-powershell.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Portal](search-manage.md)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure 认知搜索是一种完全托管的、基于云的搜索服务，用于在自定义应用中生成丰富的搜索体验。 本文介绍可在 [Azure 门户](https://portal.azure.com)中对已预配的搜索服务执行的服务管理任务。 服务管理设计成轻型，它限于以下任务：

* 使用中间页“使用情况”链接检查存储。
* 使用中间页“监视”链接检查查询量和延迟，以及请求是否受到了限制。
* 使用左边的“密钥”页管理访问。
* 使用左边的“缩放”页调整容量。

在门户中执行的相同任务也可以通过[管理 API](/rest/api/searchmanagement/) 和 [Az.Search PowerShell 模块](search-manage-powershell.md)以编程方式处理。 管理任务完全呈现在门户和程序设计界面中。 任何特定管理任务都不会只能通过一种形式使用。

Azure 认知搜索充分利用其他 Azure 服务进行更深入的监视和管理。 通过搜索服务单独存储的唯一数据是内容（索引、索引器和数据源定义，以及其他对象）。 报告到门户页面的指标按照滚动的 30 天周期从内部日志中拉取。 对于用户控制的日志保留和其他事件，需要 [Azure Monitor](../azure-monitor/index.yml)。 

## <a name="fixed-service-properties"></a>固定的服务属性

搜索服务的一些方面是在预配服务时确定的，确定后不能更改：

* 服务名称（不能重命名服务）
* 服务位置（目前无法将服务原封不动地移动到另一个区域）
* 最大副本计数和分区计数（由“基本”层或标准“层”确定）

如果开始时使用的是最大分区数为 1 的“基本”层，并且现在需要多个分区，则需要在更高层[创建新服务](search-create-service-portal.md)并在新服务上重新创建内容。 

## <a name="administrator-rights"></a>管理员权限

预配或解除对服务本身的授权可以通过 Azure 订阅管理员或协同管理员完成。

关于对终结点的访问，任何具有服务 URL 访问权限和 API 密钥的人员均可访问内容。 若要详细了解密钥，请参阅[管理 API 密钥](search-security-api-keys.md)。

* 服务的只读访问权限是查询权限，通常，向客户端应用程序授予这种权限的方式是向该应用程序提供 URL 和查询 API 密钥。
* 具有读写访问权限就能够添加、删除或修改服务器对象，包括 API 密钥、索引、索引器、数据源和计划。读写访问权限是通过提供 URL 和管理 API 密钥来授予的。

服务预配设备的权限是通过角色分配授予的。 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md) 是基于 [Azure 资源管理器](../azure-resource-manager/management/overview.md)构建的授权系统，用于预配 Azure 资源。 

在 Azure 认知搜索上下文中，[Azure 角色分配](search-security-rbac.md)将确定哪些用户可以执行任务，而不考虑他们是使用[门户](search-manage.md)、[PowerShell](search-manage-powershell.md) 还是[管理 REST API](/rest/api/searchmanagement/search-howto-management-rest-api)：

* 创建或删除服务
* 缩放服务
* 删除或重新生成 API 密钥
* 启用诊断日志记录（创建服务）
* 启用流量分析（创建服务）

> [!TIP]
> 利用 Azure 范围内的机制，可以锁定订阅或资源，以防止具备管理员权限的用户意外或在未经授权的情况下删除搜索服务。 有关详细信息，请参阅[锁定资源以防止意外删除](../azure-resource-manager/management/lock-resources.md)。

## <a name="logging-and-system-information"></a>日志记录和系统信息

在基本层以及更高层上，Microsoft 会监视所有 Azure 认知搜索服务以达到服务级别协议 (SLA) 的 99.9% 可用性。 如果服务的速度较慢或请求吞吐量低于 SLA 阈值，则支持团队审查提供给他们的日志文件并解决问题。

Azure 认知搜索利用 [Azure Monitor](../azure-monitor/index.yml) 来收集和存储索引及查询活动。 搜索服务仅单独存储其内容（索引、索引器定义、数据源定义、技能组定义、同义词映射）。 高速缓存信息和记录的信息是脱离服务而存储的，通常存储在 Azure 存储帐户中。 有关记录索引和查询工作负载的详细信息，请参阅[收集和分析日志数据](search-monitor-logs.md)。

就有关服务的一般信息而言，只需使用 Azure 认知搜索本身内置的设施就可以通过以下方式获取信息：

* 使用服务“概述”页面，通过通知、属性和状态消息。
* 使用 [PowerShell](search-manage-powershell.md) 或[管理 REST API](/rest/api/searchmanagement/) 来[获取服务属性](/rest/api/searchmanagement/services)。 在编程层不提供新信息或操作。 接口是存在的，这样你便可以编写脚本。

## <a name="monitor-resource-usage"></a>监视资源使用情况

在仪表板中，资源监视仅限于服务仪表板中显示的信息，以及一些可通过查询服务获得的度量值。 在服务仪表板的“使用量”部分中，可以快速确定分区资源级别是否适合应用程序。 如果你希望捕获并持久保存所记录的事件，可以预配外部资源，例如 Azure 监视。 有关详细信息，请参阅[监视 Azure 认知搜索](search-monitor-usage.md)。

使用搜索服务 REST API，可以通过编程方式获取文档和索引的计数： 

* [获取索引统计信息](/rest/api/searchservice/Get-Index-Statistics)
* [计数文档](/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>灾难恢复和服务中断

虽然我们可以挽救数据，但 Azure 认知搜索在群集或数据中心级别发生服务中断时不提供服务的即时故障转移。 如果数据中心的群集出现故障，运营团队会检测故障，并努力还原服务。 在服务还原期间将遇到停机，但是可以根据[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 申请服务信用额度来补偿服务不可用的情况。 

如果在超出 Microsoft 控制的灾难性故障中需要连续性服务，可在其他区域[预配一个附加服务](search-create-service-portal.md)并实施异地复制策略，确保索引跨所有服务完全冗余。

使用[索引器](search-indexer-overview.md)来填充和刷新索引的客户可利用相同的数据源，通过特定于地区的索引器来处理灾难恢复。 不同区域的两个服务（每个都运行索引器）可对相同数据源进行索引，实现异地冗余。 如果从同样异地冗余的数据源进行索引，请注意 Azure 认知搜索索引器只能从主要副本执行增量索引（从新的、已修改的或已删除的文档合并更新）。 在故障转移事件中，请确保将索引器重新指向到新的主要副本。 

如果不使用索引器，也可使用应用程序代码将对象和数据并行推送到其他搜索服务。 有关详细信息，请参阅 [Azure 认知搜索中的性能和优化](search-performance-optimization.md)。

## <a name="backup-and-restore"></a>备份和还原

由于 Azure 认知搜索不是主数据存储解决方案，因此，我们不提供正式的自助备份和还原机制。 但是，你可以使用此 [Azure 认知搜索 .NET 示例存储库](https://github.com/Azure-Samples/azure-search-dotnet-samples)中的 **index-backup-restore** 示例代码将索引定义和快照备份到一系列 JSON 文件，然后根据需要使用这些文件来还原索引。 还可以使用此工具在服务层级之间移动索引。

在其他情况下，如果误删索引，用于创建和填充索引的应用程序代码是事实上的还原选项。 要重新生成索引，请删除它（假设其存在），在服务中重新创建该索引，并通过从主数据存储中检索数据来重新加载该索引。

## <a name="scale-up-or-down"></a>增加或减少

每个搜索服务从至少一个副本和一个分区开始操作。 如果你注册了[支持更多容量的层](search-limits-quotas-capacity.md)，请在左侧导航窗格中单击“缩放”以调整资源使用情况。

如果通过任一资源添加容量，服务会自动使用它们。 无需执行任何进一步的操作，但在新资源产生作用之前，会有轻微延迟。 可能需要 15 分钟或更长的时间才能预配其他资源。

### <a name="add-replicas"></a>添加副本

增加每秒查询次数 (QPS) 或实现高可用性可通过添加副本来完成。 每个副本都有索引的副本，因此多添加一个副本将转换为可用于处理服务查询要求的多个索引。 高可用性至少需要 3 个副本（有关详细信息，请参阅[调整容量](search-capacity-planning.md)）。

具有许多副本的搜索服务可通过大量索引进行负载均衡查询请求。 在给定查询量级别的情况下，当有更多索引副本可用于为请求提供服务时，查询吞吐量的速度将更快。 如果出现查询延迟，会期望在附加副本联机后对性能产生积极的影响。

尽管添加副本时查询吞吐量会提高，但不会按在将向服务添加副本时的恰好两倍或三倍来提高。 所有搜索应用程序都会因可能影响到查询性能的外部因素而受到约束。 复杂的查询和网络延迟是造成查询响应次数变化的两个因素。

### <a name="add-partitions"></a>添加分区

添加副本更为常见，但当存储受到限制时，你可以添加分区以获得更多容量。 预配服务的层确定是否可以添加分区。 基本层锁定在一个分区上。 标准层及以上层支持其他数量的分区。

分区数按 12 的因数进行添加（具体而言为 1、2、3、4、6 或 12）。 这是分片的项目。 索引会在 12 个分区中创建，可以全部存储在 1 个分区上，也可以平均分配到 2、3、4、6 或 12 个分区（每个分区一个分片）。

### <a name="remove-replicas"></a>删除副本

在高查询量期间过后，可以使用滑块在搜索查询负载正常后（例如，假日销售结束后）减少副本。 无须再执行其他步骤。 降低副本计数会消除数据中心内的虚拟机。 相较于之前的情况而言，现在会在较少的 VM 上执行查询和数据引入操作。 最低要求是一个副本。

### <a name="remove-partitions"></a>删除分区

与无需执行额外工作即可删除副本相比，如果使用的存储大于可减少的存储，可能需要完成一些工作。 例如，如果解决方案使用三个分区，则在新存储空间小于承载索引所需空间时，缩减为一或两个分区将生成错误。 正如预期的那样，可以选择删除索引或相关索引内的文档来释放空间，或者保持目前配置。

无法通过任何检测方法确定哪些索引分片存储在特定分区上。 每个分区提供大约 25 GB 的存储，因此需要将存储减少到可让所拥有分区数能容纳的大小。 如果要还原为一个分区，则所有 12 个分片都需要适合。

为了帮助实现未来规划，可能需要检查存储（使用[获取索引统计信息](/rest/api/searchservice/Get-Index-Statistics)），了解实际使用了多少空间。 

## <a name="next-steps"></a>后续步骤

* 使用 [PowerShell](search-manage-powershell.md) 进行自动化操作

* 查看[性能和优化](search-performance-optimization.md)技术

* 查看[安全功能](search-security-overview.md)以保护内容和操作

* 启用[诊断日志记录](search-monitor-logs.md)以监视查询和索引工作负载