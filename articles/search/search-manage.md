---
title: "Azure 门户中 Azure 搜索服务管理"
description: "使用 Azure 门户管理 Azure 搜索，这是 Microsoft Azure 上托管的云搜索服务。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: c87d1fdd-b3b8-4702-a753-6d7e29dbe0a2
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 03/05/2017
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: e0de3b12b98bf9bf361607dac4b087e4eacabf1e
ms.lasthandoff: 03/06/2017


---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Azure 门户中 Azure 搜索服务管理
> [!div class="op_single_selector"]
> * [门户](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST API](search-get-started-management-api.md)
> 
> 

Azure 搜索是一种完全托管的、基于云的搜索服务，用于在自定义应用中生成丰富的搜索体验。 本文介绍可在 [Azure 门户](https://portal.azure.com)中对已预配的搜索服务执行的*服务管理*任务。 *服务管理*设计成轻型，它限于以下任务：

* 管理和保护对用于服务读取或写入的 *api-keys* 的访问。
* 通过更改分区和副本的分配以调整服务容量。
* 根据服务层的最大限制，监视资源使用情况。

**不在作用域** 

*内容管理*（或索引管理）是指分析搜索流量以了解查询量等操作、发现用户所搜索的字词，以及搜索结果将客户引导至索引中特定文档的成功率。 内容管理已超出本文的范围。 有关如何深入了解索引级别内部操作的说明，请参阅 [Azure 搜索的搜索流量分析](search-traffic-analytics.md)。

*查询性能*也超出了本文的范围。 有关详细信息，请参阅[监视使用情况和查询度量值](search-monitor-usage.md)以及[性能和优化](search-performance-optimization.md)。


<a id="admin-rights"></a>

## <a name="administrator-rights"></a>管理员权限
预配或解除对服务本身的授权可以通过 Azure 订阅管理员或协同管理员完成。

在服务中，具有服务 URL 与管理员 API 密钥访问权限的任何人都可以读取和写入服务，相当于可以对服务器对象（例如 API 密钥、索引、索引器、数据源、计划和角色分配）进行添加、删除或修改，就像通过 [RBAC 定义的角色](#rbac)实现一样。

Azure 搜索服务的所有用户交互属于下列模式之一：对服务的读写访问（管理员权限）或对服务的只读访问（查询权限）。 有关详细信息，请参阅[管理 API 密钥](#manage-keys)。

<a id="sys-info"></a>

## <a name="set-rbac-roles-for-administrative-access"></a>针对管理访问权限设置 RBAC 角色
对于通过门户或 Resource Manager API 管理的所有服务，Azure 提供了[基于全局角色的授权模型](../active-directory/role-based-access-control-configure.md)。 所有者、参与者和读者角色根据分配给每个角色的 Active Directory 用户、组和安全主体的服务管理，确定服务管理的级别。 

对于 Azure 搜索，RBAC 权限确定以下管理任务：

| 角色 | 任务 |
| --- | --- |
| 所有者 |创建或删除服务或者服务上的任何对象，包括 API 密钥、索引、索引器、索引器数据源和索引器计划。<p>查看服务状态，包括计数和存储大小。<p>添加或删除角色成员身份（仅所有者才能管理角色成员身份）。<p>订阅管理员和服务所有者拥有所有者角色的自动成员身份。 |
| 参与者 |访问级别与所有者的访问级别相同，不包括 RBAC 角色管理。 例如，参与者可以查看和重新生成 `api-key`，但不能修改角色成员身份。 |
| 读取器 |查看服务状态和查询密钥。 此角色的成员既不能更改服务配置，也无法查看管理密钥。 |

请注意，角色不授予对服务终结点的访问权限。 搜索服务操作（例如索引管理、索引填充和搜索数据的查询）可通过 API 密钥而非角色进行控制。 有关详细信息，请参阅[什么是基于角色的访问控制](../active-directory/role-based-access-control-what-is.md)中的“管理授权与数据操作”。

<a id="secure-keys"></a>
## <a name="logging-and-system-information"></a>日志记录和系统信息
Azure 搜索服务不会通过门户或程序设计界面公开单个服务的日志文件。 在基本层以及更高层上，Microsoft 会监视所有 Azure 搜索服务以达到服务级别协议 (SLA) 的 99.9% 可用性。 如果服务的速度较慢或请求吞吐量低于 SLA 阈值，则支持团队审查提供给他们的日志文件并解决问题。

根据服务的常规信息，可以通过以下方式获取信息：

* 在门户中、在服务仪表板上、通过通知、属性和状态消息。
* 使用 [PowerShell](search-manage-powershell.md) 或[管理 REST API](https://msdn.microsoft.com/library/azure/dn832684.aspx) 来[获取服务属性](https://msdn.microsoft.com/library/azure/dn832694.aspx)，或索引资源使用状况的状态。
* 如前面所述，通过[搜索流量分析](search-traffic-analytics.md)。

<a id="manage-keys"></a>

## <a name="manage-api-keys"></a>管理 API 密钥
对搜索服务的所有请求都需要专为服务生成的 API 密钥。 此 API 密钥是用于验证搜索服务终结点的访问的唯一机制。 

API 密钥是随机生成的数字和字母所组成的字符串。 它以独占方式由服务生成。 通过 [RBAC 权限](#rbac)，可以删除或读取密钥，但无法借助用户定义的字符串替代已生成的密钥（具体而言，如果有常用的密码，则无法借助用户定义的密码替代 API 密钥）。 

两种类型的密钥用于访问搜索服务：

* 管理员（适用于服务的任何读写操作）
* 查询（适用于只读操作，例如针对索引的查询）

预配服务时，会创建管理 API 密钥。 有两个管理密钥，分别指定为*主要*和*辅助*密钥以将它们保持在各自的位置，但事实上是可互换的。 每个服务都有两个管理密钥，以便在转换其中一个时不会丢失服务的访问权限。 可以重新生成任一管理密钥，但无法添加到管理密钥总计数。 每个搜索服务最多有两个管理密钥。

查询密钥专用于直接调用搜索的客户端应用程序。 最多可以创建 50 个查询密钥。 在应用程序代码中，可以指定搜索 URL 和查询 API 密钥，以允许对服务的只读访问。 应用程序代码还会指定应用程序使用的索引。 终结点、仅供只读访问的 API 密钥以及目标索引共同定义客户端应用程序连接的作用域和访问级别。

若要获取或重新生成 API 密钥，请打开服务仪表板。 单击“密钥”以滑动打开密钥管理页。 重新生成或创建密钥的命令都在页面顶部。 默认情况下，仅创建管理密钥。 必须手动创建这些查询 API 密钥。

 ![][9]

<a id="rbac"></a>

## <a name="secure-api-keys"></a>保护 API 密钥
通过门户或 Resource Manager 界面（PowerShell 或命令行接口）以限制访问，从而保护密钥安全。 如前所述，订阅管理员可以查看和重新生成所有 API 密钥。 作为预防措施，查看角色分配以了解谁有权访问管理密钥。

1. 在服务仪表板中，单击“访问”图标以滑动打开“用户”边栏选项卡。
   ![][7]
2. 在“用户”中，查看现有的角色分配。 正如预期的那样，订阅管理员已通过所有者角色拥有服务的完全访问权限。
3. 若要进一步研究，请单击“订阅管理员”，然后展开角色分配列表，以查看谁在搜索服务上具有共同管理权限。

查看访问权限的另一种方法是单击“用户”边栏选项卡上的“角色”。 这样一来，将显示可用的角色和已分配给每个角色的用户或组数。

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>监视资源使用情况
在仪表板中，资源监视仅限于服务仪表板中显示的信息，以及一些可通过查询服务获得的度量值。 在服务仪表板的“使用量”部分中，可以快速确定分区资源级别是否适合你的应用程序。

使用搜索服务 API，可以获取文档和索引的计数。 存在与基于定价层的这些计数相关联的硬限制。 有关详细信息，请参阅[搜索服务限制](search-limits-quotas-capacity.md)。 

* [获取索引统计信息](http://msdn.microsoft.com/library/dn798942.aspx)
* [计数文档](http://msdn.microsoft.com/library/dn798924.aspx)

> [!NOTE]
> 缓存行为可暂时放宽限制。 例如，使用共享的服务时，你可能会看到文档计数超过 10,000 个文档的硬限制。 该超出情形是暂时的，将在下一次限制强制检查中检测出来。 
> 
> 

## <a name="disaster-recovery-and-service-outages"></a>灾难恢复和服务中断

虽然我们可以挽救你的数据，但 Azure 搜索在群集或数据中心级别发生服务中断时不提供服务的即时故障转移。 如果数据中心的群集出现故障，运营团队会检测故障，并努力还原服务。 你在服务还原期间会出现停机的情况。 可以根据[服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) 申请服务信用额度来补偿服务不可用的情况。 

为确保服务的连续性，包括超出 Microsoft 控制的灾难性故障，应在其他区域[预配一个附加服务](search-create-service-portal.md)并实施异地复制策略，以确保索引跨所有服务完全冗余。

使用索引器来填充和刷新索引的客户可利用相同的数据源，通过特定于地区的索引器来处理灾难恢复。 除了索引器，也可使用应用程序代码将对象和数据并行推送到其他服务。 有关详细信息，请参阅 [Azure 搜索中的性能和优化](search-performance-optimization.md)。

## <a name="backup-and-restore"></a>备份和还原

由于 Azure 搜索不是主数据存储解决方案，因此，我们不提供正式的自助备份和还原机制。 如果误删索引，用于创建和填充索引的应用程序代码是事实上的还原选项。 

若要重新生成索引，请删除它（假设其存在），在服务中重新创建该索引，然后通过从主数据存储中检索数据来重新加载该索引。 在发生区域性服务中断时，也可以联系[客户支持]()来挽救索引。


<a id="scale"></a>

## <a name="scale-up-or-down"></a>增加或减少
每个搜索服务从至少一个副本和一个分区开始操作。 如果已使用[基本或标准定价层](search-limits-quotas-capacity.md)注册专用资源，可以在服务仪表板中单击“缩放”磁贴，来调整服务所用的分区和副本数。

如果通过任一资源添加容量，服务将自动使用它们。 无需执行任何进一步的操作，但在新资源产生作用之前，会有轻微延迟。 可能需要 15 分钟或更长的时间才能预配其他资源。

 ![][10]

### <a name="add-replicas"></a>添加副本
增加每秒查询次数 (QPS) 或实现高可用性可通过添加副本来完成。 每个副本都有索引的副本，因此多添加一个副本将转换为可用于处理服务查询要求的多个索引。 高可用性至少需要 3 个副本（有关详细信息，请参阅[容量规划](search-capacity-planning.md)）。

具有许多副本的搜索服务可通过大量索引进行负载平衡查询请求。 在给定查询量级别的情况下，当有更多索引副本可用于为请求提供服务时，查询吞吐量的速度将更快。 如果出现查询延迟，会期望在附加副本联机后对性能产生积极的影响。

尽管添加副本时查询吞吐量会提高，但不会按在将向服务添加副本时的恰好两倍或三倍来提高。 所有搜索应用程序都会因可能影响到查询性能的外部因素而受到约束。 复杂的查询和网络延迟是造成查询响应次数变化的两个因素。

### <a name="add-partitions"></a>添加分区
大多数服务应用程序都有多个副本而非分区方面的内置需求。 如果已注册标准服务，则在需要增加文档计数的情况下，可以添加分区。 基本层不提供其他分区。

在标准层中，分区按 12 的倍数进行添加（具体而言，1、2、3、4、6 或 12）。 这是分片的项目。 索引会在 12 个分区中创建，可以全部存储在 1 个分区上，也可以平均分配到 2、3、4、6 或 12 个分区（每个分区一个分片）。

### <a name="remove-replicas"></a>删除副本
在高查询量期间过后，在搜索查询负载正常后（例如，假日销售结束后），很有可能会减少副本。

为此，请将副本滑块移回至较低数目。 无须再执行其他步骤。 降低副本计数会消除数据中心内的虚拟机。 相较于之前的情况而言，现在将在较少的 VM 上执行查询和数据引入操作。 最小限制为一个副本。

### <a name="remove-partitions"></a>删除分区
与无需执行额外工作即可删除副本相比，如果使用的存储大于可减少的存储，你可能需要完成一些工作。 例如，如果解决方案使用三个分区，则在新存储空间小于所需空间时，缩减为一或两个分区将生成错误。 正如预期的那样，可以选择删除索引或相关索引内的文档来释放空间，或者保持目前配置。

不存在任何检测方法可告诉你哪些索引分片存储在特定分区上。 每个分区提供大约 25 GB 的存储，因此需要将存储减少到可让所拥有分区数能容纳的大小。 如果要还原为一个分区，则所有 12 个分片都需要适合。

为了帮助实现未来规划，你可能需要检查存储（使用[获取索引统计信息](http://msdn.microsoft.com/library/dn798942.aspx)），了解实际使用了多少空间。 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>缩放和部署的最佳实践
此 30 分钟视频介绍高级部署方案的最佳实践，包括地理位置分布式工作负荷。 也可以查看 [Azure 搜索服务中的性能和优化](search-performance-optimization.md)，以获取有关介绍相同要点的帮助页。

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>后续步骤
了解与服务管理相关的操作类型后，请考虑服务管理的各种方法：

* [PowerShell](search-manage-powershell.md)
* [管理 REST API](search-get-started-management-api.md)

此外，如果尚未执行此操作，请查看[性能和优化文章](search-performance-optimization.md)，并选择性地观看上一部分所述的视频，以获取推荐技术的深入探讨和演示。

<!--Image references-->
[7]: ./media/search-manage/rbac-icon.png
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png




