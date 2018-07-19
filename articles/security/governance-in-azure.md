---
title: Azure 中的监管 | Microsoft Docs
description: 了解可通过纵向和横向扩展来满足应用程序或企业需求的基于云的计算服务。
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: 579e900ee6616af8fd197e501364acd8e18d3e37
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970517"
---
# <a name="governance-in-azure"></a>Azure 中的监管

Azure 提供很多安全选项以及对这些选项进行控制的功能，，让你可以满足组织部署的独特需求。

Azure 云监管指决策制定过程、计划所涉及的条件和策略、体系结构、购置、部署、操作和云计算的管理。 Azure 云监管提供集成的审核和咨询方法，方便对组织的 Azure 平台使用情况进行评审和建议。 

若要创建 Azure 云监管计划，需要深入查看现有的人员、流程和技术。 之后，可以生成便于 IT 部门持续支持业务需求的框架，同时为用户提供使用 Azure 各项功能的灵活性。

## <a name="implementation-of-policies-processes-and-standards"></a>实施策略、流程和标准 

管理已建立监督 Azure 中信息安全策略实施情况和操作连续性的角色和责任。 Azure 管理负责监督其各自团队（包括第三方）的安全性和持续性做法， 并促使符合安全策略、流程和标准。

### <a name="account-provisioning"></a>帐户预配

定义帐户层次结构是在公司中使用和构造 Azure 服务的主要步骤。 它是核心监管结构。 具备企业协议 (EA) 的客户可将环境细分为部门、帐户和订阅。

![帐户预配](./media/governance-in-azure/security-governance-in-azure-fig1.png)

如果没有企业协议，请考虑在订阅级别使用 [Azure 标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)定义层次结构。 Azure 订阅是包含所有资源的基本单位。 它还定义 Azure 中的多种限制，例如核心数和资源数。 订阅可以包含[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)，后者又可包含资源。 [基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) 原则应用于这三个级别。

每个企业都不同。 对于非企业公司，使用 Azure 标记的层次结构允许灵活地组织 Azure。 在 Azure 中部署资源之前，应模拟层次结构，并了解对计费、资源访问和复杂性产生的影响。

### <a name="subscription-controls"></a>订阅控制

订阅确定如何对资源使用状况进行报告和计费。 可以将订阅设置为单独计费和付款。 一个 Azure 帐户可以有多个订阅。 订阅可用于确定公司中多个部门的 Azure 资源使用情况。

例如，一家公司有 IT 部门、HR 部门和营销部门，这些部门运行着不同的项目。 公司可以根据每个部门的 Azure 资源（例如虚拟机）使用状况，分别进行计费。 这样，公司就可以控制每个部门的财务状况。

Azure 订阅建立三个参数：

- 唯一的订阅者 ID

- 计费位置

- 可用资源集

对于个人，这些参数包括一个 Microsoft 帐户 ID、信用卡卡号和一整套 Azure 服务。 Microsoft 根据订阅类型强制实施使用限制。

Azure 注册层次结构定义企业协议内的服务构成方式。 企业协议门户允许客户基于可根据组织需求定制的灵活层次结构，来划分对企业协议所关联的 Azure 资源的访问权限。 层次结构模式应符合组织的管理结构和地理结构，才能对相关计费和资源访问进行准确说明。

三种高级层次结构模式分别为功能模式、业务单元模式和地理模式。 部门是帐户分组的行政构造。 可以在每个部门内为帐户分配订阅，从而在 Azure 中创建计费接收器和一些关键限制（例如，VM 和存储帐户的数量）。

![订阅控制](./media/governance-in-azure/security-governance-in-azure-fig2.png)


对于具有企业协议的组织，Azure 订阅遵循四级层次结构：

1. 企业注册管理员

2. 部门管理员

3. 帐户所有者

4. 服务管理员

此层次结构管理以下内容：

- 计费关系。

- 帐户管理。

- 通过 RBAC 对资源的访问。

- 边界：

  - 使用情况和计费（基于产品/服务数量的费率卡）

  - 限制

  - 虚拟网络

- Azure Active Directory (Azure AD) 的附属物。 一个 Azure AD 实例可以与多个订阅关联。

- 与企业注册帐户的关联。

### <a name="role-based-access-control"></a>基于角色的访问控制

[RBAC](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) 可用于对 Azure 中的资源进行细致的访问管理。 使用 RBAC，可以仅授予用户执行其作业所需的访问次数。 公司应侧重于向员工提供他们所需的确切权限。 权限过多，攻击者可轻松威胁帐户。 权限太少，员工无法有效完成工作。 

而不是向每个人提供对 Azure 订阅或资源的无限权限，可以仅允许某些操作。 例如，可以使用 RBAC 允许一个员工管理订阅中的虚拟机，而允许另一个员工管理同一订阅中的 SQL 数据库。

若要授予访问权限，可向特定范围内的用户、组或应用程序分配角色。 角色分配的范围可以是订阅、资源组或单个资源。 分配在父范围内的角色也会将访问权限授予给其中所含的子范围。 例如，对某资源组具有访问权限的用户可以管理其包含的所有资源，如网站、虚拟机和子网。 在每个订阅中，最多可以创建 2,000 个角色分配。

角色是一个权限集合，RBAC 具有若干个内置角色。 以下内置角色适用于所有资源类型：

- “所有者”拥有对所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。

- 参与者可以创建和管理所有类型的 Azure 资源，但不能将访问权限授予其他用户。

- “读取者”可以查看所有 Azure 资源。

剩余的 Azure 内置角色允许管理特定的 Azure 资源。 例如，虚拟机参与者角色允许用户创建和管理虚拟机。 有关所有内置角色及其操作的列表，请参阅 [RBAC 内置角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

RBAC 支持 Azure 门户和 Azure 资源管理器 API 中的 Azure 资源的管理操作。 在大多数情况下，RBAC 不能授权 Azure 资源的数据级别操作。 有关如何将 RBAC 扩展到数据操作的信息，请参阅[了解角色定义](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)。

如果内置角色不能满足特定访问需要，则可[创建自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。 与内置角色一样，可以将自定义角色分配到订阅、资源组和资源范围内的用户、组和应用程序。 可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell)、[Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) 和 [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest) 创建自定义角色。

### <a name="resource-management"></a>资源管理

Azure 提供两种部署模型：[经典模型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)和 Azure 资源管理器模型。

在经典模型中，每个资源彼此独立。 无法将相关的资源组合在一起。 你必须手动跟踪构成解决方案或应用程序的资源，并记得以协调的方式进行管理。 管理的基本单位是订阅。 订阅中的资源很难分解，导致需要创建大量的订阅。

资源管理器部署模型引入了[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)的概念。 资源组是一种容器，专门容纳共享同一生命周期的资源。 它可以包含解决方案的所有资源，也可以只包含需要以组的形式进行管理的资源。 根据对组织有利的原则，决定如何将资源分配到资源组。

Resource Manager 部署模型具有几大优点：

- 用户可以采用群组形式部署、管理和监视解决方案的所有服务，无需单独处理。

- 可以在解决方案的整个生命周期内重复部署该解决方案，确保以一致的状态部署资源。

- 可以将访问控制应用于资源组中的所有资源。 向资源组添加新资源时，会自动应用这些策略。

- 可以将标记应用到资源，以逻辑方式组织订阅中的所有资源。

- 可以使用 JavaScript 对象表示法 (JSON) 来定义解决方案的基础结构。 JSON 文件称为 Resource Manager 模板。

- 可以定义各资源之间的依赖关系，使其按正确的顺序进行部署。

![资源管理器](./media/governance-in-azure/security-governance-in-azure-fig4.png)

有关模板的建议，请参阅[创建 Azure 资源管理器模板的最佳实践](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)。

Azure 资源管理器会分析依赖关系，帮助确保按正确的顺序创建资源。 如果一个资源依赖于另一个资源中的值（例如虚拟机需要存储帐户才能访问磁盘），请在模板中[设置依赖关系](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies)。

还可以使用模板对基础结构进行更新。 例如，可以将新的资源添加到应用程序，并为已部署的资源添加配置规则。 如果模板指定要创建资源，但该资源已存在，则资源管理器将执行更新而不是创建新资产。 资源管理器会将现有资产更新为与新资产相同的状态。

当你需要更多操作（例如，安装未包含在安装程序中的软件）时，资源管理器可提供所需的扩展。

### <a name="resource-tracking"></a>资源跟踪

随着组织中的用户不断地在订阅中添加资源，将资源与相应的部门、客户和环境相关联就变得越发重要。 可以通过[标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)将元数据附加到资源。 可以使用标记提供有关资源或所有者的信息。 利用标记，不仅可以通过多种方式对资源进行聚合和分组，还能将该数据用于退款目的。

如果有一系列复杂的资源组和资源，并想要以最有利的方式可视化这些资产，则可以使用标记。 例如，可以标记组织中充当类似角色或者属于同一部门的资源。

如果不使用标记，组织中的用户可以创建多个资源，这可能会使将来的标识和管理变得十分困难。 例如，你可能想要删除某个项目的所有资源。 如未对项目的这些资源使用标记，则需要手动查找它们。 标记是降低不必要的订阅成本的重要方法。

资源无需驻留在同一个资源组就能共享一个标记。 你可以创建自己的标记分类，以确保组织中的所有用户使用公用的标记，避免无意中应用稍有不同的标记（如“dept”而不是“department”）。

通过资源策略，可为组织创建标准规则。 可创建相应策略，确保使用适当的值标记资源。

也可通过 Azure 门户查看标记的资源。 订阅的[使用情况报告](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)包括标记名称和值，可用于按标记对成本进行细分。

有关标记的详细信息，请参阅[计费标记策略计划](../azure-policy/scripts/billing-tags-policy-init.md)。

以下限制适用于标记：

- 每个资源或资源组最多可以有 15 个标记键值对。 此限制仅适用于直接应用到资源组或资源的标记。 资源组可以包含多个资源，这些资源每个都有 15 个标记键值对。

- 标记名称仅限 512 个字符。

- 标记值仅限 256 个字符。

- 应用于资源组的标记不会被该资源组中的资源继承。

如果有超过 15 个需要与资源关联的值，请将 JSON 字符串用于标记值。 JSON 字符串可以包含多个应用于单个标记键的值。

#### <a name="tags-for-billing"></a>计费标记

使用标记可对计费数据进行分组。 例如，如果针对不同组织运行多个 VM，可以使用标记根据成本中心对使用情况进行分组。 还可使用标记根据运行时环境对成本进行分类；例如，在生产环境中运行的虚拟机的计费使用情况。

可以通过 [Azure 资源使用情况与费率卡 API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) 或者使用情况逗号分隔值 (CSV) 文件检索有关标记的信息。 可以从 [Azure 帐户门户](https://account.windowsazure.com/)或 [EA 门户](https://ea.azure.com/)下载使用情况文件。

有关以编程方式访问计费信息的详细信息，请参阅[深入了解 Microsoft Azure 资源消耗](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview)。 有关 REST API 操作，请参阅 [Azure 计费 REST API 参考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)。

在为支持标记和计费的服务下载使用情况 CSV 时，标记将显示在“标记”列中。

### <a name="critical-resource-controls"></a>关键资源控制

随着组织不断在订阅中添加核心服务，确保这些服务随时可用，避免业务中断已变得越来越重要。 使用[资源锁](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)可以限制针对高价值资源（修改或删除这些资源会给应用程序或云基础结构造成重大影响）的操作。 可以将锁应用于订阅、资源组或资源。 通常，我们会将锁应用于虚拟网络、网关和存储帐户等基础资源。

资源锁目前支持两个值：“CanNotDelete”和“ReadOnly”。 “CanNotDelete”表示用户（具有相应权限）仍可读取或修改某个资源，但无法删除该资源。 “ReadOnly”表示经过授权的用户无法删除或修改某个资源。

资源锁仅应用于管理平面内发生的操作，包括发送到 <https://management.azure.com> 的操作。 这类锁不会限制资源如何执行各自的函数。 资源更改将受到限制，但资源操作不受限制。 例如，SQL 数据库上的“ReadOnly”锁会阻止删除或修改该数据库，但不会阻止创建、更新或删除该数据库中的数据。

应用“ReadOnly”可能会导致意外结果，因为看似读取操作的某些操作实际上需要其他操作。 例如，在存储帐户上放置 **ReadOnly** 锁将阻止所有用户列出密钥。 列出密钥操作通过 POST 请求进行处理，因为返回的密钥可用于写入操作。

![关键资源控制](./media/governance-in-azure/security-governance-in-azure-fig5.png)

另举一例，在 Azure 应用服务资源上放置“ReadOnly”锁将阻止 Visual Studio 服务器资源管理器显示资源文件，因为该交互需要写权限。

与基于角色的访问控制不同，可以使用管理锁来对所有用户和角色应用限制。 若要了解如何设置权限，请参阅[使用 RBAC 和 Azure 门户管理访问权限](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)。

在父范围应用锁时，该范围内所有资源都将继承相同的锁。 即使是之后添加的资源也会从父范围继承该锁。 继承中限制性最强的锁优先执行。

若要创建或删除管理锁，必须有权访问 Microsoft.Authorization/ 或 Microsoft.Authorization/locks/ 操作。 在内置角色中，只有“所有者”和“用户访问管理员”有权执行这些操作。

### <a name="api-access-to-billing-information"></a>API 对计费信息的访问

使用 Azure 计费 API 将用量和资源数据提取到偏好的数据分析工具。 Azure 资源用量和 RateCard API 可以帮助你准确预测及管理成本。 这些 API 作为资源提供程序实现，属于 Azure 资源管理器公开的 API 系列。

#### <a name="resource-usage-api-preview"></a>资源使用状况 API（预览版）

使用 Azure [资源用量 API](https://msdn.microsoft.com/library/azure/mt219003) 获取预估的 Azure 耗用数据量。 该 API 包括：

- **RBAC**：在 [Azure 门户](https://portal.azure.com/)上或通过 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview) 配置访问策略，指定哪些用户或应用程序有权访问订阅的使用状况数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到计费读取者、读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。

- **每小时或每日聚合**：调用方可以指定 Azure 使用状况数据是以每小时增量为单位，还是以每日增量为单位。 默认值为每日聚合。

- **实例元数据（包括资源标记）**：获取实例级详细信息，例如完全限定的资源 URI (/subscriptions/{subscription-id} /..)，以及资源组信息和资源标记。 对于交叉收费等用例，此元数据可帮助你以编程方式按标记明确分配使用状况。

- **资源元数据**：资源详细信息（如测定仪名称、测定仪类别、测定仪子类别、计价单位和区域）可让调用方更好地了解所消耗的资源。 我们还致力于在跨 Azure 门户、Azure 使用状况 CSV、EA 计费 CSV 和其他面向公众的体验的情况下保持资源元数据术语的一致性，帮助你跨不同体验关联数据。

- **所有产品类型的使用状况**：提供所有产品类型的使用状况数据，包括即用即付、MSDN、货币承诺、货币额度和 EA。

#### <a name="resource-ratecard-api"></a>资源 RateCard API

使用 Azure 资源 RateCard API 获取可用 Azure 资源的列表，以及每个资源的估计定价信息。 该 API 包括：

- **RBAC**：在 Azure 门户上或通过 Azure PowerShell cmdlet 配置访问策略，指定哪些用户或应用程序有权访问 RateCard 数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到读取者、所有者或参与者角色，以访问特定 Azure 订阅的使用状况数据。

- **支持即用即付、MSDN、货币承诺和货币额度产品（不支持 EA）**：此 API 提供 Azure 产品级费率信息。 此 API 的调用方必须传入产品/服务信息，才能获取资源详细信息和费率。 由于 EA 产品按注册自定义费率，因此目前不支持 EA。 

#### <a name="scenarios"></a>方案

使用状况 API 和 RateCard API 的组合可以实现以下方案：

- **了解 Azure 月支出**：将使用状况 API 与 RateCard API 结合使用可以深入了解云的月支出。 可以分析每小时和每日使用状况和估计费用。

- **设置警报**：可将使用状况 API 与 RateCard API 结合使用，估计云消耗量和费用，从而设置基于资源或资金的警报。

- **预测帐单**：可以估计消耗量和云支出，应用机器学习算法来预测计费周期结束时的帐单。

- **执行预先消耗费用分析**：如果要将工作负荷转移到 Azure，可以使用 RateCard API 来预测帐单（通过提供的所需用量数据）。 如果在其他云或私有云中拥有现有的工作负荷，则还可以将用量与 Azure 费率进行映射，以便更好地估计 Azure 支出。 通过这种估计可以根据产品生成透视图，在不同的产品类型（不仅仅局限于即用即付，还包括货币承诺和货币额度）之间进行比较。

- **执行模拟分析**：可以确定在其他区域或 Azure 资源的其他配置上运行工作负荷是否会更具成本效益。 Azure 资源费用根据所用的 Azure 区域而异。 还可以确定其他 Azure 套餐类型是否提供更优惠的 Azure 资源费率。

### <a name="networking-controls"></a>网络控制

对资源的访问可能是从内部（在企业网络中）或外部（通过 Internet）发起的。 组织中的用户经常会无意中将资源放在错误的位置，使其遭到恶意访问。 与对待本地设备一样，企业必须增设相应的控制机制，确保 Azure 用户做出正确的决策。

![网络控制](./media/governance-in-azure/security-governance-in-azure-fig6.png)

为了进行订阅监管，以下核心资源提供基本的访问控制。

#### <a name="network-connectivity"></a>网络连接

[虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)是子网的容器对象。 尽管严格意义上没有必要，但将应用程序连接到内部企业资源时往往要用到虚拟网络。 利用 Azure 虚拟网络服务，可以安全地将 Azure 资源通过虚拟网络相互连接。

虚拟网络是你自己的网络在云中的表示形式。 虚拟网络是对专用于订阅的 Azure 云进行的逻辑隔离。 也可将虚拟网络连接到本地网络。

以下是 Azure 虚拟网络的功能：

- **隔离**：虚拟网络之间彼此隔离。 可以为使用相同 CIDR 地址块的开发、测试和生产创建单独的虚拟网络。 相反地，也可以创建使用不同 CIDR 地址块的多个虚拟网络并将网络连接在一起。 可将一个虚拟网络分为多个子网。 Azure 为连接到虚拟网络的 VM 和 Azure 云服务角色实例提供内部名称解析。 可选择配置虚拟网络来使用自己的 DNS 服务器，而不使用 Azure 内部名称解析。

- **Internet 连接**：默认情况下，连接到虚拟网络 的所有 Azure 虚拟机和云服务角色实例都具有 Internet 访问权限。 根据需要，还可对特定资源启用入站访问。

- **Azure 资源连接**：可以将云服务和 VM 等 Azure 资源连接到同一虚拟网络。 即使资源在不同的子网中，也可通过专用 IP 地址连接彼此。 Azure 提供子网、虚拟网络和本地网络之间的默认路由，因此无需配置和管理路由。

- **虚拟网络连接**：可以将虚拟网络互相连接。 之后，连接到任何虚拟网络的资源可与任何其他虚拟网络上的任何资源进行通信。

- **本地连接**：可通过网络和 Azure 间的专用网络连接或基于 Internet 的站点到站点虚拟专用网 (VPN) 连接将虚拟网络连接到本地网络。

- **流量筛选**：可以按源 IP 地址和端口、目标 IP 地址和端口以及协议对虚拟机和云服务的网络流量（入站和出站）进行筛选。

- **路由**：可选择通过配置自己的路由或通过网关使用 BGP 路由来替代 Azure 默认路由。

#### <a name="network-access-controls"></a>网络访问控件

[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) 类似于防火墙，为资源如何通过网络“通信”提供规则。 它们针对子网（或虚拟机）如何能够连接到 Internet 或同一虚拟网络中的其他子网提供控制。

网络安全组包含一系列安全规则，这些规则可以允许或拒绝流向连接到 Azure 虚拟网络的资源的网络流量。 可以将 NSG 与子网、单个 VM（经典模型）或附加到 VM 的单个网络接口 (NIC)（资源管理器模型）进行关联。

将 NSG 与子网关联时，规则适用于连接到该子网的所有资源。 可通过将 NSG 关联到 VM 或 NIC 来进一步限制流量。

## <a name="security-and-compliance-with-organizational-standards"></a>安全性和组织标准符合性

每个企业都有不同的需求，每个企业将从云解决方案获得不同的好处。 尽管如此，所有类型的客户迁移到云存在着同样的基本问题。 客户对云提供商的要求是：

- **保护数据安全**：IT 负责人认识到云可以提供增强的数据安全性和管理控制。 但他们还是担心相比他们目前的内部解决方案，迁移到云会使他们更容易受到黑客的攻击。

- **保持数据的私密性**：云服务带来了独特的隐私挑战。 公司指靠云来缩减基础结构成本并提高灵活性的同时，他们同样担心对数据的存储位置、访问人员和使用方式失去控制。

- **保证控制权**：即使在公司利用云部署更多创新解决方案时，他们也非常担心对数据失去控制。 近期披露政府机构通过合法和法外方式访问客户数据，这使某些 CIO 对在云中存储数据更为谨慎。

- **提升透明度**：企业决策者了解安全、隐私和控制的重要性。 但他们也希望能够独立验证数据的存储、访问和保护方式。

- **保持符合性**：随着公司扩展云技术应用，标准和规则的复杂性和范围不断演化。 公司要求其符合性标准得到满足。

## <a name="security-configuration-for-monitoring-logging-and-auditing"></a>用于监视、日志记录和审核的安全性配置

Azure 订阅者可从多种设备管理其云环境。 这些设备可能包括管理工作站、开发人员电脑，甚至是具有任务特定权限的特权最终用户设备。 

在某些情况下，可通过基于 Web 的控制台（例如 Azure 门户）来执行管理功能。 在其他情况下，可以从本地系统通过 VPN、终端服务、客户端应用程序协议或 Azure 服务管理 API (SMAPI)（以编程方式）直接连接到 Azure。 此外，客户端终结点（例如平板电脑或智能手机）可以加入域或者受到隔离且不受管理。

这种可变性会给云部署带来很大的风险。 可能会难以管理、跟踪和审核管理操作。 由于对用于管理云服务的客户端终结点的访问不受管制，这种可变性还可能带来安全威胁。 使用普通工作站或专用工作站开发和管理基础结构会打开诸如 Web 浏览（例如水坑攻击）或电子邮件（例如社交工程和网络钓鱼）等不可预测的威胁媒介。

监视、日志记录和审核可为跟踪和了解管理活动提供基础。 但受限于所生成的数据量，并不一定总能巨细无遗地审核所有操作。 但是，审核管理策略的效果是一种最佳做法。

Azure Active Directory Domain Services (AD DS) GPO 的 Azure 安全监管可帮助控制所有管理员的 Windows 接口，例如文件共享。 将管理工作站纳入监视、日志记录和审核流程中。 跟踪所有管理员和开发人员的访问和使用活动。

### <a name="azure-security-center"></a>Azure 安全中心

在 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)可集中查看订阅资源的安全状态。 它提供有助于防止资源泄密的建议。 它支持更详细的策略，例如，向特定的资源组应用策略，使企业能够根据面临的风险调整立场。

![Azure 安全中心](./media/governance-in-azure/security-governance-in-azure-fig7.png)

安全中心提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。 为订阅的资源启用[安全策略](https://docs.microsoft.com/azure/security-center/security-center-policies)以后，安全中心将分析相关资源的安全性，确定可能的漏洞。 可立即提供有关网络配置的信息。

Azure 安全中心结合了 Azure 订阅中所有资源的最佳做法分析和安全策略管理。 它使安全团队和风险官能够防止、检测和应对安全威胁，因为它会自动收集和分析来自 Azure 资源、网络和合作伙伴解决方案（如反恶意程序和防火墙）的安全性数据。

此外，Azure 安全中心还应用了高级分析，包括机器学习和行为分析。 它使用 Microsoft 产品和服务、Microsoft 反数字犯罪部门 (DCU)、Microsoft 安全响应中心 (MSRC) 以及外部源提供的全球威胁情报。 [安全监管](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/)可以在订阅级别广泛应用。 也可缩小到具体要求，通过策略定义应用于个别资源。

最后，Azure 安全中心基于这些策略分析资源安全运行状况，并利用此信息提供具有洞察力的仪表板和事件（例如恶意软件检测或恶意 IP 连接尝试）警报。 有关如何应用建议的详细信息，请参阅[在 Azure 安全中心实施安全建议](https://docs.microsoft.com/azure/security-center/security-center-recommendations)。

Azure 安全中心监视以下 Azure 资源：

- 虚拟机 (VM)（包括云服务）

- 虚拟网络

- SQL 数据库

- 与 Azure 订阅集成的合作伙伴解决方案，例如 VM 和[应用服务环境](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)上的 Web 应用程序防火墙

初次访问安全中心时，会在订阅中的所有虚拟机上启用数据收集。 建议保留数据收集的启用状态，但可以在安全中心策略中[禁用它](https://docs.microsoft.com/azure/security-center/security-center-faq)。

### <a name="log-analytics"></a>Log Analytics

Azure Log Analytics 软件开发和服务团队的信息安全和[监管计划](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md)支持其业务需求。 它遵循 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)和 [Microsoft 信任中心符合性](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)中所述的法律和法规。 其中还介绍了 Log Analytics 如何建立安全要求、识别安全控制、管理和监控风险。 每年，该团队都会对策略、标准、过程和指导原则进行评审。

每个 Log Analytics 开发团队成员都会接受正式的应用程序安全培训。 版本控制系统有助于保护正在开发的每个软件项目。

Microsoft 有安全性和合规性团队，负责对 Microsoft 中所有服务进行监控和评估。 该团队由信息安全专员组成，他们不会加入开发 Log Analytics 的工程部门。 安全专员有自己的管理链。 他们负责对产品和服务进行独立评估，帮助确保安全性和符合性。

Log Analytics 的核心功能由在 Azure 中运行的一组服务提供。 每个服务提供特定的管理功能，可以组合这些服务来实现不同的管理方案。

![Azure 管理服务](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

这些管理服务是在云中设计的。 它们完全托管在 Azure 中，因此不涉及本地资源的部署和管理。 其配置工作极少，只需几分钟就能启动并运行。

在数据中心内的任意 Windows 或 Linux 计算机上安装一个代理，就能将数据发送到 Log Analytics。 可在其中连同从云或本地服务收集的其他所有数据一起进行分析。 使用 Azure 备份和 Azure Site Recovery 可以利用云来实现本地资源的备份和高可用性。

![Azure 仪表板上的管理服务](./media/governance-in-azure/security-governance-in-azure-fig8.png)

云中的 Runbook 通常无法访问本地资源，但你可以在数据中心内托管 Runbook 的一台或多台计算机上安装代理。 启动 Runbook 时，可指定是要让它在云中运行还是在本地辅助角色上运行。

Microsoft 与合作伙伴提供不同的解决方案，可将其添加到 Azure 订阅，提高在 Log Analytics 方面所做投资的价值。 例如，Azure 提供[管理解决方案](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)，它们是一些预先打包的逻辑集，可以使用一个或多个管理服务实现管理方案。

![Azure 中的管理解决方案库](./media/governance-in-azure/security-governance-in-azure-fig10.png)

合作伙伴可以创建自己的解决方案来支持自己的应用程序和服务，并通过 Azure 市场或快速入门模板将它们提供给用户。

## <a name="performance-alerting-and-monitoring"></a>性能警报和监视

### <a name="alerting"></a>警报

警报是用来监视 Azure 资源指标、事件或日志的一种方法。 它们在符合指定条件时发出通知。

警报可跨不同的服务使用，包括：

- **Azure Application Insights**：启用 Web 测试和指标警报。 有关详细信息，请参阅[在 Application Insights 中设置警报](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)和[监视任何网站的可用性和响应能力](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)。

- **Log Analytics**：用于将活动日志和诊断日志路由到 Log Analytics。 它允许使用指标、日志和其他警报类型。 有关详细信息，请参阅 [Log Analytics 中的警报](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts)。

- **Azure 监视器**：基于指标值和活动日志事件启用警报。 可以使用 [Azure Monitor REST API](https://msdn.microsoft.com/library/dn931943.aspx) 来管理警报。 有关详细信息，请参阅[使用 Azure 门户、PowerShell 或命令行接口创建警报](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal)。

### <a name="monitoring"></a>监视

云应用中的性能问题可能会影响业务。 使用多个互连的组件和频繁发布版本时，性能随时可能会下降。 开发一款应用后，你的用户通常会发现其中的一些问题，而你在测试时却发现不了这样的问题。 你应该立即知道这些问题，并使用工具来诊断和解决问题。

可以使用一系列工具来监视 Azure 应用程序和服务。 这些工具的某些功能是相互重叠的。 之所以存在这种重叠，一部分原因是应用程序的开发与操作之间界限不明。

下面是主要工具：

- **Azure Monitor** 是用于监视 Azure 中运行的服务的基本工具。 它可以提供有关服务吞吐量和周边环境的基础结构级数据。 如果你要在 Azure 中管理所有应用，并且想要确定是纵向扩展资源还是横向扩展资源，Azure Monitor 可帮助你迈出第一步。

- **Application Insights** 可用于开发，并且可充当生产监视解决方案。 它的工作方式是在应用中安装一个包，因此可提供更内在的动态视图。 其数据包括依赖项的响应时间、异常跟踪、调试快照和执行配置文件。 它提供工具来分析所有这些遥测数据，既能帮你调试应用，也能帮你了解用户将它用于哪种目的。 你可以判断响应时间出现尖峰的原因是应用中出现某种问题，还是存在某种外部资源调配问题。 如果使用 Visual Studio，而应用出现错误，可以直接找到有问题的代码行，并修复错误。

- **Log Analytics** 面向需要优化性能，并针对生产环境中运行的应用程序规划维护的用户。 它从许多源收集和聚合数据，并且会出现 10 到 15 分钟的延迟。 它为 Azure、本地基础结构和第三方基于云的基础结构（例如 Amazon Web Services）提供整体 IT 管理解决方案。 它提供工具来跨不同的源分析数据，允许针对所有日志运行复杂的查询，并可以根据指定的条件主动发出警报。 你甚至可将自定义数据收集到它的中心存储库，然后查询和可视化这些数据。

- **System Center Operations Manager** 用于管理和监视大型云安装。 作为本地 Windows Server 和基于 Hyper-V 的云的管理工具，你可能已经对它有所了解，但它还能集成和管理 Azure 应用。 除此之外，它还能在现有的实时应用中安装 Application Insights。 如果某个应用出现故障，Operations Manager 会即时发出通知。


## <a name="next-steps"></a>后续步骤

- [有关创建 Azure 资源管理器模板的最佳做法](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)

- [Azure 订阅监管实施示例](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-subscription-examples)

- [Microsoft Azure 政府](https://docs.microsoft.com/azure/azure-government/)
