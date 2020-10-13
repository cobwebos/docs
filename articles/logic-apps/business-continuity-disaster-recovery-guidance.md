---
title: 业务连续性和灾难恢复
description: 设计策略以保护数据、在发生中断性事件后快速恢复、还原关键业务功能所需的资源，以及保持 Azure 逻辑应用的业务连续性
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 0a36cb468ebcb77c0614bffd0afc392df3655c20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89658203"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Azure 逻辑应用的业务连续性和灾难恢复

为了帮助降低不可预测的事件对业务和客户造成的影响，请确保部署一个[灾难恢复 (DR) 解决方案](https://en.wikipedia.org/wiki/Disaster_recovery)，以便可以保护数据、快速还原用于支持关键业务功能的资源，并使操作持续运行以保持[业务连续性 (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning)。  例如，中断可能包括服务中断、底层基础结构或组件（例如存储、网络或计算资源）损毁、不可恢复的应用程序故障，甚至整个数据中心损毁。 准备好业务连续性和灾难恢复 (BCDR) 解决方案后，企业或组织可以更快地对计划内或计划外中断做出响应，并缩短客户的停机时间。

本文提供在使用 [Azure 逻辑应用](../logic-apps/logic-apps-overview.md)构建自动化工作流时可以应用的 BCDR 指导和策略。 逻辑应用工作流可以减少需要编写的代码量，帮助你更轻松地在应用、云服务与本地系统之间集成和协调数据。 规划 BCDR 时，不仅需要考虑到逻辑应用，而且还必须考虑到下述要在逻辑应用中使用的 Azure 资源：

* 从逻辑应用建立的到其他应用、服务和系统的[连接](../connectors/apis-list.md)。 有关详细信息，请参阅本主题稍后的[与资源的连接](#resource-connections)。

* [本地数据网关](../logic-apps/logic-apps-gateway-connection.md)，这是你在逻辑应用中创建的 Azure 资源，用于在其中访问本地系统中的数据。 每个网关资源在本地计算机上代表一个独立的[数据网关安装](../logic-apps/logic-apps-gateway-install.md)。 有关详细信息，请参阅本主题稍后的[本地数据网关](#on-premises-data-gateways)。

* [集成帐户](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)，将在其中定义和存储逻辑应用对[企业到企业 (B2B) 企业集成](../logic-apps/logic-apps-enterprise-integration-overview.md)方案使用的项目。 例如，可以[为集成帐户设置跨区域灾难恢复](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)。

* [Integration service 环境 (ISEs) ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ，你可以在其中创建在 Azure 虚拟网络内的独立逻辑应用运行时实例中运行的逻辑应用。 然后，这些逻辑应用可以访问在该虚拟网络中受防火墙保护的资源。

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>主要和辅助位置

每个逻辑应用需要指定用于部署的位置。 此位置是全局多租户 Azure 中的公共区域，如 "美国西部"，或 (ISE) 之前创建并部署到 Azure 虚拟网络中的集成服务环境。 在 ISE 中运行逻辑应用类似于在全球 Azure 区域中运行逻辑应用，这意味着你的灾难恢复策略可以应用于这两种方案。 但是，ISEs 有其他注意事项，如配置仅对 ISEs 可用的资源的访问。

> [!NOTE]
> 如果逻辑应用还使用存储在集成帐户中的 B2B 项目（例如贸易合作伙伴、协议、架构、映射和证书），则集成帐户和逻辑应用必须指定同一位置。

此灾难恢复策略侧重于将主要逻辑应用设置为[故障转移](https://en.wikipedia.org/wiki/Failover)到也可以使用 Azure 逻辑应用的备用位置中的待机或备份逻辑应用。 这样，在主要逻辑应用发生损毁、中断或故障时，辅助逻辑应用就可以接管工作。 此策略要求辅助逻辑应用和从属资源已在备用位置中部署且准备就绪。

如果你遵循了良好的 DevOps 实践，则已经使用 [Azure 资源管理器模板](../azure-resource-manager/management/overview.md)定义并部署了逻辑应用及其从属资源。 资源管理器模板提供的功能允许你先使用单个部署定义，然后使用参数文件来提供用于每个部署目标的配置值。 此功能意味着，可将同一逻辑应用部署到不同的环境（例如开发、测试和生产环境）。 你还可以将相同的逻辑应用部署到不同的 Azure 区域或 ISEs，后者支持使用 [配对区域](../best-practices-availability-paired-regions.md)的灾难恢复策略。

对于故障转移策略，逻辑应用和位置必须满足以下要求：

* 辅助逻辑应用实例能够访问主要逻辑应用实例所能访问的应用、服务和系统。

* 这两个逻辑应用实例的主机类型相同。 因此，两个实例都部署到全局多租户 Azure 中的区域，或这两个实例都部署到 ISEs，这使逻辑应用可以直接访问 Azure 虚拟网络中的资源。 有关 BCDR 配对区域的最佳做法和详细信息，请参阅[业务连续性和灾难恢复 (BCDR)：Azure 配对区域](../best-practices-availability-paired-regions.md)。

  例如，当主逻辑应用在 ISE 中运行并使用 [ise 版本管理连接器](../connectors/apis-list.md#ise-connectors)、调用 Azure 虚拟网络中的资源的 HTTP 操作或同时使用这两者时，主要和辅助位置都必须 ISEs。 在此方案中，辅助逻辑应用还必须在辅助位置中具有类似的设置，作为主要逻辑应用。

  > [!NOTE]
  > 对于更高级的方案，可以将多租户 Azure 和 ISE 作为位置混合使用。 但是，请确保考虑并了解 [逻辑应用在 ISE 与多租户 Azure 中的运行情况之间的区别](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)。

* 如果使用 ISEs，请 [确保它们已横向扩展或具有足够的容量](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) 来处理负载。

#### <a name="example-multi-tenant-azure"></a>示例：多租户 Azure

此示例演示了此方案的主要和辅助逻辑应用实例，这些实例部署到了全球多租户 Azure 中的单独区域。 单个[资源管理器模板](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)定义了逻辑应用实例以及这些逻辑应用所需的从属资源。 单独的参数文件指定用于每个部署位置的配置值：

![位于不同位置的主要和辅助逻辑应用实例](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>示例： Integration service 环境

此示例显示了前面的主要和辅助逻辑应用实例，但部署到了单独的 ISEs。 单个资源管理器模板定义逻辑应用实例、这些逻辑应用所需的从属资源，以及 ISEs 作为部署位置。 单独的参数文件定义要用于每个位置中的部署的配置值：

![位于不同位置的主要和辅助逻辑应用](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>与资源的连接

Azure 逻辑应用提供 [内置的触发器和操作，以及](../connectors/apis-list.md) 你的逻辑应用可用于处理其他应用、服务、系统和其他资源（例如 Azure 存储帐户、SQL Server 数据库、工作或学校电子邮件帐户等）的数百个托管连接器。 如果逻辑应用需要访问这些资源，你可以创建连接来验证对这些资源的访问。 每个连接是位于特定位置的单独 Azure 资源，不可由其他位置中的资源使用。

对于灾难恢复策略，请考虑从属资源相对于逻辑应用实例所在的位置：

* 主要实例和从属资源位于不同的位置。 在这种情况下，辅助实例可以连接到相同的从属资源或终结点。 但是，应该专门为辅助实例创建连接。 这样，如果主要位置不可用，辅助位置的连接将不受影响。

  例如，假设主要逻辑应用连接到 Salesforce 之类的外部服务。 通常，外部服务的可用性和位置与逻辑应用的可用性无关。 在这种情况下，辅助实例可以连接到同一服务，但应具有自身的连接。

* 主要实例和从属资源位于同一位置。 在这种情况下，从属资源应在不同的位置具有备份或复制的版本，以便辅助实例仍可访问这些资源。

  例如，假设主要逻辑应用连接到位于同一位置或区域的服务（例如 Azure SQL 数据库）。 如果这整个区域变得不可用，则该区域中的 Azure SQL 数据库服务也可能不可用。 在这种情况下，你会希望辅助实例使用复制的数据库或备份数据库，并与该数据库建立单独的连接。

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>本地数据网关

如果逻辑应用在多租户 Azure 中运行并需要访问 SQL Server 数据库之类的本地资源，则需在本地计算机上安装[本地数据网关](../logic-apps/logic-apps-gateway-install.md)。 然后，可以在 Azure 门户中创建数据网关资源，以便在你与资源建立连接时，逻辑应用可以使用网关。

与逻辑应用资源一样，数据网关资源与某个位置或 Azure 区域相关联。 在灾难恢复策略中，请确保数据网关一直可供逻辑应用使用。 如果有多个网关安装，可以[为网关启用高可用性](../logic-apps/logic-apps-gateway-install.md#high-availability)。

> [!NOTE]
> 如果逻辑应用在集成服务环境 (ISE) 中运行，并为本地数据源使用版本受 ISE 控制的连接器，则你不需要数据网关，因为 ISE 连接器提供对本地资源的直接访问。
>
> 如果没有任何版本受 ISE 控制的连接器可供你所需要的本地资源使用，逻辑应用仍可使用全球多租户 Azure（而不是 ISE）中运行的非 ISE 连接器创建连接。 但是，此连接需要本地数据网关。

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>主动-主动角色与主动-被动角色

可以设置主要位置和辅助位置，使这些位置中的逻辑应用实例可以充当以下角色：

| 主要-辅助角色 | 说明 |
|------------------------|-------------|
| 主动-主动 | 位于两个位置的主要和辅助逻辑应用实例按照以下任一模式来主动处理请求： <p><p>- *负载平衡*：可以让两个实例都侦听终结点，并根据需要对每个实例的流量进行负载均衡。 <p>- *竞争使用者*：可以将这两个实例作为竞争使用者，使实例可以从队列中争用消息。 如果有一个实例失败，另一个实例会接管工作负荷。 |
| 主动-被动 | 主要逻辑应用实例主动处理整个工作负荷，而辅助实例是被动性的（已禁用或处于非活动状态）。 辅助实例会等待主要实例不可用或者因中断或故障而无法正常工作的信号出现，然后以主动实例的角色接管工作负荷。 |
| 组合 | 有些逻辑应用充当主动-主动角色，还有一些逻辑应用充当主动-被动角色。 |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>主动-主动设置示例

这些示例演示了主动-主动设置，其中的两个逻辑应用实例主动处理请求或消息。 其他某个系统或服务在实例之间分配请求或消息，例如，此类系统或服务的选项如下：

* “物理”负载均衡器，例如用于路由流量的硬件组件

* “软性”负载均衡器，例如 [Azure 负载均衡器](../load-balancer/load-balancer-overview.md)或 [Azure API 管理](../api-management/api-management-key-concepts.md)。 可以使用 API 管理来指定策略，以便确定如何对传入的流量进行负载均衡。 也可使用支持状态跟踪的服务，例如 [Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)。

  尽管此示例主要演示了 Azure 负载均衡器，但你可以使用最适合自己方案的需求的选项：

  ![使用负载均衡器或有状态服务的“主动-主动”设置](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* 每个逻辑应用实例充当一个使用者，让两个实例竞争来自队列的消息：

  ![使用“竞争性使用者”的“主动-主动”设置](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>主动-被动设置示例

此示例演示了主动-被动设置，其中的主要逻辑应用实例在一个位置处于活动状态，而辅助实例在另一个位置保持非活动状态。 如果主要实例遇到中断或故障，你可以让操作员运行一个脚本，以激活辅助实例来接管工作负荷。

![使用“竞争性使用者”的“主动-被动”设置](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>主动-主动和主动-被动设置的组合

此示例演示了一种组合设置，其中的主要位置包含两个主动逻辑应用实例，辅助位置包含主动-被动逻辑应用实例。 如果主要位置遇到中断或故障，辅助位置中的主动逻辑应用（已在处理部分工作负荷）可以接管整个工作负荷。

* 在主要位置，一个主动逻辑应用侦听 Azure 服务总线队列中的消息，另一个主动逻辑应用使用 Office 365 Outlook 轮询触发器检查电子邮件。

* 在辅助位置，主动逻辑应用会侦听并竞争来自同一服务总线队列的消息，通过这种方式与主要位置中的逻辑应用配合工作。 同时，被动的非活动逻辑应用处于待机等待状态，它在主要位置不可用时检查电子邮件，但它处于禁用状态，以避免重复读取电子邮件。

![使用重复触发器的“主动-被动”和“主动-被动”设置组合](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>逻辑应用状态和历史记录

当逻辑应用被触发并开始运行时，该应用的状态会存储在应用启动时所在的位置，不可传输到另一位置。 如果发生故障或中断，则会放弃任何正在进行的工作流实例。 如果设置了主要位置和辅助位置，新的工作流实例会在辅助位置开始运行。

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>减少放弃的进行中实例数

若要最大程度地减少放弃的进行中工作流实例数，可以从可实现的各种消息模式中进行选择，例如：

* [固定传送名单模式](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  此企业消息模式将业务流程拆分为较小的阶段。 对于每个阶段，需设置一个逻辑应用来处理该阶段的工作负荷。 若要相互通信，逻辑应用可以使用异步消息传送协议，例如 Azure 服务总线队列或主题。 将流程划分为较小的阶段可以减少在有故障的逻辑应用实例上受到阻滞的业务流程的数目。 有关此模式的其他常规信息，请参阅[企业集成模式 - 传送名单](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html)。

  此示例显示了一种传送名单模式，其中的每个逻辑应用代表一个阶段，使用服务总线队列与流程中的下一个逻辑应用通信。

  ![将业务流程拆分为使用 Azure 服务总线队列相互通信的逻辑应用所表示的阶段](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  如果主要和辅助逻辑应用实例在其所在位置遵循相同的传送名单模式，你可以为这些实例设置[主动-主动角色](#roles)，通过这种方式实现[竞争性使用者模式](/azure/architecture/patterns/competing-consumers)。

* [进程管理器（中介）模式](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [无超时的扫视锁定模式](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>访问触发器和运行历史记录

若要详细了解逻辑应用在过去的工作流执行情况，可以查看该应用的触发器和运行历史记录。 逻辑应用的历史记录执行历史记录存储在该逻辑应用的运行位置或区域，这意味着你无法将此历史记录迁移到其他位置。 如果主要实例故障转移到辅助实例，你只能在运行这些实例的相应位置访问每个实例的触发器和运行历史记录。 但是，可以通过将逻辑应用设置为向 Azure Log Analytics 工作区发送诊断事件，来获取有关逻辑应用历史记录的与位置无关的信息。 然后，可以查看在多个位置运行的各个逻辑应用的运行状况和历史记录。

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>触发器类型指导

在逻辑应用中使用的触发器类型决定了可以在灾难恢复策略中使用哪些选项来设置不同位置的逻辑应用。 下面是可以在逻辑应用中使用的触发器类型：

* [重复触发器](#recurrence-trigger)
* [轮询触发器](#polling-trigger)
* [请求触发器](#request-trigger)
* [Webhook 触发器](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>重复触发器

重复触发器独立于任何特定的服务或终结点，只根据指定的计划（而不能根据其他任何条件）激发，例如：

* 按固定的频率和间隔，例如每隔 10 分钟一次
* 按更高级的计划，例如，每月最后一个星期一的下午 5:00

通过重复触发器启动逻辑应用时，需要使用[主动-被动角色](#roles)设置主要和辅助逻辑应用实例。 若要降低恢复时间目标 (RTO)（表示在发生中断或灾难后恢复业务流程所需的目标持续时间），可以使用[主动-被动角色](#roles)和[被动-主动角色](#roles)的组合来设置逻辑应用实例。 在此设置中，可在不同的位置拆分计划。

例如，假设某个逻辑应用需要每隔 10 分钟运行一次。 可以设置逻辑应用和位置，以便在主要位置不可用时，辅助位置可以接管工作：

![使用重复触发器的“主动-被动”和“被动-主动”设置组合](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* 在主要位置中，为这些逻辑应用设置[主动-被动角色](#roles)：

  * 对于启用了“主动”模式的逻辑应用，将重复触发器设置为在计划的小时一开始就启动，并每隔 20 分钟重复一次，例如在上午 9:00、上午 9:20 等时间启动。

  * 对于禁用了“被动”模式的逻辑应用，将重复触发器设置为相同的计划，但在计划的小时过去 10 分钟后启动，并每隔 20 分钟重复一次，例如在上午 9:10、上午 9:30 等时间启动。

* 在辅助位置，为这些逻辑应用设置[被动-主动](#roles)角色：

  * 对于禁用了“被动”模式的逻辑应用，将重复触发器的计划设置为与主要位置中的主动逻辑应用相同，即，在计划的小时一开始就启动，并每隔 20 分钟重复一次，例如在上午 9:00、上午 9:10 等时间启动。

  * 对于启用了“主动”模式的逻辑应用，将重复触发器的计划设置为与主要位置中的被动逻辑应用相同，即，在计划的小时过去 10 分钟后启动，并每隔 20 分钟重复一次，例如在上午 9:10、上午 9:20 等时间启动。

现在，如果主要位置发生中断性事件，则可激活备用位置中的被动逻辑应用。 这样，如果发现故障会持续较长时间，则可利用此设置来限制该延迟时段内遗漏的重复周期数。

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>轮询触发器

若要定期检查是否可从特定服务或终结点提供要处理的新数据，可以让逻辑应用使用轮询触发器根据固定的重复计划反复调用该服务或终结点。 服务或终结点提供的数据可以是以下任一类型：

* 静态数据，描述始终可供读取的数据
* 不稳定数据，描述读取后不再可用的数据

为了避免反复读取相同的数据，逻辑应用需要在客户端、服务器端、服务端或系统端维护状态，记住以前读取了哪些数据。

* 处理客户端状态的逻辑应用使用可以维护状态的触发器。

  例如，从电子邮件收件箱读取新邮件的触发器必须能够记住最近读取的邮件。 这样，仅当下一封未读邮件抵达时，该触发器才启动逻辑应用。

* 处理服务器端、服务端或系统端状态的逻辑应用使用服务器端、服务端或系统端的属性值或设置。

  例如，从数据库中读取某行的基于查询的触发器要求该行包含已设置为 `FALSE` 的 `isRead` 列。 每当触发器读取某行时，逻辑应用就会通过将 `isRead` 列从 `FALSE` 更改为 `TRUE` 来更新该行。

  对于采用排队语义的服务总线队列或主题，这种服务器端方法的工作方式是类似的，其中的触发器可以在逻辑应用处理消息的同时读取和锁定消息。 当逻辑应用完成处理操作以后，触发器会从队列或主题中删除消息。

从灾难恢复的角度讲，当你设置逻辑应用的主要和辅助实例时，请确保根据逻辑应用是在客户端还是服务器端跟踪状态来考虑这些行为：

* 对于处理客户端状态的逻辑应用，请确保该逻辑应用不会多次读取同一条消息。 在任意特定时间，只能有一个位置包含主动逻辑应用实例。 在主要实例故障转移到备用位置之前，请确保备用位置中的逻辑应用实例处于非活动状态或禁用状态。

  例如，Office 365 Outlook 触发器维护客户端状态并跟踪最近读取的电子邮件的时间戳，以避免读取重复项。

* 对于处理服务器端状态的逻辑应用，可将逻辑应用实例设置为充当[主动-主动角色](#roles)，在这种情况下，这些实例以竞争性使用者的身份工作；或者将其设置为[主动-被动角色](#roles)，在这种情况下，备用实例会等待主要实例故障转移到备用位置。

  例如，从消息队列（如 Azure 服务总线队列）读取消息时会使用服务器端状态，因为排队服务会在消息中维护锁，以防其他客户端读取相同的消息。

  > [!NOTE]
  > 如果逻辑应用需要按特定顺序读取消息（例如，从服务总线队列读取），你可以使用竞争性使用者模式，但只能对服务总线会话结合使用此模式（也称为[顺序保护模式](/azure/architecture/patterns/sequential-convoy)）。 否则，必须使用主动-被动角色设置逻辑应用实例。

<a name="request-trigger"></a>

### <a name="request-trigger"></a>请求触发器

请求触发器使得逻辑应用可从其他应用、服务和系统调用，它通常用于提供以下功能：

* 为逻辑应用提供一个可由他人调用的直接 REST API

  例如，使用请求触发器启动逻辑应用，使其他逻辑应用可以使用“调用工作流 - 逻辑应用”操作来调用触发器。

* 为逻辑应用提供 [Webhook](#webhook-trigger) 或回调机制

* 可让你手动运行用户操作或例程来调用逻辑应用（例如，使用某个执行特定任务的 PowerShell 脚本）

从灾难恢复的角度讲，请求触发器是一个被动接收方，因为逻辑应用不会执行任何工作，只是等待其他某个服务或系统显式调用触发器。 作为被动终结点，可以通过以下方式设置主要和辅助实例：

* [主动-主动](#roles)：两个实例都主动处理请求或调用。 调用方或路由器在这些实例之间平衡或分配流量。

* [主动-被动](#roles)：仅主要实例处于活动状态并处理所有工作，辅助实例只有在主要实例遇到中断或故障时才工作。 调用方或路由器决定何时调用辅助实例。

可将 Azure API 管理这种建议的体系结构用作那些使用请求触发器的逻辑应用的代理。 API 管理提供[内置的跨区域复原能力，以及在多个终结点之间路由流量的功能](../api-management/api-management-howto-deploy-multi-region.md)。

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Webhook 触发器

Webhook 触发器通过向服务传递回调 URL，为逻辑应用提供订阅该服务的功能。  然后，逻辑应用可以侦听并等待特定的事件在该服务终结点上发生。 发生该事件时，服务会使用回调 URL 来调用 Webhook 触发器，然后，后者会运行逻辑应用。 启用后，Webhook 触发器会订阅此服务。 禁用后，该触发器会取消订阅此服务。

从灾难恢复的角度讲，需将使用 Webhook 触发器的主要和辅助实例设置为充当主动-被动角色，因为只能有一个实例从订阅的终结点接收事件或消息。

## <a name="assess-primary-instance-health"></a>评估主要实例的运行状况

若要使灾难恢复策略发挥作用，解决方案需要能够通过特定方式执行以下任务：

* [检查主要实例的可用性](#check-primary-availability)
* [监视主要实例的运行状况](#monitor-primary-health)
* [激活辅助实例](#activate-secondary)

本部分介绍一种可以直接使用或者作为自己设计的基础使用的解决方案。 下面是此解决方案的统括性视觉概览：

![创建监视器逻辑应用，用于监视主要位置中的运行状况检查逻辑应用](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>检查主要实例的可用性

若要确定主要实例是否可用、正在运行且能够正常工作，可以在主要实例所在的同一位置创建一个“运行状况检查”逻辑应用。 然后，可以从备用位置调用此运行状况检查应用。 如果运行状况检查应用成功做出响应，则表示该区域中的 Azure 逻辑应用服务的底层基础结构可用且在正常运行。 如果运行状况检查应用无法做出响应，则可认为该位置不再正常。

对于此任务，请创建一个执行以下任务的基本运行状况检查逻辑应用：

1. 使用请求触发器接收来自监视器应用的调用。

1. 使用“响应”操作返回包含某种状态的响应，该状态指示检查的逻辑应用是否仍可正常运行。

   > [!IMPORTANT]
   > 运行状况检查逻辑应用必须使用“响应”操作，这样应用就会以同步而不是异步方式做出响应。

1. （可选）若要进一步确定主要位置是否正常，可以考虑与此位置中的目标逻辑应用交互的任何其他服务的运行状况。 只需扩展运行状况检查逻辑应用，即可评估这些其他服务的运行状况。

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>创建监视器逻辑应用

若要监视主要实例的运行状况并调用运行状况检查逻辑应用，请在备用位置创建一个“监视器”逻辑应用。 例如，可以设置监视器逻辑应用，以便在调用运行状况检查逻辑失败时，监视器可将警报发送到运营团队，让他们调查失败以及主要实例无法响应的原因。

> [!IMPORTANT]
> 确保监视器逻辑应用所在的位置不同于主要位置。 如果主要位置中的逻辑应用服务遇到问题，监视器逻辑应用可能不会运行。

对于此任务，请在辅助位置创建一个执行以下任务的监视器逻辑应用：

1. 使用重复触发器按固定的或计划的重复周期运行。

   可将重复周期设置为某个低于恢复时间目标 (RTO) 容许级别的值。

1. 使用 HTTP 操作调用主要位置中的运行状况检查逻辑应用，例如：

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>激活辅助实例

若要自动激活辅助实例，可以创建一个逻辑应用，用于调用 [Azure 资源管理器连接器](/connectors/arm/)等管理 API，以便激活辅助位置中的相应逻辑应用。 在发生特定次数的失败后，可以扩展监视器应用来调用此激活逻辑应用。

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>收集诊断数据

可为逻辑应用运行设置日志记录，将生成的诊断数据发送到 Azure 存储、Azure 事件中心和 Azure Log Analytics 等服务处进行进一步的处理。

* 若要在 Azure Log Analytics 中使用此数据，可以通过设置逻辑应用的诊断设置并将数据发送到多个 Log Analytics 工作区，来提供主要位置和辅助位置的数据。 有关详细信息，请参阅 [设置 Azure Monitor 日志和收集 Azure 逻辑应用的诊断数据](../logic-apps/monitor-logic-apps-log-analytics.md)。

* 若要将数据发送到 Azure 存储或 Azure 事件中心，可以通过设置异地冗余，来提供主要位置和辅助位置的数据。 有关详细信息，请参阅以下文章：<p>

  * [Azure Blob 存储灾难恢复和帐户故障转移](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure 事件中心异地灾难恢复](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>后续步骤

* [Azure 复原能力概述](/azure/architecture/framework/resiliency/overview)
* [特定 Azure 服务的复原能力检查表](/azure/architecture/checklist/resiliency-per-service)
* [在 Azure 中复原时的数据管理](/azure/architecture/framework/resiliency/data-management)
* [Azure 应用程序的备份和灾难恢复](/azure/architecture/framework/resiliency/backup-and-recovery)
* [发生区域范围的服务中断后进行恢复](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Azure 服务的 Microsoft 服务级别协议 (SLA)](https://azure.microsoft.com/support/legal/sla/)
