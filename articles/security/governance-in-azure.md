---
title: "Azure 中的监管 | Microsoft Docs"
description: "了解基于云的计算服务，包括大量计算实例和服务，它们可根据应用程序或企业的需求自动增加或减少。"
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: c63eb15e8d575da91fd8198ca9d486f7fdb2b38d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2017
---
# <a name="governance-in-azure"></a>Azure 中的监管

我们知道，安全是云中的首要任务，及时找到有关 Azure 安全性的准确信息极其重要。 将 Azure 用于应用程序和服务的最合理原因之一是可以利用其各种安全工具和功能。 这些工具和功能可帮助在安全的 Azure 平台上创建安全的解决方案。

为帮助你从客户和 Microsoft 操作两个角度更好地了解在 Microsoft Azure 中实施的一系列监管控制，特编写《Azure 中的监管》一文，助你全面了解 Microsoft Azure 中的监管功能。

## <a name="azure-platform"></a>Azure 平台

Azure 是一个公有云服务平台，支持多种操作系统、编程语言、框架、工具、数据库和设备。 它可通过 Docker 集成运行 Linux 容器，使用 JavaScript、Python、.NET、PHP、Java 和 Node.js 生成应用，以及生成适用于 iOS、Android 和 Windows 设备的后端。 Azure 公有云服务支持数百万开发人员和 IT 专业人士已经有所依赖并信任的相同技术。

构建 IT 资产或将其迁移到公有云服务提供商处时，需要借助该组织的能力来保护应用程序和数据，并使用该组织提供的服务和控制机制来管理基于云的资产的安全性。

Azure 的基础结构（从设备到应用程序）经过设计，可同时托管数百万的客户，并为企业提供可靠的基础，使之能够满足其安全要求。 此外，Azure 还提供很多安全选项以及对这些选项进行控制的功能，方便你自定义安全措施来满足组织部署的独特要求。

本文档有助于了解 Azure 监管功能如何帮助满足这些要求。

## <a name="abstract"></a>摘要

Microsoft Azure 云监管提供集成的审核和咨询方法，方便对组织的 Azure 平台使用情况进行评审和建议。 Microsoft Azure 云监管指决策制定过程、计划所涉及的条件和策略、体系结构、采集、部署、操作和云计算的管理。

若要创建 Microsoft Azure 云监管计划，需要深入查看现有的人员、进程和技术，然后生成便于 IT 部门持续支持业务需求的框架，同时为最终用户提供使用 Microsoft Azure 强大功能的灵活性。

本文介绍如何对 Microsoft Azure 中的 IT 资源实现高级别的监管。 本文有助于了解 Microsoft Azure 中内置的安全和管理功能。

下面是本文中讨论的主要监管问题：

- 依据组织目标实施策略、过程和程序。

- 安全性和持续符合组织标准

- 警报和监视

## <a name="implementation-of-policies-processes-and-procedures"></a>实施策略、过程和程序 

管理已建立监督 Azure 中信息安全策略实施情况和操作连续性的角色和责任。 Microsoft Azure 管理负责监督各团队（包括第三方）中的安全性和持续性做法，并促进符合安全策略、进程和标准。

下面是演化而来的因素：

- 帐户预配

- 订阅控制

- 基于角色的访问控制

- 资源管理

- 资源跟踪

- 关键资源控制

- API 对计费信息的访问

- 网络控制

## <a name="account-provisioning"></a>帐户预配

定义帐户层次结构是在公司中使用和构造 Azure 服务的主要步骤，也是核心监管结构。 具备企业协议的客户可将环境进一步细分为部门、帐户，最终细分为订阅。

![帐户预配](./media/governance-in-azure/security-governance-in-azure-fig1.png)

如果没有企业协议，请考虑在订阅级别使用 [Azure 标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)定义层次结构。 Azure 订阅是包含所有资源的基本单位。 它还定义 Azure 中的多种限制，例如核心数、资源数，等等。订阅可以包含[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)，其中又可包含资源。 [RBAC](https://docs.microsoft.com/azure/api-management/api-management-role-based-access-control) 原则应用于这三个级别。

每家企业都是独特的，如果非企业用户使用 Azure 标记定义层次结构，则在公司内组织 Azure 具有非常大的灵活性。 在 Microsoft Azure 部署资源之前，应模拟层次结构，并了解对计费、资源访问和复杂性产生的影响。

## <a name="subscription-controls"></a>订阅控制

订阅控制如何对资源用量进行报告和计费。 订阅可以设置为单独计费和付款。 之前提到过，一个 Azure 帐户下可以有多个订阅。 订阅可用于确定公司中多个部门的 Azure 资源使用情况。

例如，如果一家公司有 IT 部门、HR 部门和营销部门，这些部门运行着不同的项目。 根据每个部门的 Azure 资源（例如虚拟机）使用情况，可以分别进行计费。 这样就可以控制每个部门的财务状况。

Azure 订阅建立三个参数：

- 唯一的订阅者 ID

- 计费位置

- 可用资源集

对于个人，这包括一个 Microsoft 帐户 ID、信用卡卡号和完整的 Azure 服务套件 - 然而 Microsoft 根据订阅类型强制实施使用限制。

Azure 注册层次结构定义企业协议内的服务构成方式。 企业门户允许客户基于可根据组织独特需求定制的灵活层次结构来划分对企业协议所关联的 Azure 资源的访问权限。 层次结构模式应符合组织的管理结构和地理结构，才能对相关计费和资源存取进行准确说明。

三种高级模式是功能、业务单元和地理模式，使用部门作为帐户分组的行政构造。 可以在每个部门内为帐户分配订阅，从而在 Azure 中创建计费接收器和一些关键限制（例如 VM 的数量、存储帐户等）。

![订阅控制](./media/governance-in-azure/security-governance-in-azure-fig2.png)


对于具有企业协议的组织，Azure 订阅遵循四级层次结构：

- 企业注册管理员

- 部门管理员

- 帐户所有者

- 服务管理员

此层次结构管理以下内容：

- 计费关系

- 帐户管理

- 对项目的基于角色的访问控制 (RBAC)

- 边界/限制

- 边界

  - 使用情况和计费（基于产品/服务数量的费率卡）

  - 限制

  - 虚拟网络

- 附加到 1 个 AAD（1 个 AAD 与多个订阅关联）

- 关联到企业注册帐户

## <a name="role-based-access-controls"></a>基于角色的访问控制

Azure 在最初发布时，对订阅的访问控制非常直接：只允许管理员或共同管理员访问。 有权访问经典模型中的订阅意味着有权访问门户中的所有资源。 缺少精细控制导致需要衍生订阅来针对 Azure 注册提供合理的访问控制级别。

![基于角色的访问控制](./media/governance-in-azure/security-governance-in-azure-fig3.png)

现在不再需要衍生订阅。 使用基于角色的访问控制，可将用户分配到标准角色（例如常见的“读取者”和“写入者”角色类型）。 还可以自定义角色。

[Azure 基于角色的访问控制 (RBAC)](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) 可用于对 Azure 进行细致的访问管理。 使用 RBAC，可以仅授予用户执行其作业所需的访问次数。 面向安全的公司应侧重于向员工提供他们所需的确切权限。 权限过多，攻击者可轻松威胁帐户。 权限太少，员工无法有效完成其工作。 Azure 基于角色的访问控制 (RBAC) 通过对 Azure 提供细致的访问管理帮助解决此问题。 RBAC 帮助你在团队中对职责进行分隔，仅向用户授予执行作业所需的访问权限。 而不是向每个人提供对 Azure 订阅或资源的无限权限，可以仅允许某些操作。

例如，使用 RBAC 允许一个员工管理订阅中的虚拟机，而允许另一个员工管理同一订阅中的 SQL 数据库。

Azure RBAC 有三种适用于所有资源类型的基本角色：

- **所有者**具有对所有资源的完全访问权限，包括将访问权限委派给其他用户的权限。

- 参与者可以创建和管理所有类型的 Azure 资源，但不能将访问权限授予其他用户。

- **读取器**可以查看现有 Azure 资源。

Azure 中的其他 RBAC 角色允许对特定的 Azure 资源进行管理。 例如，虚拟机参与者角色允许用户创建和管理虚拟机。 它并不授予其访问虚拟机连接的虚拟网络或子网的权限。

[RBAC 内置角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)列出了 Azure 中可用的角色。 它指定每个内置角色向用户授予的操作和范围。

通过将相应的 RBAC 角色分配给特定范围内的用户、组和应用程序授予访问权限。 角色分配的范围可以是订阅、资源组或单个资源。 分配在父范围内的角色也会将访问权限授予给其中所含的子范围。

例如，具有对资源组访问权限的用户可以管理其包含的所有资源，如网站、虚拟机和子网。

Azure RBAC 仅支持 Azure 门户和 Azure 资源管理器 API 中的 Azure 资源的管理操作。 它不能授权 Azure 资源的所有数据级别操作。 例如，可以授权某人管理存储帐户，但该用户不能管理存储帐户内的 blob 或表。 同样，可以管理SQL 数据库，但是不能管理其中的表。

如果想要了解有关 RBAC 如何帮助你管理访问权限的详细信息，请参阅[什么是基于角色的访问控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)。

如果没有符合你特定访问需求的内置角色，也可以在 Azure 基于角色的访问控制 (RBAC) 中[创建自定义角色](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)。 可以使用 [Azure PowerShell](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-powershell)、[Azure 命令行接口](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-azure-cli) (CLI) 和 [REST API](https://docs.microsoft.com/azure/active-directory/role-based-access-control-manage-access-rest) 创建自定义角色。 与内置角色一样，可以将自定义角色分配到订阅、资源组和资源范围内的用户、组和应用程序。

在每个订阅中，最多可以授予 2000 个角色分配。

## <a name="resource-management"></a>资源管理

Azure 最初只提供经典部署模型。 在此模型中，每个资源彼此独立；无法将相关的资源组合在一起。 因此，必须手动跟踪构成解决方案或应用程序的资源 ，并记得以协调的方式管理。

部署解决方案有两种方式：通过 Azure 门户单独创建每个资源；或创建一个脚本，以正确的顺序部署所有资源。 若要删除解决方案，必须逐个删除每个资源。 无法轻松应用和更新相关资源的访问控制策略。 最后，无法将标记应用到资源，因此无法使用有助于监视资源和管理计费的术语来标记资源。

Azure 在 2014 年引入了 Resource Manager，增加了资源组这一概念。 资源组是一种容器，专门容纳共享同一生命周期的资源。 Resource Manager 部署模型具有几大优点：

- 用户可以采用群组形式部署、管理和监视解决方案的所有服务，无需单独处理。

- 可以在解决方案的整个生命周期内重复部署该解决方案，确保以一致的状态部署资源。

- 可以将访问控制应用到资源组中的所有资源。将新资源添加到资源组时，会自动应用这些策略。

- 可以将标记应用到资源，以逻辑方式组织订阅中的所有资源。

- 可以使用 JavaScript 对象表示法 (JSON) 来定义解决方案的基础结构。 JSON 文件称为 Resource Manager 模板。

- 可以定义各资源之间的依赖关系，使其按正确的顺序进行部署。

![资源管理](./media/governance-in-azure/security-governance-in-azure-fig4.png)

使用 Resource Manager 可将资源放入有意义的组中，便于管理、记帐或自然关联。 如前所述，Azure 有两种部署模型。 在早期的[经典模型](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)中，管理的基本单位是订阅。 订阅中的资源很难分解，导致需要创建大量的订阅。 Resource Manager 模型中引入了资源组。

资源组是用于保存 Azure 解决方案相关资源的容器。 [资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)可以包含解决方案的所有资源，也可以只包含想要作为组来管理的资源。 根据对组织有利的原则，决定如何将资源分配到资源组。

有关模板的建议，请参阅[创建 Azure 资源管理器模板的最佳实践](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)。

Azure 资源管理器会分析依赖关系，以确保按正确的顺序创建资源。 如果一个资源依赖于另一个资源（例如虚拟机需要存储帐户才能访问磁盘）中的值，请设置依赖关系。

>[!Note]
>有关详细信息，请参阅[在 Azure 资源管理器模板中定义依赖关系](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies)。

还可以使用模板对基础结构进行更新。 例如，可以将新的资源添加到应用程序，并为已部署的资源添加配置规则。 如果模板指定要创建资源，但该资源已存在，则 Azure 资源管理器将执行更新而不是创建新资产。 Azure 资源管理器会将现有资产更新到相同状态，就如同该资产是新建的一样。

如果你需要其他操作（例如，安装未包含在安装程序中的软件），Resource Manager 可提供方案扩展。

## <a name="resource-tracking"></a>资源跟踪

随着组织中的用户不断地在订阅中添加资源，将资源与相应的部门、客户和环境相关联就变得越发重要。 可以通过标记将元数据附加到资源。 使用[标记](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)提供有关资源或所有者的信息。 标记不仅能让你通过多种方式聚合和分组资源，还能让你将该数据用于退款目的。

如果有一系列复杂的资源组和资源，并想要以最有利的方式可视化这些资产，则可以使用标记。 例如，可以标记组织中充当类似角色或者属于同一部门的资源。

如果不使用标记，组织中的用户可以创建多个资源，这可能会使将来的标识和管理变得十分困难。 例如，你可能想要删除某个项目的所有资源。 如未对项目的这些资源使用标记，则需要手动查找它们。 标记是降低不必要的订阅成本的重要方法。

资源不需要驻留在同一个资源组中就能共享一个标记。 可以创建自己的标记分类，以确保组织中的所有用户使用公用的标记，避免用户无意中应用稍有不同的标记（如“dept”而不是“department”）。

通过资源策略，可为组织创建标准规则。 可创建相应策略，确保使用适当的值标记资源。

> [!Note]
> 有关详细信息，请参阅[应用针对标记的资源策略](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy-tags)。

也可通过 Azure 门户查看标记的资源。

订阅的[使用情况报告](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)包括标记名称和值，可用于按标记对成本进行细分。

> [!Note]
> 有关标记的详细信息，请参阅 [使用标记来组织 Azure 资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)。

以下限制适用于标记：

- 每个资源或资源组最多可以有 15 个标记键值对。 此限制仅适用于直接应用到资源组或资源的标记。 资源组可以包含多个资源，这些资源每个都有 15 个标记键值对。

- 标记名称仅限 512 个字符。

- 标记值仅限 256 个字符。

- 应用于资源组的标记不会被该资源组中的资源继承。

如果有超过 15 个需要与资源关联的值，请将 JSON 字符串用于标记值。 JSON 字符串可以包含多个应用于单个标记键的值。

### <a name="tags-and-billing"></a>标记和计费

使用标记可对计费数据进行分组。 例如，如果针对不同组织运行多个虚拟机，可以使用标记根据成本中心对使用情况进行分组。 还可以使用标记根据运行时环境对成本进行分类；例如，在生产环境中运行的虚拟机的计费使用情况。

可以通过 [Azure 资源使用情况与费率卡 API](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview) 或者使用情况逗号分隔值 (CSV) 文件检索有关标记的信息。 可以从 [Azure 帐户门户](https://account.windowsazure.com/)或 [EA 门户](https://ea.azure.com/)下载使用情况文件。

>[!Note]
> 有关以编程方式访问计费信息的详细信息，请参阅[深入了解 Microsoft Azure 资源消耗](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview)。 有关 REST API 操作，请参阅 [Azure 计费 REST API 参考](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)。

在为支持标记和计费的服务下载使用情况 CSV 时，标记将显示在“标记”列中。

## <a name="critical-resource-controls"></a>关键资源控制

随着组织不断在订阅中添加核心服务，确保这些服务随时可用，避免业务中断已变得越来越重要。 使用[资源锁](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)可以限制针对高价值资源（修改或删除这些资源会给应用程序或云基础结构造成重大影响）的操作。 可以将锁应用于订阅、资源组或资源。 通常，我们会将锁应用于虚拟网络、网关和存储帐户等基础资源。

资源锁目前支持两个值：CanNotDelete 和 ReadOnly。 CanNotDelete 表示用户（具有相应权限）仍可读取或修改某个资源，但无法删除该资源。 ReadOnly 表示经过授权的用户无法删除或修改某个资源。

Resource Manager 锁仅适用于管理平面内发生的操作，包括发送到 <https://management.azure.com> 的操作。锁不会限制资源如何执行各自的函数。 资源更改将受到限制，但资源操作不受限制。 例如，SQL 数据库上的 ReadOnly 锁将阻止删除或修改该数据库，但不会阻止创建、更新或删除该数据库中的数据。

应用“ReadOnly”可能会导致意外结果，因为看似读取操作的某些操作实际上需要其他操作。 例如，在存储帐户上放置 **ReadOnly** 锁将阻止所有用户列出密钥。 列出密钥操作通过 POST 请求进行处理，因为返回的密钥可用于写入操作。

![关键资源控制](./media/governance-in-azure/security-governance-in-azure-fig5.png)

另举一例，在应用服务资源上放置 ReadOnly 锁将阻止 Visual Studio 服务器资源管理器显示资源文件，因为该交互需要写入访问权限。

与基于角色的访问控制不同，可以使用管理锁来对所有用户和角色应用限制。 若要了解如何为用户和角色设置权限，请参阅 [Azure 基于角色的访问控制](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure)。

在父范围应用锁时，该范围内所有资源都将继承相同的锁。 即使是之后添加的资源也会从父作用域继承该锁。 继承中限制性最强的锁优先执行。

若要创建或删除管理锁，必须有权访问 Microsoft.Authorization/ _或 Microsoft.Authorization/locks/_ 操作。 在内置角色中，只有“所有者”和“用户访问管理员”有权执行这些操作。

## <a name="api-access-to-billing-information"></a>API 对计费信息的访问

使用 Azure 计费 API 将用量和资源数据提取到偏好的数据分析工具。 Azure 资源用量和 RateCard API 可以帮助你准确预测及管理成本。 这些 API 作为资源提供程序实现，属于 Azure 资源管理器公开的 API 系列。

### <a name="azure-resource-usage-api-preview"></a>Azure 资源使用状况 API（预览）

使用 Azure [资源用量 API](https://msdn.microsoft.com/library/azure/mt219003) 获取预估的 Azure 耗用数据量。 该 API 包括：

- Azure 基于角色的访问控制 - 在 [Azure 门户](https://portal.azure.com/)上或通过 [Azure PowerShell cmdlet](https://docs.microsoft.com/powershell/azure/overview) 配置访问策略，指定哪些用户或应用程序有权访问订阅的用量数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到计费读取者、读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。

- **每小时或每日聚合** - 调用方可以指定 Azure 使用状况数据是以每小时存储桶为单位，还是以每日存储桶为单位。 默认值为每日聚合。

- 实例元数据（包括资源标记）- 获取实例级详细信息，例如完全限定的资源 URI (/subscriptions/{subscription-id}/..)，以及资源组信息和资源标记。 对于交叉费用等用例，此元数据可帮助你明确以编程方式按标记分配使用状况。

- **资源元数据** - 资源详细信息（如测定仪名称、测定仪类别、测定仪子类别、计价单位和区域）让调用方更好地了解消耗量。 此外，我们还将致力于跨 Azure 门户、Azure 使用状况 CSV、EA 计费 CSV 和其他面向公众体验的情况下保持资源元数据术语的一致性，从而让你能够在不同体验间关联数据。

- **所有产品类型的用量** - 提供所有产品类型的用量数据，例如即用即付、MSDN、货币承诺、货币信用额和 EA。

Azure 资源 RateCard API（预览）

使用 Azure 资源 RateCard API 获取可用 Azure 资源的列表，以及每个资源的估计定价信息。 该 API 包括：

- Azure 基于角色的访问控制 - 在 Azure 门户上或通过 Azure PowerShell cmdlet 配置访问策略，指定哪些用户或应用程序有权访问 RateCard 数据。 调用方必须使用标准 Azure Active Directory 令牌进行身份验证。 将调用方添加到读取者、所有者或参与者角色，以访问特定 Azure 订阅的用量数据。

- **支持即用即付、MSDN、货币承诺和货币信用额产品（不支持 EA）**- 此 API 提供了 Azure 产品级费率信息。 此 API 的调用方必须传入产品/服务信息，才能获取资源详细信息和费率。 由于 EA 产品按注册自定义费率，因此我们暂时无法提供 EA 费率。 使用状况和价目表 API 组合可以实现下面一些方案：

- **Azure 月支出** - 将用量与 RateCard API 结合使用可以深入了解云的月支出。 可以分析用量和估计费用的每小时和每日存储桶。

- **设置警报** - 可将用量和 RateCard API 结合使用，估计云消耗量和费用，从而设置基于资源或资金的警报。

- **预测帐单** - 可以估计消耗量和云支出，应用机器学习算法来预测计费周期结束时的帐单。

- **消耗费用分析** - 如果要将工作负荷转移到 Azure，可以使用 RateCard API 来预测帐单（通过提供的所需用量数据）。 如果在其他云或私有云中拥有现有的工作负荷，则还可以将用量与 Azure 费率进行映射，以便更好地估计 Azure 支出。 通过这种估计可以根据产品生成透视图，在不同的产品类型之间进行比较（不仅仅局限于即用即付产品，还包括货币承诺和货币信用额支付的产品）。 此外，API 还能够按区域查看费用差异，使你能够做假设性成本分析，帮助做出部署决策。

- 假设分析 - 可以确定在其他区域或 Azure 资源的其他配置上运行工作负荷是否会更具成本效益。 Azure 资源费用根据所用的 Azure 区域而异。

- 还可以确定其他 Azure 产品/服务类型是否提供更优惠的 Azure 资源费率。

## <a name="networking-controls"></a>网络控制

对资源的访问可能是从内部（在企业网络中）或外部（通过 Internet）发起的。 组织中的用户经常会无意中将资源放在错误的位置，使其遭到恶意访问。 与对待本地/设备一样，企业必须增设相应的控制机制，确保 Azure 用户做出正确的决策。

![网络控制](./media/governance-in-azure/security-governance-in-azure-fig6.png)

为了进行订阅监管，我们指定了可提供基本访问控制的核心资源。 这些核心资源包括：

### <a name="network-connectivity"></a>网络连接

[虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)是子网的容器对象。 尽管严格意义上没有必要，但将应用程序连接到内部企业资源时往往要用到它。 通过 Azure 虚拟网络服务可安全地将 Azure 资源连接到具有虚拟网络 (VNet) 的各个资源。

VNet 是自己的网络在云中的表示形式。 VNet 是对专用于订阅的 Azure 云进行的逻辑隔离。 也可将 VNet 连接到本地网络。

以下是 Azure 虚拟网络的功能：

- 隔离：VNet 之间彼此隔离。 可以为使用相同 CIDR 地址块的开发、测试和生产创建单独的 VNet。 相反地，也可以创建使用不同 CIDR 地址的多个 VNet 并将网络连接在一起。 可将一个 VNet 分为多个子网。 Azure 为连接到 VNet 的 VM 和云服务角色实例提供内部名称解析。 可选择配置 VNet 来使用自己的 DNS 服务器，而不使用 Azure 内部名称解析。

- Internet 连接：默认情况下，所有 Azure 虚拟机 (VM) 和连接到 VNet 的云服务角色实例都具有 Internet 访问权限。 根据需要，还可对特定资源启用入站访问。

- Azure 资源连接：云服务和 VM 等 Azure 资源可连接到同一 VNet。 即使资源在不同的子网中，也可使用专用 IP 地址连接彼此。 Azure 提供子网、VNet 和本地网络之间的默认路由，因此无需配置和管理路由。

- VNet 连接性：VNet 之间可相互进行连接，从而使连接到任何 VNet 的资源可与任何其他 VNet 上的所有资源进行通信。

- 本地连接性：可通过网络和 Azure 间的专用网络连接或基于 Internet 的站点到站点 VPN 连接将 VNet 连接到本地网络。

- 流量筛选：按源 IP 地址和端口、目标 IP 地址和端口以及协议，可对 VM 和云服务角色实例网络流量进行入站和出站筛选。

- 路由：可选择通过配置自己的路由或通过网关使用 BGP 路由来替代 Azure 默认路由。

## <a name="network-access-controls"></a>网络访问控制

[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)类似于防火墙，为资源如何通过网络“通信”提供规则。 它们针对子网（或虚拟机）如何/是否能够连接到 Internet 或同一虚拟网络中的其他子网提供精细控制。

网络安全组 (NSG) 包含一系列安全规则，这些规则可以允许或拒绝流向连接到 Azure 虚拟网络 (VNet) 的资源的网络流量。 可以将 NSG 关联到子网、单个 VM（经典）或附加到 VM 的单个网络接口 (NIC) (Resource Manager)。

将 NSG 关联到子网时，规则适用于连接到该子网的所有资源。 也可通过将 NSG 关联到 VM 或 NIC 来进一步限制流量。

## <a name="security-and-continuous-compliance-with-organizational-standards"></a>安全性和持续符合组织标准

每个企业有着不同的需求，每个企业将从云解决方案获得不同的好处。 尽管如此，所有类型的客户迁移到云存在着同样的基本问题。 他们想保留对数据的控制，并希望数据在保持安全隐私的同时保有透明度和符合性。

客户对云提供商的要求是：

- **保护数据安全**尽管认识到云可以提供增强的数据安全性和管理控制，IT 负责人还是担心相比他们目前的内部方案，迁移到云会使他们更容易受到黑客的攻击。

- **保持数据私有**云服务为企业带来了独特的隐私挑战。 公司指靠云来缩减基础结构成本并提高灵活性的同时，他们同样担心对数据的存储位置、访问人员和使用方式失去控制。

- **保证控制权**甚至当公司充分利用云部署更多创新方案时，他们也非常担心对数据失去控制。 近期披露政府机构通过合法和法外方式访问客户数据，这使某些 CIO 对在云中存储数据更为谨慎。

- **提升透明度**虽然安全、隐私和控制对企业决策者很重要，但是他们也希望能独立验证数据如何存储、访问以及受保护。

- **保持符合性**随着公司扩展云技术应用，标准和规则的复杂性和范围不断演化。 公司要求其符合性标准得到满足，且符合性随着规则变化不断演化。

## <a name="security-configuration-monitoring-and-alerting"></a>安全性配置、监视和警报

Azure 订阅者可从多种设备管理其云环境，这些设备包括管理工作站、开发人员电脑，甚至是具有任务特定权限的特权最终用户设备。 在某些情况下，可通过基于 Web 的控制台（例如 Azure 门户）来执行管理功能。 有其他情况下，可以从本地系统通过虚拟专用网络 (VPN)、终端服务、客户端应用程序协议或 Azure 服务管理 API (SMAPI)（以编程方式）直接连接到 Azure。 此外，客户端终结点（例如平板电脑或智能手机）可以加入域或者受到隔离且不受管理。

尽管这么多种的访问和管理功能可提供丰富的选项，但选项太多也可能会让云部署承受巨大风险。 它可能变得难以管理、跟踪和审核管理操作。 这种差异还可能由于用于管理云服务的客户端终结点进行的访问不受管制而带来安全威胁。 使用普通工作站或专用工作站开发和管理基础结构会打开诸如 Web 浏览（例如水坑攻击）或电子邮件（例如社交工程和网络钓鱼）等不可预测的威胁媒介。

监视、日志记录和审核可为跟踪和了解管理活动提供基础，但受限于所生成的数据量，它不一定都能巨细无遗地审核所有操作。 但是，审核管理策略的效果是最佳做法。

使用 AD DS GPO 的 Azure 安全监管控制所有管理员的 Windows 接口，例如文件分享。 将管理工作站纳入审核、监视和日志记录过程中。 跟踪所有管理员和开发人员的访问和使用活动。

### <a name="azure-security-center"></a>Azure 安全中心

[Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-intro)在一个中心视图中提供订阅中资源的安全状态，并提供建议帮助避免资源泄密。 它支持更精细的策略（例如，向特定的资源组应用策略，使企业能够根据面临的风险调整立场）。

![Azure 安全中心](./media/governance-in-azure/security-governance-in-azure-fig7.png)

安全中心提供 Azure 订阅之间的集成安全监视和策略管理，帮助检测可能被忽略的威胁，且适用于广泛的安全解决方案生态系统。 为订阅的资源启用[安全策略](https://docs.microsoft.com/azure/security-center/security-center-policies)以后，安全中心将分析相关资源的安全性，确定可能的漏洞。 可立即提供有关网络配置的信息。

Azure 安全中心结合了 Azure 订阅中所有资源的最佳做法分析和安全策略管理。 此功能强大且易于使用的工具使安全小组和风险官能够防止、检测和应对安全威胁，因其自动收集和分析来自 Azure 资源、网络和合作伙伴解决方案（如反恶意软件和防火墙）中的安全性数据。

此外，Azure 安全中心应用高级分析（包括机器学习和行为分析），同时充分利用 Microsoft 产品和服务、Microsoft 数字犯罪部门 (DCU)、Microsoft 安全响应中心 (MSRC) 以及外部源提供的全球威胁情报。 [安全监管](https://www.credera.com/blog/credera-site/azure-governance-part-4-other-tools-in-the-toolbox/)可以在订阅级别广泛应用，也可缩小到具体要求，通过策略定义应用于个别资源。

最后，Azure 安全中心基于这些策略分析资源安全运行状况，并用其提供具有洞察力的仪表板和事件警报，例如恶意软件检测或恶意 IP 连接尝试。

>[!Note]
> 如需详细了解如何应用建议，请参阅[在 Azure 安全中心实施安全建议](https://docs.microsoft.com/azure/security-center/security-center-recommendations)。

安全中心会收集来自虚拟机的数据，进而评估其安全状态、提供安全建议并发出威胁警报。 初次访问安全中心时，会在订阅中的所有虚拟机上启用数据收集。 建议使用数据收集，但可通过安全中心策略中[禁用数据收集](https://docs.microsoft.com/azure/security-center/security-center-faq)来选择退出。

最后，Azure 安全中心是一个开放式平台，允许 Microsoft 合作伙伴和独立软件供应商创建可与 Azure 安全中心对接的软件来增强其功能。

Azure 安全中心监视以下 Azure 资源：

- 虚拟机 (VM)（包括云服务）

- Azure 虚拟网络

- Azure SQL 服务

- 与 Azure 订阅集成的合作伙伴解决方案，例如 VM 和[应用服务环境](https://docs.microsoft.com/azure/app-service/app-service-app-service-environments-readme)上的 Web 应用程序防火墙。

### <a name="operations-management-suite"></a>Operations Management Suite

OMS 软件开发和服务团队的信息安全和[监管计划](https://github.com/Microsoft/azure-docs/blob/master/articles/log-analytics/log-analytics-security.md)支持其业务需求，并遵循 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/)和 [Microsoft 信任中心符合性](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)中所述的法律和法规。 其中还介绍了 OMS 如何建立安全要求、识别安全控制、管理和监控风险。 每年，我们都会对策略、标准、过程和指导原则进行评审。

每个 OMS 开发团队成员都会接受正式的应用程序安全培训。 在内部，我们将版本控制系统用于软件开发。 每个软件项目都受版本控制系统保护。

Microsoft 有安全性和合规性团队，负责对 Microsoft 中所有服务进行监控和评估。 由信息安全专员组成该团队，他们不会加入开发 OMS 的工程部门。 安全专员有其自己的管理链，负责对产品和服务进行独立评估，以确保安全性和合规性。

Operations Management Suite（简称为 OMS）是在云中从无到有设计出的管理服务集合。 OMS 组件完全在 Azure 中托管，而不是在本地资源上部署和管理。 配置工作极少，基本上在几分钟内就能将它启动并运行。

![Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig8.png)

不能仅仅因为 OMS 服务在云中运行，就认为它们无法有效管理本地环境。

在数据中心内的任意 Windows 或 Linux 计算机上安装一个代理，就能将数据发送到 Log Analytics，可在其中连同从云或本地服务收集的其他所有数据一起进行分析。 使用 Azure 备份和 Azure Site Recovery 可以利用云来实现本地资源的备份和高可用性。

云中的 Runbook 通常无法访问本地资源，但你可以在数据中心内托管 Runbook 的一台或多台计算机上安装代理。 启动 Runbook 时，只需指定是要让它在云中还是本地辅助角色上运行即可。

OMS 的核心功能由 Azure 中运行的一组服务提供。 每个服务提供特定的管理功能，可以组合这些服务来实现不同的管理方案。

![Operations Manager Suite](./media/governance-in-azure/security-governance-in-azure-fig9.JPG)

Azure 操作管理器通过提供管理解决方案扩展其功能。 [管理解决方案](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions)是预先打包的逻辑集，可以实现利用一个或多个 OMS 服务的管理方案。

![Azure 操作管理](./media/governance-in-azure/security-governance-in-azure-fig10.png)

Microsoft 与合作伙伴提供不同的解决方案，可将其添加到 Azure 订阅，提高 OMS 投资的价值。

合作伙伴可以创建自己的解决方案来支持自己的应用程序和服务，并通过 Azure 应用商店或快速启动模板将它们提供给用户。

## <a name="performance-alerting-and-monitoring"></a>性能警报和监视

### <a name="alerting"></a>警报

警报是用来监视 Azure 资源指标、事件或日志并在符合指定条件时发出通知的一种方法。

**不同 Azure 服务中的警报**

警报可跨不同的服务使用，包括：

- Application Insights：启用 Web 测试和指标警报。

>[!Note]
> 请参阅[在 Application Insights 中设置警报](https://docs.microsoft.com/azure/application-insights/app-insights-alerts)和[监视任何网站的可用性和响应能力](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)。

- Log Analytics (Operations Management Suite)：用于将活动日志和诊断日志路由到 Log Analytics。 Operations Management Suite 允许指标、日志和其他警报类型。

>[!Note]
> 有关详细信息，请参阅 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-alerts) 中的警报。

- Azure Monitor：基于指标值和活动日志事件启用警报。 可以使用 [Azure Monitor REST API](https://msdn.microsoft.com/library/dn931943.aspx) 来管理警报。

>[!Note]
> 有关详细信息，请参阅[使用 Azure 门户、PowerShell 或命令行接口创建警报](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-alerts-portal)。

### <a name="monitoring"></a>监视

云应用中的性能问题可能会影响业务。 使用多个互连的组件和频繁发布版本时，性能随时可能会下降。 开发一款应用后，你的用户通常会发现其中的一些问题，而你在测试时却发现不了这样的问题。 应该立即知道这些问题，并使用工具来诊断和解决问题。 Microsoft Azure 提供一系列的工具用于识别这些问题。

**如何监视 Azure 云应用？**

可以使用一系列工具来监视 Azure 应用程序和服务。 这些工具的某些功能是相互重叠的。 之所以存在这种重叠，一部分原因是历史遗留问题，还有一部分原因是应用程序的开发与操作之间界限不明。

下面是主要工具：

- **Azure Monitor** 是用于监视 Azure 中运行的服务的基本工具。 它可以提供有关服务吞吐量和周边环境的基础结构级数据。 如果在 Azure 中管理所有应用，并想要确定是否需要扩展或缩减资源，则 Azure Monitor 可以提供初始信息。

- **Application Insights** 可用于开发，并且可充当生产监视解决方案。 它的工作方式是在应用中安装一个包，提供更内在的动态视图。 其数据包括依赖项的响应时间、异常跟踪、调试快照和执行配置文件。 它提供强大的智能工具用于分析所有这些遥测数据，既能帮助你调试应用，也能帮助你了解用户将它用于哪种目的。 可以判断响应时间出现尖峰的原因是应用中出现某种问题，还是存在某种外部资源调配问题。 如果使用 Visual Studio，而应用出现错误，可以直接找到有问题的代码行，并修复错误。

- **Log Analytics** 面向需要优化性能，并针对生产环境中运行的应用程序规划维护的用户。 此服务驻留在 Azure 中。 它可以从许多源收集和聚合数据，不过会出现 10 到 15 分钟的延迟。 它为 Azure、本地基础结构和第三方基于云的基础结构（例如 Amazon Web Services）提供整体 IT 管理解决方案。 它提供更丰富的工具来跨更多的源分析数据，允许针对所有日志运行复杂的查询，并可以根据指定的条件主动发出警报。 甚至可以将自定义数据收集到它的中心存储库，以便查询和可视化这些数据。

- **System Center Operations Manager (SCOM)** 用于管理和监视大型云安装。 作为本地 Windows Sever 和基于云的 Hyper-V 计算机的管理工具，可能已经对它有所了解，但它还能集成和管理 Azure 应用。 除此之外，它还能在现有的实时应用中安装 Application Insights。 如果某个应用出现故障，它会即时发出通知。


## <a name="next-steps"></a>后续步骤

- 
            [创建 Azure 资源管理器模板的最佳做法](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)。

- [Azure 订阅监管实施示例](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-manager-subscription-examples)。

- [Microsoft Azure 政府](https://docs.microsoft.com/azure/azure-government/)。
