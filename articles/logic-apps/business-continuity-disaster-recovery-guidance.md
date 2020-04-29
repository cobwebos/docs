---
title: 业务连续性和灾难恢复
description: 设计你的策略来保护数据、快速恢复中断性事件、恢复关键业务功能所需的资源，以及维护 Azure 逻辑应用的业务连续性
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 7bf71ce7c44229ccf19022e9cfb0162f9d77cd97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437702"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>适用于 Azure 逻辑应用的业务连续性和灾难恢复

为了帮助降低无法预测的事件对您的业务和客户造成的影响和影响，请确保已准备好[*灾难恢复*（DR）](https://en.wikipedia.org/wiki/Disaster_recovery)解决方案，以便您可以保护数据，快速还原支持关键业务功能的资源，并使操作保持运行以维持[*业务连续性*（BC）](https://en.wikipedia.org/wiki/Business_continuity_planning)。 例如，中断可能包括中断、底层基础结构或组件（如存储、网络或计算资源）、不可恢复的应用程序故障，甚至整个数据中心损失。 通过实现业务连续性和灾难恢复（BCDR）解决方案，企业或组织可以更快地做出中断、计划内或计划外的响应，并缩短客户的停机时间。

本文提供使用[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)生成自动化工作流时可应用的 BCDR 指导和策略。 逻辑应用工作流通过减少您必须编写的代码，帮助您更轻松地在应用、云服务和本地系统之间集成和协调数据。 在规划 BCDR 时，请确保考虑的是逻辑应用，还应考虑使用逻辑应用时所用的 Azure 资源：

* 从逻辑应用创建的与其他应用、服务和系统的[连接](../connectors/apis-list.md)。 有关详细信息，请参阅本主题后面的[与资源的连接](#resource-connections)。

* [本地数据网关](../logic-apps/logic-apps-gateway-connection.md)，是你在逻辑应用中创建和使用的 Azure 资源，用于访问本地系统中的数据。 每个网关资源代表本地计算机上单独的[数据网关安装](../logic-apps/logic-apps-gateway-install.md)。 有关详细信息，请参阅本主题后面的["本地数据网关](#on-premises-data-gateways)"。

* 用于定义和存储逻辑应用用于企业[到企业（B2B）企业集成](../logic-apps/logic-apps-enterprise-integration-overview.md)方案的项目的[集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)。 例如，你可以[为集成帐户设置跨区域灾难恢复](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)。

* [集成服务环境（ISEs）](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ，可在其中创建在 Azure 虚拟网络内的独立逻辑应用运行时实例中运行的逻辑应用。 然后，这些逻辑应用可以访问在该虚拟网络中受防火墙保护的资源。

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>主要和辅助位置

每个逻辑应用都需要指定要用于部署的位置。 此位置是全局多租户 Azure 中的公共区域，如 "美国西部" 或以前创建并部署到 Azure 虚拟网络中的 integration services 环境（ISE）。 在 ISE 中运行逻辑应用类似于在全球 Azure 区域中运行逻辑应用，这意味着你的灾难恢复策略可以应用于这两种方案。 但是，ISEs 有其他注意事项，如配置仅对 ISEs 可用的资源的访问。

> [!NOTE]
> 如果你的逻辑应用也适用于 B2B 项目，例如贸易合作伙伴、协议、架构、映射和证书（存储在集成帐户中），则你的集成帐户和逻辑应用必须指定相同的位置。

此灾难恢复策略重点介绍如何设置主要逻辑应用，以便在 Azure 逻辑应用也可用的备用位置上[*故障转移*](https://en.wikipedia.org/wiki/Failover)到备用或备份逻辑应用。 这样一来，如果主要的损失、中断或故障，辅助数据库便可执行工作。 此策略要求你的辅助逻辑应用和相关资源已在备用位置部署并准备就绪。

如果遵循良好的 DevOps 实践，则已使用[Azure 资源管理器模板](../azure-resource-manager/management/overview.md)来定义和部署逻辑应用及其相关资源。 资源管理器模板使你能够使用单个部署定义，然后使用参数文件提供要用于每个部署目标的配置值。 此功能意味着，你可以将同一逻辑应用部署到不同的环境，例如开发、测试和生产。 你还可以将相同的逻辑应用部署到不同的 Azure 区域或 ISEs，后者支持使用[配对区域](../best-practices-availability-paired-regions.md)的灾难恢复策略。

对于故障转移策略，你的逻辑应用和位置必须满足以下要求：

* 辅助逻辑应用实例有权访问与主逻辑应用实例相同的应用、服务和系统。

* 这两个逻辑应用实例具有相同的主机类型。 因此，两个实例都部署到全局多租户 Azure 中的区域，或这两个实例都部署到 ISEs，这使逻辑应用可以直接访问 Azure 虚拟网络中的资源。 有关 BCDR 的配对区域的最佳实践和详细信息，请参阅[业务连续性和灾难恢复（BCDR）： Azure 配对区域](../best-practices-availability-paired-regions.md)。

  例如，当主逻辑应用在 ISE 中运行并使用[ise 版本管理连接器](../connectors/apis-list.md#ise-connectors)、调用 Azure 虚拟网络中的资源的 HTTP 操作或同时使用这两者时，主要和辅助位置都必须 ISEs。 在此方案中，辅助逻辑应用还必须在辅助位置中具有类似的设置，作为主要逻辑应用。

  > [!NOTE]
  > 对于更高级的方案，可以将多租户 Azure 和 ISE 作为位置混合使用。 但是，请确保考虑并了解[逻辑应用在 ISE 与多租户 Azure 中的运行情况之间的区别](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)。

* 如果使用 ISEs，请[确保它们已横向扩展或具有足够的容量](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)来处理负载。

#### <a name="example-multi-tenant-azure"></a>示例：多租户 Azure

此示例显示了主要和辅助逻辑应用实例，这些实例部署到全局多租户 Azure 中适用于此方案的各个区域。 单个[资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)定义逻辑应用实例和这些逻辑应用所需的依赖资源。 单独的参数文件指定要用于每个部署位置的配置值：

![位于不同位置的主要和辅助逻辑应用实例](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>示例： Integration service 环境

此示例显示了前面的主要和辅助逻辑应用实例，但部署到了单独的 ISEs。 单个资源管理器模板定义逻辑应用实例、这些逻辑应用所需的从属资源，以及 ISEs 作为部署位置。 单独的参数文件定义要用于每个位置中的部署的配置值：

![位于不同位置的主要和辅助逻辑应用](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>与资源的连接

Azure 逻辑应用提供[内置的触发器和操作，以及](../connectors/apis-list.md)你的逻辑应用可用于处理其他应用、服务、系统和其他资源（例如 Azure 存储帐户、SQL Server 数据库、Office 365 Outlook 电子邮件帐户等）的数百个托管连接器。 如果逻辑应用需要访问这些资源，可以创建连接来验证对这些资源的访问权限。 每个连接都是位于特定位置的单独 Azure 资源，不能由其他位置的资源使用。

对于灾难恢复策略，请考虑依赖资源相对于逻辑应用实例的位置：

* 主实例和从属资源存在于不同的位置。 在这种情况下，你的辅助实例可以连接到相同的依赖资源或终结点。 但是，你应专门为辅助实例创建连接。 这样一来，如果主位置变为不可用，则辅助数据库的连接不会受到影响。

  例如，假设主要逻辑应用连接到 Salesforce 之类的外部服务。 通常，外部服务的可用性和位置与逻辑应用的可用性无关。 在这种情况下，你的辅助实例可以连接到相同的服务，但应具有其自己的连接。

* 主实例和从属资源都存在于同一位置。 在这种情况下，依赖资源应该在不同的位置包含备份或复制版本，以便辅助实例仍可访问这些资源。

  例如，假设主要逻辑应用连接到位于同一位置或区域的服务，例如，Azure SQL 数据库。 如果整个区域变得不可用，则该区域中的 Azure SQL 数据库服务也可能不可用。 在这种情况下，你希望你的辅助实例使用已复制或备份的数据库以及与该数据库的单独连接。

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>本地数据网关

如果逻辑应用在多租户 Azure 中运行，并且需要访问本地资源（例如 SQL Server 数据库），则需要在本地计算机上安装本地[数据网关](../logic-apps/logic-apps-gateway-install.md)。 然后，你可以在 Azure 门户中创建数据网关资源，以便逻辑应用可以在创建到资源的连接时使用网关。

数据网关资源与位置或 Azure 区域相关联，就像逻辑应用资源。 在灾难恢复策略中，请确保 "数据网关" 可供逻辑应用使用。 当你有多个网关安装时，你可以[为网关启用高可用性](../logic-apps/logic-apps-gateway-install.md#high-availability)。

> [!NOTE]
> 如果逻辑应用在 integration service 环境（ISE）中运行，并且仅将 ISE 版本控制连接器用于本地数据源，则不需要数据网关，因为 ISE 连接器提供对本地资源的直接访问权限。
>
> 如果你需要的本地资源没有 ISE 版本控制连接器，则逻辑应用仍可使用非 ISE 连接器创建连接，该连接器在全局多租户 Azure 中运行，而不是在 ISE 中运行。 但是，此连接需要本地数据网关。

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>主动-主动和主动-被动角色

你可以设置主要和辅助位置，以便这些位置中的逻辑应用实例可以扮演这些角色：

| 主要辅助角色 | 说明 |
|------------------------|-------------|
| *主动-主动* | 这两个位置中的主要逻辑应用实例和辅助逻辑应用实例通过以下任一模式主动处理请求： <p><p>- *负载平衡*：可以让两个实例都侦听终结点，并根据需要对每个实例的流量进行负载均衡。 <p>- *竞争使用者*：可以将这两个实例作为竞争使用者，使实例可以从队列中争用消息。 如果一个实例失败，其他实例将接管工作负荷。 |
| *主动-被动* | 主逻辑应用实例主动处理整个工作负荷，而辅助实例为被动（禁用或非活动）。 辅助数据库将等待主节点不可用或由于中断或故障而无法正常工作的信号，并接管工作负荷作为活动实例。 |
| 组合 | 某些逻辑应用扮演主动-主动角色，而其他逻辑应用则扮演主动-被动角色。 |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>主动-主动示例

这些示例显示了主动-主动设置，其中的两个逻辑应用实例主动处理请求或消息。 某些其他系统或服务在实例之间分发请求或消息，例如以下选项之一：

* "物理" 负载均衡器，例如路由流量的硬件

* "软" 负载均衡器，例如[Azure 负载均衡器](../load-balancer/load-balancer-overview.md)或[azure API 管理](../api-management/api-management-key-concepts.md)。 利用 API 管理，你可以指定确定如何对传入流量进行负载均衡的策略。 或者，你可以使用支持状态跟踪的服务，例如[Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)。

  尽管此示例主要显示 Azure 负载均衡器，但可以使用最适合于方案需求的选项：

  ![使用负载均衡器或有状态服务的 "主动-主动" 设置](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* 每个逻辑应用实例都作为一个使用者，并使两个实例都可以从队列中争用消息：

  ![使用 "竞争使用者" 的 "主动-主动" 设置](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>主动-被动示例

此示例显示了主动-被动设置，其中主逻辑应用实例在一个位置处于活动状态，而辅助实例在另一个位置保持不活动状态。 如果主要用户遇到中断或失败，则可以让操作员运行脚本来激活辅助工作负荷。

![使用 "竞争性使用者" 的 "主动-被动" 安装](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>结合主动-主动和主动-被动

此示例显示了一个组合的设置，其中主位置同时具有活动的逻辑应用实例，而辅助位置具有主动-被动逻辑应用实例。 如果主位置遇到中断或故障，则辅助位置中的活动逻辑应用（已在处理部分工作负荷）可以接管整个工作负荷。

* 在主位置，活动逻辑应用会侦听 Azure 服务总线队列中的消息，而另一个活动的逻辑应用将使用 Office 365 Outlook 轮询触发器来检查电子邮件。

* 在辅助位置，活动逻辑应用可通过侦听和争用来自同一服务总线队列的消息，在主位置中使用逻辑应用。 同时，当主位置不可用时，被动非活动逻辑应用会等待等待检查电子邮件，但会被*禁用*以避免重新读取的电子邮件。

![使用重复触发器的 "主动-被动" 和 "主动-被动" 组合](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>逻辑应用状态和历史记录

触发逻辑应用并开始运行时，应用的状态将存储在应用启动的同一位置，并且不可转让到另一个位置。 如果发生失败或中断，则会放弃任何正在进行的工作流实例。 如果设置了主位置和辅助位置，则新工作流实例将开始在辅助位置运行。

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>减少已放弃的正在进行的实例

若要最大程度地减少放弃的正在进行的工作流实例的数量，可以从可实现的各种消息模式中进行选择，例如：

* [固定路由单模式](https://docs.microsoft.com/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  这种企业消息模式，可将业务流程拆分为较小的阶段。 对于每个阶段，设置一个逻辑应用，用于处理该阶段的工作负荷。 若要彼此通信，逻辑应用使用异步消息传送协议，如 Azure 服务总线队列或主题。 将进程划分为较小的阶段时，会减少出现故障的逻辑应用实例的业务流程数。 有关此模式的更多常规信息，请参阅[企业集成模式-传送名单](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html)。

  此示例显示了一种路由 slip 模式，其中每个逻辑应用代表一个阶段，并使用服务总线队列与进程中的下一个逻辑应用进行通信。

  ![使用 Azure 服务总线队列将业务流程拆分为逻辑应用表示的阶段](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  如果主要和辅助逻辑应用实例在其位置中都遵循相同的传送名单模式，则可以通过为这些实例设置[主动-主动角色](#roles)来实现[竞争使用者模式](https://docs.microsoft.com/azure/architecture/patterns/competing-consumers)。

* [进程管理器（broker）模式](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [无超时模式的速览锁定](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>对触发器和运行历史记录的访问

若要获取有关逻辑应用过去的工作流执行的详细信息，可以查看应用的触发器和运行历史记录。 逻辑应用的历史记录执行历史记录存储在逻辑应用运行所在的同一位置或区域，这意味着您不能将此历史记录迁移到其他位置。 如果主实例故障转移到辅助实例，则只能访问每个实例的触发器，并在这些实例运行的各个位置运行历史记录。 但是，可以通过设置逻辑应用将诊断事件发送到 Azure Log Analytics 工作区，获取有关逻辑应用历史记录的位置不可知的信息。 然后，你可以查看跨多个位置运行的逻辑应用的运行状况和历史记录。

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>触发器类型指南

逻辑应用中使用的触发器类型确定了如何在灾难恢复策略中跨位置设置逻辑应用的选项。 下面是可在逻辑应用中使用的可用触发器类型：

* [重复触发器](#recurrence-trigger)
* [轮询触发器](#polling-trigger)
* [请求触发器](#request-trigger)
* [Webhook 触发器](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>重复触发器

**定期**触发器独立于任何特定服务或终结点，并只根据指定的计划（例如：

* 固定频率和间隔，例如每10分钟
* 更高级的计划，例如每月最后一个星期一下午5:00

逻辑应用从定期触发器开始时，需要使用[主动-被动角色](#roles)设置主逻辑应用和辅助逻辑应用实例。 若要减少*恢复时间目标*（RTO），这指的是在发生中断或灾难后恢复业务流程的目标持续时间，可以使用[主动-被动角色](#roles)和[被动活动角色](#roles)的组合设置逻辑应用实例。 在此设置中，你可以在各个位置拆分计划。

例如，假设有一个逻辑应用需要每10分钟运行一次。 你可以设置逻辑应用和位置，以便在主位置变为不可用时，辅助位置可以接管工作：

![使用重复触发器的 "主动-被动" 和 "被动-主动" 组合](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* 在主位置，为这些逻辑应用设置[主动-被动角色](#roles)：

  * 对于启用了*活动*的逻辑应用，将定期触发器设置为在该小时的顶部开始，每隔20分钟重复一次，例如 9:00 am、9:20 AM 等。

  * 对于*被动*禁用的逻辑应用，将定期触发器设置为相同的计划，但从该小时之后的10分钟开始，每隔20分钟重复一次，例如 9:10 am、9:30 AM 等。

* 在辅助位置，为这些逻辑应用设置[被动-主动](#roles)：

  * 对于*被动*禁用的逻辑应用，将定期触发器设置为与主位置中的活动逻辑应用相同的计划，该计划位于小时的顶部，每20分钟重复一次，例如 9:00 am、9:10 AM 等。

  * 对于启用了*活动*的逻辑应用，请将定期触发器设置为与主位置中的被动逻辑应用相同的计划，这是在该时间之后的10分钟开始，每隔20分钟重复一次，例如 9:10 am、9:20 AM 等。

现在，如果在主位置发生了中断性事件，请在备用位置激活被动逻辑应用。 这样一来，如果发现故障会耗费时间，则此设置将限制在该延迟内丢失的重复次数。

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>轮询触发器

若要定期检查特定服务或终结点是否提供用于处理的新数据，逻辑应用可以使用*轮询*触发器，该触发器根据固定的定期计划重复调用服务或终结点。 服务或终结点提供的数据可以具有以下任一类型：

* 静态数据，描述始终可读取的数据
* 可变数据，描述读取后不再可用的数据

为了避免重复读取相同的数据，逻辑应用需要通过在客户端或服务器、服务或系统端保持状态来记住以前读取了哪些数据。

* 适用于客户端状态的逻辑应用使用可以保持状态的触发器。

  例如，从电子邮件收件箱读取新消息的触发器需要触发器记住最近读取的消息。 这样，触发器只有在下一个未读消息到达时才会启动逻辑应用。

* 使用服务器、服务或系统端状态的逻辑应用使用服务器、服务或系统端的属性值或设置。

  例如，从数据库读取行的基于查询的触发器需要行具有设置为`isRead` `FALSE`的列。 每当触发器读取行时，逻辑应用会通过将`isRead`列从`FALSE`更改为来`TRUE`更新该行。

  这种服务器端方法的工作方式类似于服务总线队列或主题，其中包含在逻辑应用处理消息时触发器可以读取和锁定消息的队列语义。 逻辑应用完成处理后，触发器将从队列或主题中删除该消息。

从灾难恢复的角度来看，当你设置逻辑应用的主实例和辅助实例时，请确保你根据逻辑应用是否跟踪客户端或服务器端的状态来考虑这些行为：

* 对于适用于客户端状态的逻辑应用，请确保逻辑应用不会多次读取同一消息。 在任意特定时间，只能有一个位置有活动的逻辑应用实例。 在主实例故障转移到备用位置之前，请确保备用位置中的逻辑应用实例处于非活动状态或处于禁用状态。

  例如，Office 365 Outlook 触发器维护客户端状态并跟踪最近阅读的电子邮件的时间戳，以避免读取重复项。

* 对于适用于服务器端状态的逻辑应用，你可以将逻辑应用实例设置为扮演[活动活动的角色](#roles)，在这些角色[中，](#roles)备用实例会等待，直到主实例故障转移到备用位置。

  例如，从消息队列（如 Azure 服务总线队列）读取将使用服务器端状态，因为队列服务会对消息维护锁定，以防其他客户端读取相同的消息。

  > [!NOTE]
  > 如果逻辑应用需要按特定顺序（例如从服务总线队列）读取消息，则可以使用竞争性使用者模式，但仅当与服务总线会话（也称为[*顺序保护*模式](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy)）结合使用时才可使用。 否则，你必须设置具有主动-被动角色的逻辑应用实例。

<a name="request-trigger"></a>

### <a name="request-trigger"></a>请求触发器

**Request**触发器使逻辑应用可从其他应用、服务和系统调用，通常用于提供以下功能：

* 其他人可以调用的逻辑应用的直接 REST API

  例如，使用 Request 触发器启动逻辑应用，以便其他逻辑应用可以使用 "**调用工作流-逻辑应用**" 操作调用触发器。

* 逻辑应用的[webhook](#webhook-trigger)或回调机制

* 一种方法，可手动运行用户操作或例程来调用逻辑应用，例如，通过使用执行特定任务的 PowerShell 脚本

从灾难恢复的角度来看，Request 触发器是被动的接收方，因为逻辑应用不会执行任何操作，而是等到其他某个服务或系统显式调用触发器。 作为被动终结点，你可以通过以下方式设置主实例和辅助实例：

* [主动-主动](#roles)：这两个实例主动处理请求或调用。 调用方或路由器平衡或分配这些实例之间的流量。

* [主动-被动](#roles)：仅主实例处于活动状态并处理所有工作，而辅助实例等待，直到主实例发生故障或发生故障。 调用方或路由器确定何时调用辅助实例。

作为推荐的体系结构，可以使用 Azure API 管理作为使用请求触发器的逻辑应用的代理。 API 管理提供[内置的跨区域复原功能，以及将流量路由到多个终结点的功能](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)。

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Webhook 触发器

*Webhook*触发器为逻辑应用提供了通过向该服务传递*回调 URL*来订阅服务的功能。 然后，逻辑应用可以侦听并等待特定事件发生在该服务终结点上。 事件发生时，服务将使用回调 URL 调用 webhook 触发器，然后将运行逻辑应用。 启用后，webhook 触发器会订阅服务。 禁用后，该触发器将从服务中取消订阅。

从灾难恢复的角度来看，请设置使用 webhook 触发器来播放主动-被动角色的主要和辅助实例，因为只有一个实例应该接收来自订阅的终结点的事件或消息。

## <a name="assess-primary-instance-health"></a>评估主实例运行状况

要使灾难恢复策略发挥作用，解决方案需要执行以下任务：

* [检查主实例的可用性](#check-primary-availability)
* [监视主实例的运行状况](#monitor-primary-health)
* [激活辅助实例](#activate-secondary)

本部分介绍了一种可将其作为自己的设计的基础的解决方案。 下面是此解决方案的高级视觉对象概述：

![创建监视主要位置的运行状况检查逻辑应用的监视逻辑应用](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>检查主实例可用性

若要确定主实例是否可用、正在运行且能够正常工作，可以创建与主实例位于同一位置的 "运行状况检查" 逻辑应用。 然后，可以从备用位置调用此运行状况检查应用。 如果运行状况检查应用成功响应，则该区域中的 Azure 逻辑应用服务的底层基础结构可用且正常工作。 如果运行状况检查应用无法响应，你可以假定该位置不再正常。

对于此任务，请创建执行以下任务的基本运行状况检查逻辑应用：

1. 使用请求触发器从监视程序应用接收调用。

1. 使用指示所选逻辑应用是否仍可使用响应操作的状态进行响应。

   > [!IMPORTANT]
   > 运行状况检查逻辑应用必须使用响应操作，以便应用以同步方式进行响应，而不是异步响应。

1. （可选）若要进一步确定主位置是否正常运行，你可以在此位置中考虑与目标逻辑应用交互的任何其他服务的运行状况。 只需扩展运行状况检查逻辑应用即可评估其他这些服务的运行状况。

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>创建监视器逻辑应用

若要监视主实例的运行状况并调用运行状况检查逻辑应用，请在*备用位置*创建 "监视器" 逻辑应用。 例如，你可以设置监视器逻辑应用，以便在调用运行状况检查逻辑失败时，监视器可以将警报发送到你的运营团队，以便他们能够调查故障以及主实例不响应的原因。

> [!IMPORTANT]
> 请确保你的监视器逻辑应用位于*与主位置不同的位置*。 如果主要位置的逻辑应用服务遇到问题，则可能不会运行你的监视程序逻辑应用。

对于此任务，请在辅助位置创建一个用于执行以下任务的监视程序逻辑应用：

1. 使用定期触发器基于固定或计划定期运行。

   可以在恢复时间目标（RTO）的容差级别下将重复周期设置为一个值。

1. 使用 HTTP 操作在主位置调用运行状况检查逻辑应用，例如：

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>激活辅助实例

若要自动激活辅助实例，可以创建一个逻辑应用，用于调用管理 API （如[Azure 资源管理器连接器](https://docs.microsoft.com/connectors/arm/)）在辅助位置激活适当的逻辑应用。 在发生特定数量的故障后，可以扩展监视应用以调用此激活逻辑应用。

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>收集诊断数据

可以为逻辑应用运行设置日志记录，并将生成的诊断数据发送到 Azure 存储、Azure 事件中心和 Azure Log Analytics 等服务，以便进行进一步的处理和处理。

* 如果要将此数据与 Azure Log Analytics 一起使用，可以通过设置逻辑应用的**诊断设置**并将数据发送到多个 Log Analytics 工作区，使数据可用于主位置和辅助位置。 有关详细信息，请参阅[设置 Azure Monitor 日志和收集 Azure 逻辑应用的诊断数据](../logic-apps/monitor-logic-apps-log-analytics.md)。

* 如果要将数据发送到 Azure 存储或 Azure 事件中心，可以通过设置异地冗余，使数据可用于主位置和辅助位置。 有关详细信息，请参阅以下文章：<p>

  * [Azure Blob 存储灾难恢复和帐户故障转移](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure 事件中心异地灾难恢复](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>后续步骤

* [Azure 的复原能力概述](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
* [特定 Azure 服务的复原能力检查表](https://docs.microsoft.com/azure/architecture/checklist/resiliency-per-service)
* [Azure 中的恢复数据管理](https://docs.microsoft.com/azure/architecture/framework/resiliency/data-management)
* [Azure 应用程序的备份和灾难恢复](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)
* [发生区域范围的服务中断后进行恢复](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
* [适用于 Azure 服务的 Microsoft 服务级别协议（Sla）](https://azure.microsoft.com/support/legal/sla/)
