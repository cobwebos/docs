---
title: Azure 门户中 Azure 搜索服务管理
description: 使用 Azure 门户管理 Azure 搜索，这是 Microsoft Azure 上托管的云搜索服务。
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 896a12db1ac196b6de1e57dde9b5910e11dcc8c7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Azure 门户中 Azure 搜索服务管理
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Azure 搜索是一种完全托管的、基于云的搜索服务，用于在自定义应用中生成丰富的搜索体验。 本文介绍可在 [Azure 门户](https://portal.azure.com)中对已预配的搜索服务执行的*服务管理*任务。 *服务管理*设计成轻型，它限于以下任务：

* 管理和保护对用于服务读取或写入的 *api-keys* 的访问。
* 通过更改分区和副本的分配以调整服务容量。
* 根据服务层的最大限制，监视资源使用情况。

请注意，“升级”未列为管理任务。 因为预配服务时会分配资源，所以移动到其他层需要新的服务。 有关详细信息，请参阅[创建 Azure 搜索服务](search-create-service-portal.md)。

> [!Tip]
> 查找有关如何分析搜索流量或查询性能的帮助？ 深入了解查询卷、用户搜索哪些术语，以及搜索结果在指引客户使用索引中特定文档方面的成功率如何。 有关指南，请参阅 [Azure 搜索的搜索流量分析](search-traffic-analytics.md)、[监视使用情况和查询指标](search-monitor-usage.md)以及[性能和优化](search-performance-optimization.md)。

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>管理员权限
预配或解除对服务本身的授权可以通过 Azure 订阅管理员或协同管理员完成。

在服务中，有权访问服务 URL 并拥有管理员 API 密钥的任何人都有对该服务的读写访问权限。 借助读写访问权限能够添加、删除或修改服务器对象（包括通过 [RBAC 定义的角色](search-security-rbac.md)实现的 API 密钥、索引、索引器、数据源、计划和角色分配）。

Azure 搜索服务的所有用户交互属于下列模式之一：对服务的读写访问（管理员权限）或对服务的只读访问（查询权限）。 有关详细信息，请参阅[管理 API 密钥](search-security-api-keys.md)。

<a id="sys-info"></a>

## <a name="logging-and-system-information"></a>日志记录和系统信息
Azure 搜索服务不会通过门户或程序设计界面公开单个服务的日志文件。 在基本层以及更高层上，Microsoft 会监视所有 Azure 搜索服务以达到服务级别协议 (SLA) 的 99.9% 可用性。 如果服务的速度较慢或请求吞吐量低于 SLA 阈值，则支持团队审查提供给他们的日志文件并解决问题。

根据服务的常规信息，可以通过以下方式获取信息：

* 在门户中、在服务仪表板上、通过通知、属性和状态消息。
* 使用 [PowerShell](search-manage-powershell.md) 或[管理 REST API](https://docs.microsoft.com/rest/api/searchmanagement/) 来[获取服务属性](https://docs.microsoft.com/rest/api/searchmanagement/services)，或索引资源使用状况的状态。
* 如前面所述，通过[搜索流量分析](search-traffic-analytics.md)。

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>监视资源使用情况
在仪表板中，资源监视仅限于服务仪表板中显示的信息，以及一些可通过查询服务获得的度量值。 在服务仪表板的“使用量”部分中，可以快速确定分区资源级别是否适合应用程序。

使用搜索服务 REST API，可以通过编程方式获取文档和索引的计数： 

* [获取索引统计信息](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [计数文档](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>灾难恢复和服务中断

虽然我们可以挽救数据，但 Azure 搜索在群集或数据中心级别发生服务中断时不提供服务的即时故障转移。 如果数据中心的群集出现故障，运营团队会检测故障，并努力还原服务。 在服务还原期间会出现停机的情况。 可以根据[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 申请服务信用额度来补偿服务不可用的情况。 

如果在超出 Microsoft 控制的灾难性故障中需要连续性服务，可在其他区域[预配一个附加服务](search-create-service-portal.md)并实施异地复制策略，确保索引跨所有服务完全冗余。

使用[索引器](search-indexer-overview.md)来填充和刷新索引的客户可利用相同的数据源，通过特定于地区的索引器来处理灾难恢复。 不同区域的两个服务（每个都运行索引器）可从相同数据源索引，实现异地冗余。 如果从异地冗余的数据源索引，请注意 Azure 搜索索引器只能从主要副本执行递增索引。 在故障转移事件中，请确保将索引器重新指向到新的主要副本。 

如果不使用索引器，也可使用应用程序代码将对象和数据并行推送到其他搜索服务。 有关详细信息，请参阅 [Azure 搜索中的性能和优化](search-performance-optimization.md)。

## <a name="backup-and-restore"></a>备份和还原

由于 Azure 搜索不是主数据存储解决方案，因此，我们不提供正式的自助备份和还原机制。 如果误删索引，用于创建和填充索引的应用程序代码是事实上的还原选项。 

要重新生成索引，请删除它（假设其存在），在服务中重新创建该索引，并通过从主数据存储中检索数据来重新加载该索引。 在发生区域性服务中断时，也可以联系[客户支持]()来挽救索引。


<a id="scale"></a>

## <a name="scale-up-or-down"></a>增加或减少
每个搜索服务从至少一个副本和一个分区开始操作。 如果已注册[提供专用资源的层](search-limits-quotas-capacity.md)，请单击服务仪表板中的“规模”磁贴，调整资源使用情况。

如果通过任一资源添加容量，服务会自动使用它们。 无需执行任何进一步的操作，但在新资源产生作用之前，会有轻微延迟。 可能需要 15 分钟或更长的时间才能预配其他资源。

 ![][10]

### <a name="add-replicas"></a>添加副本
增加每秒查询次数 (QPS) 或实现高可用性可通过添加副本来完成。 每个副本都有索引的副本，因此多添加一个副本将转换为可用于处理服务查询要求的多个索引。 高可用性至少需要 3 个副本（有关详细信息，请参阅[容量规划](search-capacity-planning.md)）。

具有许多副本的搜索服务可通过大量索引进行负载均衡查询请求。 在给定查询量级别的情况下，当有更多索引副本可用于为请求提供服务时，查询吞吐量的速度将更快。 如果出现查询延迟，会期望在附加副本联机后对性能产生积极的影响。

尽管添加副本时查询吞吐量会提高，但不会按在将向服务添加副本时的恰好两倍或三倍来提高。 所有搜索应用程序都会因可能影响到查询性能的外部因素而受到约束。 复杂的查询和网络延迟是造成查询响应次数变化的两个因素。

### <a name="add-partitions"></a>添加分区
大多数服务应用程序都有多个副本而非分区方面的内置需求。 如果已注册标准服务，则在需要增加文档计数的情况下，可以添加分区。 基本层不提供其他分区。

在标准层中，分区按 12 的倍数进行添加（具体而言，1、2、3、4、6 或 12）。 这是分片的项目。 索引会在 12 个分区中创建，可以全部存储在 1 个分区上，也可以平均分配到 2、3、4、6 或 12 个分区（每个分区一个分片）。

### <a name="remove-replicas"></a>删除副本
在高查询量期间过后，可在搜索查询负载正常后（例如，假日销售结束后）减少副本。

为此，请将副本滑块移回至较低数目。 无须再执行其他步骤。 降低副本计数会消除数据中心内的虚拟机。 相较于之前的情况而言，现在会在较少的 VM 上执行查询和数据引入操作。 最小限制为一个副本。

### <a name="remove-partitions"></a>删除分区
与无需执行额外工作即可删除副本相比，如果使用的存储大于可减少的存储，可能需要完成一些工作。 例如，如果解决方案使用三个分区，则在新存储空间小于所需空间时，缩减为一或两个分区将生成错误。 正如预期的那样，可以选择删除索引或相关索引内的文档来释放空间，或者保持目前配置。

无法通过任何检测方法确定哪些索引分片存储在特定分区上。 每个分区提供大约 25 GB 的存储，因此需要将存储减少到可让所拥有分区数能容纳的大小。 如果要还原为一个分区，则所有 12 个分片都需要适合。

为了帮助实现未来规划，可能需要检查存储（使用[获取索引统计信息](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)），了解实际使用了多少空间。 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>缩放和部署的最佳实践
此 30 分钟视频介绍高级部署方案的最佳实践，包括地理位置分布式工作负荷。 也可以查看 [Azure 搜索服务中的性能和优化](search-performance-optimization.md)，以获取有关介绍相同要点的帮助页。

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>后续步骤
了解服务管理的相关概念后，请考虑使用 [PowerShell](search-manage-powershell.md) 来自动执行任务。

同时建议查看[性能和优化文章](search-performance-optimization.md)。

另一条建议是观看前面部分提到的视频。 视频会更深入地讲解本部分提到的技术。

<!--Image references-->
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



