---
title: Azure IT 操作人员入门指南 | Microsoft Docs
description: Azure IT 操作人员入门指南
services: ''
documentationcenter: ''
author: themichaelbender-ms
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: ''
ms.topic: ''
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/12/2017
ms.author: mibender
ms.openlocfilehash: 86f11e7c2d5503a0c474a6c15501a6b872c564e3
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072328"
---
# <a name="introduction-to-cloud-computing-and-microsoft-azure"></a>云计算和 Microsoft Azure 简介

本指南介绍与部署和管理 Microsoft Azure 基础结构相关的核心概念。 对于不熟悉云计算或 Azure 本身的用户，本指南可帮助其快速了解相关概念、部署和管理的详细信息。 本指南的许多部分讨论了部署虚拟机等操作，还提供了链接，供深入了解技术详情。


## <a name="cloud-computing-overview"></a>云计算概述

云计算提供传统本地数据中心的现代化替代方案。 由公有云供应商提供和管理所有计算基础结构和基础管理软件。 这些供应商提供多种云服务。 这些云服务可能是虚拟机、Web 服务器或云托管数据库引擎。 作为云提供商客户，用户可以根据需要租用这些云服务。 这样，硬件维护资金转换为运营费用。 云服务还具有以下优势：

-   快速部署大型计算环境

-   快速解除分配不再需要的系统

-   轻松部署传统的复杂系统，如负载均衡器

-   能够提供灵活的计算能力或按需缩放

-   更具成本效益的计算环境

-   通过基于 Web 的门户或编程自动化从任意位置进行访问

-   基于云的服务可满足大多数计算和应用程序需求

使用本地基础结构，可以完全控制部署的硬件和软件。 一直以来，这都会引领关注扩展的用户做出硬件采购决策。 一个示例是购买具有更多内核的服务器，满足峰值性能需求。 遗憾的是，在不需要峰值性能的时段，这种基础结构可能未得到充分利用。 使用 Azure，可以只部署所需基础结构，并随时进行缩放。 这样，可以通过部署额外的计算节点来进行扩展，满足性能需求。 与通过昂贵的硬件进行扩展相比，扩大云服务更具成本效益。

Microsoft 在全球部署了多个 Azure 数据中心，并且计划部署更多。 此外，Microsoft 正在增加中国和德国等区域的主权云。 只有最大的全球企业才能以这种方式部署数据中心，因此，使用 Azure，任何规模的企业都可轻松将其服务部署到客户身边。

对于小型企业，Azure 允许低成本的入口点，支持随计算需求的增加快速缩放。 这样就无需对基础结构进行大量前期资本投资，并可根据需要灵活地构建和重新构建系统。 云计算的应用与初创企业增长的快速缩放和快速失败模型非常契合。

有关可用 Azure 区域的详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。

### <a name="cloud-computing-is-classified-into-three-categories-saas-paas-and-iaas"></a>云计算分为三类：SaaS、PaaS 和 IaaS。

#### <a name="saas-software-as-a-service"></a>SaaS：服务型软件

SaaS 是集中托管和管理的软件。 它通常基于多租户体系结构 - 所有客户使用的单一版本的应用程序。 可以扩大到多个实例，确保在所有位置都达到最佳性能。 SaaS 软件通常通过月度订阅或年度订阅获得许可。

Microsoft Office 365 就是一款典型的 SaaS 服务。 订阅者支付月度订阅或年度订阅的费用，以服务的形式获得 Microsoft Exchange、Microsoft OneDrive 以及其余 Microsoft Office 套件。 订阅者获取的始终是最新版本，并且 Exchange 服务器由系统进行管理。 与每年安装和升级 Office 相比，这可以节省金钱和精力。

#### <a name="paas-platform-as-a-service"></a>PaaS：平台即服务

使用 PaaS，可以将应用程序部署到云服务供应商提供的环境中。 由供应商进行所有基础结构管理，因此用户可以专注于应用程序开发。

Azure 提供了多个 PaaS 计算服务，包括 Azure 应用服务和 Azure 云服务的 Web 应用功能（Web 角色和辅助角色）。 在任一情况下，开发者都可通过多种方式来部署应用程序，而无需了解支持该应用程序的任何具体细节。 开发者不必创建虚拟机 (VM)，无需使用远程桌面协议 (RDP) 登录每个虚拟机，也不必安装应用程序。 他们只需点击按钮（或者靠近它），Microsoft 提供的工具就会预配 VM，然后在其中部署并安装应用程序。

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS：服务架构

由 IaaS 云供应商运行并管理所有物理计算资源和所需软件，实现计算机虚拟化。 此服务的客户在这些托管的数据中心部署虚拟机。 虽然虚拟机位于场外数据中心，但 IaaS 使用者可以控制其配置和管理。

Azure 提供了多个 IaaS 解决方案，包括虚拟机、虚拟机规模集和相关网络基础结构。 虚拟机是最初向 Azure 迁移服务的常用选择，因为它可以启用“直接迁移”迁移模型。 可以像配置当前在数据中心运行服务的基础结构那样配置 VM，然后将软件迁移到新的 VM。 可能需要进行配置更新（例如，更新其他服务或存储的 URL），但可以按照这种方式迁移多个应用程序。

虚拟机规模集基于 Azure 虚拟机而构建，可用于轻松部署相同 VM 的群集。 虚拟机规模集还支持自动缩放，可以在需要时可以自动部署新 VM。 这让虚拟机规模集成为托管高级微服务计算群集（如 Azure Service Fabric 和 Azure 容器服务）的理想平台。

## <a name="azure-services"></a>Azure 服务

Azure 在其云计算平台中提供多种服务。 这些服务包括：

### <a name="compute-services"></a>计算服务

用于托管和运行应用程序工作负荷的服务：

-   Azure 虚拟机 - Linux 和 Windows

-   应用服务（Web 应用、移动应用、逻辑应用、API 应用和 Function App）

-   Azure Batch（用于大规模并行批量计算作业）

-   Azure Service Fabric

-   Azure 容器服务

### <a name="data-services"></a>数据服务

用于存储和管理数据的服务：

-   Azure 存储（包括 Azure Blob、队列、表和文件服务）

-   Azure SQL 数据库

-   Azure Cosmos DB

-   Microsoft Azure StorSimple

-   Azure Redis 缓存

### <a name="application-services"></a>应用程序服务

用于生成和操作应用程序的服务：

-   Azure Active Directory (Azure AD)

-   Azure 服务总线，用于连接分布式系统的

-   Azure HDInsight，用于处理大数据

-   Azure 计划程序

-   Azure 媒体服务

### <a name="network-services"></a>网络服务

在 Azure 内以及 Azure 与本地数据中心之间建立网络的服务：

-   Azure 虚拟网络

-   Azure ExpressRoute

-   Azure 提供的 DNS

-   Azure 流量管理器

-   Azure 内容分发网络

有关 Azure 服务的详细文档，请参阅 [Azure 服务文档](https://docs.microsoft.com/azure)。

## <a name="azure-key-concepts"></a>Azure 关键概念

### <a name="datacenters-and-regions"></a>数据中心和区域

Azure 是一个全球性云平台，在世界各地的许多区域都可以使用。 在 Azure 中预配服务、应用程序或 VM 时，需要选择一个区域。 所选区域代表运行应用程序的特定数据中心。 有关详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/)。

使用 Azure 的好处之一是，可以将应用程序部署到全球范围内的各种数据中心。 选择的区域可能会影响应用程序的性能。 最好选择更接近于大部分客户的区域，以减少网络请求的延迟。 也可以根据在某些国家/地区分发应用的法律要求选择区域。

### <a name="azure-portal"></a>Azure 门户

Azure 门户是一个基于 Web 的应用程序，可用于创建、管理和删除 Azure 资源及服务。 Azure 门户位于 https://portal.azure.com。 它包括一个可自定义的仪表板和用于管理 Azure 资源的工具。 还提供计费和订阅信息。 有关详细信息，请参阅 [Microsoft Azure 门户概述](https://azure.microsoft.com/documentation/articles/azure-portal-overview/)和[通过门户管理 Azure 资源](https://docs.microsoft.com/azure/azure-portal/resource-group-portal)。

### <a name="resources"></a>资源

Azure 资源是单独的计算、网络、数据或应用托管服务，并且这些服务已部署到 Azure 订阅中。 一些常见的资源包括虚拟机、存储帐户或 SQL 数据库。 Azure 服务通常由几个相关的 Azure 资源组成。 例如，Azure 虚拟机可能包括 VM、存储帐户、网络适配器和公共 IP 地址。 可单独创建、管理和删除这些资源，也可将其作为一个组进行这些操作。 本指南稍后将更详细地介绍 Azure 资源。

### <a name="resource-groups"></a>资源组

Azure 资源组是用于保存 Azure 解决方案相关资源的容器。 资源组可以包含解决方案的所有资源，也可以只包含以组的形式进行管理的资源。 本指南稍后将更详细地介绍 Azure 资源组。

### <a name="resource-manager-templates"></a>资源管理器模板

Azure 资源管理器模板是一个 JavaScript 对象表示法 (JSON) 文件，用于定义一个或多个要部署到资源组的资源。 它也会定义所部署资源之间的依赖关系。 本指南稍后将更详细地介绍资源管理器模板。

### <a name="automation"></a>自动化

除了通过使用 Azure 门户创建、管理和删除资源之外，还可以使用 PowerShell 或 Azure 命令行接口 (CLI) 自动执行这些活动。

**Azure PowerShell**

Azure PowerShell 是一组模块，提供用于管理 Azure 的 cmdlet。 可以使用这些 cmdlet 创建、管理和删除 Azure 服务。 这些 cmdlet 有助于实现可重复的一致型无人参与部署。 有关详细信息，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps)。

**Azure 命令行接口**

Azure 命令行接口是一种可用于从命令行创建、管理和删除 Azure 资源的工具。 Azure CLI 适用于 Linux、Mac OS X 和 Windows。 有关详细信息和技术详细信息，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli.md)。

**REST API** Azure 是基于一组支持 Azure 门户 UI 的 REST API 构建的。 其中大多数 REST API 还支持以编程方式在任何启用了 Internet 的设备上预配和管理 Azure 资源和应用。 有关详细信息，请参阅 [Azure REST SDK Reference](https://docs.microsoft.com/rest/api/index)（Azure REST SDK 参考）。

## <a name="azure-subscriptions"></a>Azure 订阅

订阅是链接到 Azure 帐户的 Azure 服务的逻辑分组。 一个 Azure 帐户可包含多个订阅。 Azure 服务按订阅计费。 Azure 订阅有一个帐户管理员和一个服务管理员，前者可以完全控制订阅，后者可以控制订阅中的所有服务。 除了管理员外，还可以通过 RBAC 对单个帐户授予对 Azure 资源的详细控制权限。

### <a name="select-and-enable-an-azure-subscription"></a>选择并启用 Azure 订阅

需要订阅之后才能使用 Azure 服务。 可以使用多种订阅类型。

**免费帐户**：注册免费帐户的链接位于 [Azure网站](https://azure.microsoft.com/)。 这可以提供 30 天试用期和相应信用额度，用于尝试 Azure 中的任何资源组合。 超出信用额度后，帐户将被暂停。 试用期结束时，服务将被停用，不再工作。 可随时升级到即用即付订阅。

**MSDN 订阅**：如果有 MSDN 订阅，每月可以获得特定金额的 Azure 信用额度。 例如，如果有 Microsoft Visual Studio Enterprise with MSDN 订阅，则每月可获得 \$150 美元的 Azure 信用额度。

如果超过信用额度数量，服务将被禁用，直到下个月开始。 可关闭支出限制，并添加信用卡支付额外的费用。 对于 MSDN 帐户，其中一些费用会打折。 例如，对于运行 Windows Server 的 VM，需要为 Linux 付费，但不需要为 Microsoft 服务器（如 Microsoft SQL Server）支付额外费用。 这让 MSDN 帐户非常适合开发和测试方案。

**BizSpark 帐户**：Microsoft BizSpark 程序为初创企业提供了许多好处。 其中一个好处是可以访问所有 Microsoft 软件，使用最多 5 个 MSDN 帐户来开发和测试环境。 这 5 个 MSDN 帐户都可获得 150 美元的 Azure 信用额度，使用多个 Azure 服务（如虚拟机）时，支付费率会降低。

**即用即付**：使用此订阅，可以将信用卡或借记卡附加到帐户，为使用的内容付费。 对于组织，还可以开具发票。

**企业协议**：达成企业协议，即表示承诺在接下来一年里使用一定数量的 Azure 服务，并提前支付费用。 所做的承诺在一整年内有效。 如果超出承诺金额，可以拖欠支付超额部分。 根据承诺的金额，可以享受 Azure 服务价格折扣。

### <a name="grant-administrative-access-to-an-azure-subscription"></a>授予对 Azure 订阅的管理访问权限

多个帐户管理员角色可用，并且可以随时更改。 两个主要角色是：

-   **服务管理员**：此角色有权管理 Azure 服务。 默认情况下，有权访问的帐户与帐户管理员相同。

-   **共同管理员**：此角色具有与服务管理员相同的访问权限。 但是，此角色不能更改订阅与 Azure 目录的关联。

有关详细信息，请参阅[如何添加或更改 Azure 管理员角色](../../billing/billing-add-change-azure-subscription-administrator.md)。

### <a name="view-billing-information-in-the-azure-portal"></a>在 Azure 门户中查看计费信息

使用 Azure 的一个重要元素是能够查看计费信息。 可通过 Azure 门户全面细致地了解 Azure 计费信息。

有关详细信息，请参阅[如何下载 Azure 帐单发票和每日使用数据](../../billing/billing-download-azure-invoice-daily-usage-date.md)。

### <a name="get-billing-information-from-billing-apis"></a>从计费 API 获取计费信息

除了在门户中查看计费外，还可以通过 Azure 计费 REST API 使用脚本或程序访问计费信息：

-   可以使用 Azure 使用情况 API 来检索使用情况数据。 可以通过标记相关 Azure 资源来微调计费使用情况信息。 例如，可以使用部门名称或项目名称标记资源组中的每个资源，然后专门跟踪一种标记对应的成本。

-   可以使用 Azure 价目表 API 列出所有可用资源，以及每个资源的元数据和定价信息。

有关详细信息，请参阅[深入了解 Microsoft Azure 资源消耗](../../billing/billing-usage-rate-card-overview.md)。

### <a name="forecast-cost-with-the-pricing-calculator"></a>使用价格计算器预测成本

每项 Azure 服务的定价各不相同。 许多 Azure 服务提供基本、标准和高级层。 通常，每一层都有多个价格和性能级别。 可通过使用[联机价格计算器](http://azure.microsoft.com/pricing/calculator)来创建价格估计值。 使用该计算器，可灵活地估计单个资源或一组资源的成本。

### <a name="set-up-billing-alerts"></a>设置计费警报

在 Azure 上部署应用程序或解决方案后，可以创建警报，以便在接近警报中定义的支出限制时收到电子邮件。 有关详细信息，请参阅[为 Microsoft Azure 订阅设置计费警报](../../billing/billing-set-up-alerts.md)。

## <a name="azure-resource-manager"></a>Azure 资源管理器

Azure 资源管理器是用于 Azure 资源的部署、管理和组织机制。 通过使用资源管理器，可以将多个单独的资源置于一个资源组中。

资源管理器还提供部署功能，可用于对相关资源进行自定义部署和配置。 例如，通过使用资源管理器，可将包含多个虚拟机、负载均衡器和 SQL 数据库的应用程序作为单一单元进行部署。 可以使用资源管理器模板来开发这些部署。

资源管理器提供多种优势：

-   可以以组的形式部署、管理和监视解决方案的所有资源，而不是单独处理这些资源。

-   可以在整个开发生命周期内重复部署解决方案，并确保以一致的状态部署资源。

-   可以通过声明性模板而非脚本来管理基础结构。

-   可以定义各资源之间的依赖关系，使其按正确的顺序进行部署。

-   可以将访问控制应用到资源组中的所有服务，因为 RBAC 已在本机集成到管理平台。

-   可以将标记应用到资源，以逻辑方式组织订阅中的所有资源。

-   可以通过查看一组共享相同标记的资源的成本来理清组织的帐单。

### <a name="tips-for-creating-resource-groups"></a>创建资源组的提示

制定有关资源组的决策时，请考虑以下提示：

-   资源组中的所有资源都应具有相同的生命周期。

-   一次只可将资源分配到一个组。

-   随时都可在资源组中添加或删除资源。 每个资源都必须属于资源组。 因此，如果从一个组中删除资源，必须将该资源添加到另一个组。

-   随时都可将大多数类型的资源移动到不同资源组。

-   资源组中的资源可以位于不同的区域。

-   可以使用资源组来控制其中包含的资源的访问。

### <a name="building-resource-manager-templates"></a>生成资源管理器模板

资源管理器模板以声明方式定义将要部署到单个资源组中的资源和资源配置。 可使用资源管理器模板来协调复杂的部署，而无需额外编写脚本或手动配置。 开发模板后，可以多次进行部署 - 每次都是相同的结果。

资源管理器模板包含四个部分：

-   **参数**：参数是部署的输入。 参数值可以由用户提供，也可以由自动化过程提供。 示例参数可能是 Windows VM 的管理员用户名和密码。 指定参数值后，整个部署过程都会使用这些参数值。

-   **变量**：变量用于保存整个部署过程中使用的值。 与参数不同，变量值不是在部署时提供的。 而是通过硬编码生成或动态生成的。

-   **资源**：模板的这一部分定义了要部署的资源，如虚拟机、存储帐户和虚拟网络。

-   **输出**：部署完成后，资源管理器可能会返回数据，如动态生成的连接字符串。

以下机制可用于部署自动化：

-   **函数**：可以在资源管理器模板中使用多个函数。 包括将字符串转换为小写、部署已定义资源的多个实例，以及动态返回目标资源组等操作。 资源管理器函数有助于生成动态部署。

-   **资源依赖关系**：部署多个资源时，某些资源对其他资源具有依赖关系。 为了方便部署，可以使用资源依赖关系声明，先部署依赖资源，然后再部署其他资源。

-   **模版链接**：可从一个资源管理器模板中链接到另一个模板。 这允许将部署分解成一组具有特定用途的定向模板。

可在任何文本编辑器中生成资源管理器模板。 但是，用于 Visual Studio 的 Azure SDK 包括的工具可提供帮助。 通过使用 Visual Studio，可以通过向导向模板添加资源，然后直接从 Visual Studio 中部署和调试模板。 有关详细信息，请参阅[创作 Azure 资源管理器模板](../../resource-group-authoring-templates.md)。

最后，可将现有资源组从 Azure 门户转换为可重用模板。 如果要创建现有资源组的可部署模板，或者只想检查基础 JSON，这非常有用。 若要导出资源组，请从资源组的设置中选择“自动化脚本”按钮。

## <a name="security-of-azure-resources-rbac"></a>Azure 资源的安全性 (RBAC)

可在如下指定范围内授予对用户帐户的操作访问权限：订阅、资源组或单个资源。 这意味着可以将一组资源（例如虚拟机和所有相关资源）部署到资源组中，并向特定用户或组授予权限。 此方法将访问权限限制为仅针对属于目标资源组的资源。 还可以授予对单个资源（如虚拟机或虚拟网络）的访问权限。

若要授予访问权限，可向用户或用户组分配角色。 有许多预定义的角色。 还可以定义自己的自定义角色。

以下是 Azure 中内置的一些示例角色：

-   **所有者**：具有此角色的用户可管理所有内容，包括访问权限。

-   **读者**：具有此角色的用户可以读取所有类型的资源（机密除外），但不能进行更改。

-   **虚拟机参与者**：具有此角色的用户可以管理虚拟机，但不能管理其连接的虚拟网络或 VHD 文件所在的存储帐户。

-   **SQL DB 参与者**：具有此角色的用户可以管理 SQL 数据库，而不是与安全性相关的策略。

-   **SQL 安全管理器**：具有此角色的用户可以管理 SQL 服务器和数据库与安全性相关的策略。

-   **存储帐户参与者**：具有此角色的用户可以管理存储帐户，但不能管理对存储帐户的访问权限。

有关详细信息，请参阅[使用角色分配管理对 Azure 订阅资源的访问权限](../../role-based-access-control/role-assignments-portal.md)。

## <a name="azure-virtual-machines"></a>Azure 虚拟机

Azure 虚拟机是 Azure 中的一个中心 IaaS 服务。 Azure 虚拟机支持将 Windows 或 Linux 虚拟机部署到 Microsoft Azure 数据中心。 通过 Azure 虚拟机，用户可以完全控制 VM 配置，并负责所有软件安装、配置和维护。

在部署 Azure VM 时，可从 Microsoft Azure 市场中选择一个映像，也可提供自己的通用映像。 此映像用于应用操作系统和初始配置。 在部署期间，资源管理器将处理一些配置设置，例如分配计算机名称、管理凭据和网络配置。 可以使用 Azure 虚拟机扩展来进一步自动化配置，如软件安装、防病毒配置和监视解决方案。

可以创建多个不同大小的虚拟机。 虚拟机的大小决定了如何分配资源，如处理、内存和存储容量。 在某些情况下，只有特定大小的 VM 上才可使用特定功能，如启用了 RDMA 的网络适配器和 SSD 磁盘。 有关 VM 大小和功能的完整列表，请参阅 [Azure 中 Windows 虚拟机的大小](../../virtual-machines/windows/sizes.md)和 [Azure 中 Linux 虚拟机的大小](../../virtual-machines/linux/sizes.md)。

### <a name="use-cases"></a>用例

由于 Azure 虚拟机可用于实现对配置的完全控制，因此它们适用于不适合 PaaS 模型的各种服务器工作负荷。 数据库服务器（SQL Server、Oracle 或 MongoDB）、Windows Server Active Directory、Microsoft SharePoint 等服务器工作负荷可以在 Microsoft Azure 平台上运行。 如果需要，可将这些工作负荷从本地数据中心移动到一个或多个 Azure 区域，而无需大量重新配置。

### <a name="deployment-of-virtual-machines"></a>部署虚拟机

要部署 Azure 虚拟机，可使用 Azure 门户，也可将自动化与 Azure PowerShell 模块结合使用，或将自动化与跨平台 CLI 结合使用。

**门户**

要使用 Azure 门户部署虚拟机，只需要具有一个有效的 Azure 订阅，以及对 Web 浏览器的访问权限。 可选择使用不同配置的多个不同的操作系统映像。 所有存储和网络要求都在部署期间配置。 有关详细信息，请参阅[使用 Azure 门户创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-portal.md) 和[使用 Azure 门户创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-portal.md)。

使用 Azure 门户，除了部署虚拟机之外，还可以部署 Azure 资源管理器模板。 这将部署并配置模板中定义的所有资源。 有关详细信息，请参阅[使用资源管理器模板和 Azure 门户部署资源](../../azure-resource-manager/resource-group-template-deploy-portal.md)。

**PowerShell**

使用 PowerShell 部署 Azure 虚拟机，可以完全自动化地部署所有相关虚拟机资源，包括存储和网络。 有关详细信息，请参阅[使用资源管理器和 PowerShell 创建 Windows VM](../../virtual-machines/windows/quick-create-powershell.md)。

除了单独部署 Azure 计算资源之外，还可以使用 Azure PowerShell 模块来部署 Azure 资源管理器模板。 有关详细信息，请参阅[使用资源管理器模板和 Azure PowerShell 部署资源](../../azure-resource-manager/resource-group-template-deploy.md)。

**命令行接口 (CLI)**

与 PowerShell 模块一样，Azure 命令行接口也可实现部署自动化，并且可用于 Windows、OS X 或 Linux 系统。 使用 Azure CLI“vm quick-create”命令时，将部署所有相关虚拟机资源（包括存储和网络）以及虚拟机本身。 有关详细信息，请参阅[使用 Azure CLI 创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-cli.md)。

同样，可以使用 Azure CLI 部署 Azure 资源管理器模板。 有关详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/resource-group-template-deploy-cli.md)。

### <a name="access-and-security-for-virtual-machines"></a>虚拟机的访问和安全性

从 Internet 访问虚拟机需要使用公共 IP 地址配置相关网络接口或负载均衡器（如果适用）。 公共 IP 地址包括将解析到虚拟机或负载均衡器的 DNS 名称。 有关详细信息，请参阅 [Azure 中的 IP 地址](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)。

可使用网络安全组 (NSG) 资源来管理通过公共 IP 地址访问虚拟机。 NSG 的作用类似于防火墙，允许或拒绝一组定义的端口上网络接口或子网的流量。 例如，若要使用 Azure VM 创建远程桌面会话，需要配置 NSG，允许端口 3389 上的入站流量。 有关详细信息，请参阅[在 Azure 中使用 Azure 门户打开 VM 端口](../../virtual-machines/windows/nsg-quickstart-portal.md)。

最后，与任何计算机系统的管理一样，应使用安全凭据和软件防火墙，在操作系统中保证 Azure 虚拟机的安全性。

## <a name="azure-storage"></a>Azure 存储

Azure 存储是一款 Microsoft 托管的服务，可提供持久、可缩放的冗余存储。 可通过使用任何资源部署方法将 Azure 存储帐户作为资源添加到任何资源组。 Azure 包括四种存储类型：Blob 存储、文件存储、表存储和队列存储。 部署存储帐户时，可以使用两种帐户类型，即通用存储和 Blob 存储。 通用存储帐户可用于访问所有四种存储类型。 Blob 存储帐户类似于通用帐户，但它包含专门的 blob，其中包括热访问层和冷访问层。 有关 blob 存储的更多信息，请参阅 [Azure Blob 存储](../../storage/blobs/storage-blob-storage-tiers.md)。

Azure 存储帐户可以配置不同级别的冗余：

-   **本地冗余存储**确保所有数据的三个副本同步后才将写入视为成功，提供了高可用性。 这些副本存储在单个区域的单个设施中。 副本驻留在不同的容错域和升级域中。 这意味着即使存放数据的存储节点失败或脱机更新，数据也可用。

-   **异地冗余存储**在主要区域中创建三个数据同步副本，以实现高可用性，然后在配对区域中以异步方式创建三个副本，以实现灾难恢复。

-   **读取访问异地冗余存储**是一种异地冗余存储，能够读取次要区域中的数据。 此能力使其适用于部分灾难恢复。 如果主要区域出现问题，可将应用程序更改为对配对区域具有只读访问权限。

### <a name="use-cases"></a>用例

每个存储类型具有不同的用例。

**Blob 存储**

“Blob”是二进制大型对象的缩写。 Blob 和计算机上存储的文件一样，是非结构化文件。 Blob 存储可以存储任何类型的文本或二进制数据，例如文档、媒体文件或应用程序安装程序。 Blob 存储也称为对象存储。 Azure Blob 存储还可保存 Azure 虚拟机数据磁盘。

Azure 存储支持三种 blob：

-   **块 blob** 用于保存最大 195 GB（4 MB × 50,000 个块）的普通文件。 块 blob 的主要用例是存储从头到尾读取的文件，例如网站的媒体文件或图像文件。 它们被命名为块 blob 的原因是，大于 64 MB 的文件必须作为小块上传。 这些块随后合并（或提交）到最终的 blob 中。

-   **页 blob** 用于保存最大 1 TB 的随机访问文件。 页 blob 主要用作 VHD 的后备存储，为 Azure 中的 IaaS 计算服务 Azure 虚拟机提供耐用的磁盘。 它们被命名为页 blob 的原因是，它们提供对 512 字节页的随机读/写访问。

-   **追加 blob** 由块 blob 等块组成，但针对追加操作进行了优化。 它们经常用于将一个或多个源的信息记录到同一 blob。 例如，可将所有跟踪日志记录写入在多个 VM 上运行的应用程序的同一追加 blob。 单个追加 blob 最大大小可达 195 GB。

有关详细信息，请参阅[通过 .NET 开始使用 Azure Blob 存储](../../storage/blobs/storage-dotnet-how-to-use-blobs.md)。

**文件存储**

Azure 文件存储服务通过使用标准服务器消息块 (SMB) 协议在云中提供文件共享。 该服务支持 SMB 2.1 和 SMB 3.0。 通过 Azure 文件存储，可以将依赖文件共享的应用程序快速迁移到 Azure，且无成本高昂的重写。 在 Azure 虚拟机、云服务中或者从本地客户端运行的应用程序可以在云中装载文件共享。 这与桌面应用程序装载典型 SMB 共享的方式类似。 之后，任意数量的应用程序组件可以装载并同时访问文件存储共享。

由于文件存储共享是标准 SMB 文件共享，在 Azure 中运行的应用程序可以通过文件系统 I/O API 访问共享中的数据。 因此，开发者可以使用其现有代码和技术迁移现有应用程序。 IT 专业人员在管理 Azure 应用程序的过程中，可以使用 PowerShell cmdlet 来创建、装载和管理文件存储共享。

有关详细信息，请参阅[在 Windows 上开始使用 Azure 文件存储](../../storage/files/storage-how-to-use-files-windows.md)或[如何通过 Linux 使用 Azure 文件存储](../../storage/files/storage-how-to-use-files-linux.md)。

**表存储**

Azure 表存储是一种将结构化的 NoSQL 数据存储在云中的服务。 表存储是采用无架构设计的键/属性存储。 因为表存储无架构，因此可以很容易地随着应用程序需求的发展使数据适应存储。 对于所有类型的应用程序，都可以快速并经济高效地访问数据。 对于相似的数据量，表存储的成本通常显著低于传统的 SQL。

可以使用表存储来存储灵活的数据集，例如 Web 应用程序的用户数据、通讯簿、设备信息，以及服务需要的任何其他类型的元数据。 可以在表中存储任意数量的实体。 存储帐户可以包含任意数量的表，直至达到存储帐户的容量极限。

有关详细信息，请参阅[开始使用 Azure 表存储](../../cosmos-db/table-storage-how-to-use-dotnet.md)。

**队列存储**

Azure 队列存储用于在应用程序组件之间进行云消息传送。 设计可缩放的应用程序时，应用程序组件通常是分离的，各组件可以独立缩放。 队列存储提供的异步消息传送适用于在应用程序组件之间进行通信，无论这些应用程序组件是运行在云中、桌面上、本地服务器上还是移动设备上。 队列存储还支持管理异步任务以及构建过程工作流。

有关详细信息，请参阅[开始使用 Azure 队列存储](../../storage/queues/storage-dotnet-how-to-use-queues.md)。

### <a name="deploying-a-storage-account"></a>部署存储帐户

可通过多种选项部署存储帐户。

**门户**

要使用 Azure 门户部署存储帐户，只需要具有一个有效的 Azure 订阅，以及对 Web 浏览器的访问权限。 可将新的存储帐户部署到新的或现有资源组中。 创建存储帐户后，可以使用门户创建一个 blob 容器或文件共享。 可通过编程方式创建表和队列存储实体。 有关详细信息，请参阅[创建存储帐户](../../storage/common/storage-create-storage-account.md#create-a-storage-account)。

使用 Azure 门户，除了部署存储帐户之外，还可以部署 Azure 资源管理器模板。 这将部署并配置模板中定义的所有资源，包括任何存储帐户。 有关详细信息，请参阅[使用资源管理器模板和 Azure 门户部署资源](../../azure-resource-manager/resource-group-template-deploy-portal.md)。

**PowerShell**

使用 PowerShell 部署 Azure 存储帐户，可以完全自动化地部署存储帐户。 有关详细信息，请参阅[对 Azure 存储使用 Azure PowerShell](../../storage/common/storage-powershell-guide-full.md)。

除了单独部署 Azure 资源之外，还可以使用 Azure PowerShell 模块来部署 Azure 资源管理器模板。 有关详细信息，请参阅[使用资源管理器模板和 Azure PowerShell 部署资源](../../azure-resource-manager/resource-group-template-deploy.md)。

**命令行接口 (CLI)**

与 PowerShell 模块一样，Azure 命令行接口也可实现部署自动化，并且可用于 Windows、OS X 或 Linux 系统。 可使用 Azure CLI“storage account create”命令创建存储帐户。 有关详细信息，请参阅[将 Azure CLI 用于 Azure 存储。](../../storage/common/storage-azure-cli.md)

同样，可以使用 Azure CLI 部署 Azure 资源管理器模板。 有关详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../../resource-group-template-deploy-cli.md)。

### <a name="access-and-security-for-azure-storage"></a>Azure 存储的访问和安全性

可通过多种方式访问 Azure 存储，包括通过 Azure 门户进行访问、在 VM 创建和操作期间访问，以及从存储客户端库访问。

**虚拟机磁盘**

部署虚拟机时，还需要创建一个存储帐户来保存虚拟机操作系统磁盘和任何其他数据磁盘。 可选择现有存储帐户，也可以创建一个新存储帐户。 由于 blob 的最大大小为 1,024 GB，因此单个 VM 磁盘的最大大小为 1,023 GB。 若要配置较大的数据磁盘，可以将多个数据磁盘提供给虚拟机，并将它们汇集在一起，组成一个逻辑磁盘。 有关详细信息，请参阅 [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) 和 [Linux](../../virtual-machines/linux/tutorial-manage-disks.md) 的“管理 Azure 磁盘”。

**存储工具**

可以通过多种不同的存储资源管理器访问 Azure 存储帐户，如 Visual Studio Cloud Explorer。 这些工具可用于浏览存储帐户和数据。 有关详细信息和可用存储资源管理器的列表，请参阅 [ Azure 存储客户端工具](../../storage/common/storage-explorers.md)。

**存储 API**

可以通过任何发出 HTTP/HTTPS 请求的语言来访问存储资源。 另外，Azure 存储还为多种主流语言提供了编程库。 这些库通过对细节进行处理简化了 Azure 存储的使用，这些细节包括同步和异步调用、操作的批处理、异常管理和自动重试。 有关详细信息，请参阅 [Azure 存储服务 REST API 参考](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference)。

**存储访问密钥**

每个存储帐户都有两个身份验证密钥，一个是主要密钥，一个是辅助密钥。 每个密钥都可用于存储访问操作。 这些存储密钥用于帮助保护存储帐户，并且需要具有这些密钥才可以编程方式访问数据。 两个密钥允许偶尔变换密钥以增强安全性。 确保密钥安全至关重要，因为使用密钥和帐户名即可对存储帐户中的任何数据进行无限制的访问。

**共享访问签名**

如果需要允许用户具有对存储资源的受控访问权限，可以创建一个共享访问签名。 共享访问签名是一个可以追加到 URL 的令牌，可实现对存储资源的委托访问。 持有令牌的任何人都可以在令牌有效期间使用它指定的权限访问它指向的资源。 有关详细信息，请参阅[使用共享访问签名](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

## <a name="azure-virtual-network"></a>Azure 虚拟网络


需要通过虚拟网络来支持虚拟机之间的通信。 可以定义子网、自定义 IP 地址、DNS 设置、安全筛选以及负载均衡。 通过使用 VPN 网关或 ExpressRoute 线路，可将 Azure 虚拟网络连接到本地网络。

### <a name="use-cases"></a>用例

下面是 Azure 网络的不同用例。

**仅限云的虚拟网络**

默认情况下，Azure 虚拟网络只能访问存储在 Azure 中的资源。 连接到同一虚拟网络的资源可以相互通信。 可以将虚拟机网络接口和负载均衡器与公共 IP 地址关联，以便能够通过 Internet 访问虚拟机。 使用网络安全组有助于保护对公开资源的访问。

**跨界虚拟网络**

通过使用 ExpressRoute 或站点到站点 VPN 连接，可将本地网络连接到 Azure 虚拟网络。 在此配置中，Azure 虚拟网络实质上是本地网络基于云的扩展。

由于 Azure 虚拟网络连接到本地网络，因此跨界虚拟网络必须使用组织所用地址空间的一部分，并且该部分必须是唯一的。 与不同公司位置获得一个特定 IP 子网相同，Azure 会随着网络的扩展成为另一个位置。

### <a name="deploying-a-virtual-network"></a>部署虚拟网络

可通过多种选项部署虚拟网络。

**门户**

要使用 Azure 门户部署 Azure 虚拟网络，只需要具有一个有效的 Azure 订阅，以及对 Web 浏览器的访问权限。 可将新的虚拟网络部署到新的或现有资源组中。 从门户创建新的虚拟机时，可选择现有虚拟网络或创建一个新的虚拟网络。 有关详细信息，请参阅[使用 Azure 门户创建虚拟网络](../../virtual-network/quick-create-portal.md)。

使用 Azure 门户，除了部署 Azure 虚拟网络之外，还可以部署 Azure 资源管理器模板。 这将部署并配置模板中定义的所有资源，包括任何虚拟网络资源。 有关详细信息，请参阅[使用资源管理器模板和 Azure 门户部署资源](../../azure-resource-manager/resource-group-template-deploy-portal.md)。

**PowerShell**

使用 PowerShell 部署 Azure 虚拟网络，可以完全自动化地部署存储帐户。 有关详细信息，请参阅[使用 PowerShell 创建虚拟网络](../../virtual-network/quick-create-powershell.md)。

除了单独部署 Azure 资源之外，还可以使用 Azure PowerShell 模块来部署 Azure 资源管理器模板。 有关详细信息，请参阅[使用资源管理器模板和 Azure PowerShell 部署资源](../../azure-resource-manager/resource-group-template-deploy.md)。

**命令行接口 (CLI)**

与 PowerShell 模块一样，Azure 命令行接口也可实现部署自动化，并且可用于 Windows、OS X 或 Linux 系统。 可使用 Azure CLI“network vnet create”命令创建虚拟网络。 有关详细信息，请参阅[使用 Azure CLI 创建虚拟网络](../../virtual-network/quick-create-cli.md)。

同样，可以使用 Azure CLI 部署 Azure 资源管理器模板。 有关详细信息，请参阅[使用资源管理器模板和 Azure CLI 部署资源](../../azure-resource-manager/resource-group-template-deploy-cli.md)。

### <a name="access-and-security-for-virtual-networks"></a>虚拟网络的访问和安全性

可使用网络安全组来保护 Azure 虚拟网络。 NSG 包含一系列访问控制列表 (ACL) 规则，这些规则可以允许或拒绝虚拟网络中流向 VM 实例的网络流量。 可将 NSG 与任一子网或该子网内的单个 VM 实例关联。 将 NSG 与子网关联时，ACL 规则将应用到该子网中的所有 VM 实例。 另外，可通过将 NSG 与单个 VM 直接关联，对流向该 VM 的流量进行进一步的限制。 有关详细信息，请参阅[使用网络安全组筛选网络流量](../../virtual-network/security-overview.md)。

## <a name="next-steps"></a>后续步骤

- [创建 Windows VM](/virtual-machines/windows/quick-create-portal.md)
- [创建 Linux VM](../../virtual-machines/linux/quick-create-portal.md)
