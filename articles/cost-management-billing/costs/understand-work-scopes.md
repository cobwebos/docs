---
title: 了解和使用 Azure 成本管理范围
description: 本文将帮助你了解 Azure 中提供的计费和资源管理范围，以及如何在成本管理和 Api 中使用范围。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 58bd1d3e3fb27344706b23866a68c7e1363e7ec2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990133"
---
# <a name="understand-and-work-with-scopes"></a>了解并使用范围

本文将帮助你了解 Azure 中提供的计费和资源管理范围，以及如何在成本管理和 Api 中使用范围。

## <a name="scopes"></a>作用域

_作用域_是 Azure 资源层次结构中的一个节点，Azure AD 用户访问和管理服务。 大多数 Azure 资源是在订阅中创建并部署到资源组中的。 Microsoft 还在 Azure 订阅之上提供了两个层次结构，这些层次结构具有用于管理计费数据的专用角色：
- 计费数据，如付款和发票
- 云服务，例如成本和策略管理

作用域是你在其中管理计费数据、拥有特定于付款的角色、查看发票和执行常规帐户管理的位置。 计费和帐户角色与使用[AZURE RBAC](../../role-based-access-control/overview.md)的资源管理所用的角色分开管理。 若要清楚地区分不同范围的意图，包括访问控制差异，它们分别称为_计费范围_和_RBAC 范围_。

## <a name="how-cost-management-uses-scopes"></a>成本管理如何使用作用域

成本管理适用于上述资源的所有范围，使组织能够在他们有权访问的级别管理成本，无论这是整个计费帐户还是单个资源组。 尽管根据你的 Microsoft 协议（订阅类型），计费范围有所不同，但 RBAC 范围并不相同。

## <a name="azure-rbac-scopes"></a>Azure RBAC 范围

Azure 支持三个作用域进行资源管理。 每个作用域都支持管理访问和管理，包括但不限于成本管理。

- [**管理组**](../../governance/management-groups/overview.md)-分层容器，最多8个级别，用于组织 Azure 订阅。

    资源类型： [managementGroups/](/rest/api/resources/managementgroups)

- **订阅**-Azure 资源的主要容器。

    资源类型： [Microsoft. 资源/订阅](/rest/api/resources/subscriptions)

- [**资源组**](../../azure-resource-manager/management/overview.md#resource-groups)-共享相同生命周期的 Azure 解决方案的相关资源的逻辑分组。 例如，同时部署和删除的资源。

    资源类型： [Microsoft .resources/订阅/resourceGroups](/rest/api/resources/resourcegroups)

管理组允许您将订阅组织到层次结构中。 例如，你可以使用管理组创建逻辑组织层次结构。 然后，为团队订阅生产和开发/测试工作负荷。 然后，在订阅中创建资源组以管理每个子系统或组件。

创建组织层次结构可以实现成本和策略符合性汇总组织。 然后，每个领导都可以查看和分析其当前成本。 然后，他们可以创建预算来实现错误支出模式，并在最低级别通过顾问建议优化成本。

使用 Azure RBAC，授予访问权限以查看成本，还可以选择管理成本配置，例如预算和导出。 使用 Azure RBAC 向 Azure AD 的用户和组授予访问权限，以执行在特定范围和下面的角色中定义的一组预定义操作。 例如，分配给管理组作用域的角色还向嵌套的订阅和资源组授予相同的权限。

对于以下每个作用域，成本管理支持以下内置角色：

- [**所有者**](../../role-based-access-control/built-in-roles.md#owner)–可以查看成本并管理所有内容，包括成本配置。
- [**参与者**](../../role-based-access-control/built-in-roles.md#contributor)–可以查看成本并管理所有内容，包括成本配置，但不包括访问控制。
- [**读者**](../../role-based-access-control/built-in-roles.md#reader)–可以查看所有内容，包括成本数据和配置，但不能进行任何更改。
- [**成本管理参与者**](../../role-based-access-control/built-in-roles.md#cost-management-contributor)–可以查看成本、管理成本配置并查看建议。
- [**成本管理读者**](../../role-based-access-control/built-in-roles.md#cost-management-reader)–可以查看成本数据、成本配置和查看建议。

成本管理参与者是推荐的最小特权角色。 它允许用户创建和管理预算和导出，更有效地监视和报告成本。 成本管理参与者可能还需要其他角色以支持端到端成本管理方案。 请考虑以下方案：

- **超出预算时执行操作**–成本管理参与者还需要具有创建和/或管理操作组的访问权限，以自动响应超额。 请考虑向包含超出预算阈值时要使用的操作组的资源组授予[监视参与者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)。 自动执行特定操作需要使用特定服务的其他角色，如自动化和 Azure Functions。
- **计划成本数据导出**-成本管理参与者还需要访问管理存储帐户，以便将数据复制到存储帐户。 请考虑将[存储帐户参与者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)授予包含将成本数据导出到的存储帐户的资源组。
- **查看节约成本的建议**-默认情况下，成本管理读者和成本管理参与者有权*查看*成本建议。 但是，对成本建议的操作的访问权限需要访问单个资源。 如果要采取基于成本的建议，请考虑授予[服务特定的角色](../../role-based-access-control/built-in-roles.md#built-in-role-descriptions)。

## <a name="enterprise-agreement-scopes"></a>企业协议范围

企业协议（EA）计费帐户（也称为 "注册"）具有以下作用域：

- [**计费帐户**](../manage/view-all-accounts.md)-表示 EA 注册。 在此范围内生成发票。 不基于使用情况的采购（如 Marketplace 和预订）仅在此范围内可用。 它们不在部门或注册帐户中表示。

    资源类型： `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **部门**-可选的注册帐户分组。

    资源类型： `Billing/billingAccounts/departments`

- **注册帐户**-表示单个帐户的所有者。 不支持授予对多个用户的访问权限。

    资源类型： `Microsoft.Billing/billingAccounts/enrollmentAccounts`

尽管调控范围绑定到单个目录，但 EA 计费范围并不相同。 EA 计费帐户可能具有跨任意数量的 Azure AD 目录的订阅。

EA 计费范围支持下列角色：

- **企业管理员**–可以管理计费帐户设置和访问权限，可以查看所有成本，还可以管理成本配置。 例如，预算和出口。 在函数中，EA 计费范围与[成本管理参与者 AZURE RBAC 角色](../../role-based-access-control/built-in-roles.md#cost-management-contributor)相同。
- **企业只读用户**-可以查看计费帐户设置、成本数据和成本配置。 例如，预算和出口。 在函数中，EA 计费范围与[成本管理读取器 AZURE RBAC 角色](../../role-based-access-control/built-in-roles.md#cost-management-reader)相同。
- **部门管理员**–可以管理部门设置，如成本中心，可以访问、查看所有成本和管理成本配置。 例如，预算和出口。  必须为部门管理员和只读用户启用**DA 查看费用**计费帐户设置才能查看成本。 如果禁用了**DA 查看费用**，则即使是帐户或订阅所有者，部门用户也不能在任何级别上看到成本。
- "**部门只读用户**"-可以查看部门设置、成本数据和成本配置。 例如，预算和出口。 如果禁用了**DA 查看费用**，则即使是帐户或订阅所有者，部门用户也不能在任何级别上看到成本。
- **帐户所有者**-可以管理注册帐户设置（如成本中心）、查看所有成本并管理注册帐户的成本配置（例如预算和出口）。 必须为帐户所有者和 RBAC 用户启用**AO 查看费用**计费帐户设置，才能查看成本。

EA 计费帐户用户无法直接访问发票。 可从外部批量授权系统获取发票。

Azure 订阅嵌套在 "注册帐户" 下。 计费用户有权访问其各自范围内的订阅和资源组的成本数据。 他们无权查看或管理 Azure 门户中的资源。 计费用户可以通过导航到服务 Azure 门户列表中的 "**成本管理 + 计费**" 来查看成本。 然后，他们可以根据需要报告的特定订阅和资源组来筛选成本。

计费用户无权访问管理组，因为它们不在特定的计费帐户下显式显示。 必须对管理组显式授予访问权限。 管理组从所有嵌套订阅汇总成本。 但它们只包含基于使用情况的购买。 它们不包括预订和第三方 Marketplace 产品/服务。 若要查看这些开销，请使用 EA 计费帐户。

## <a name="individual-agreement-scopes"></a>单个协议范围

从单个产品/服务创建的 Azure 订阅，例如即用即付和相关类型（如免费试用版和开发/测试产品/服务）没有明确的计费帐户范围。 相反，每个订阅都有一个帐户所有者或帐户管理员，例如 EA 帐户所有者。

- [**计费帐户**](../manage/view-all-accounts.md)-表示一个或多个 Azure 订阅的单个帐户所有者。 它目前不支持授予对多个人的访问权限或访问聚合成本视图。

    资源类型：不适用

单个 Azure 订阅帐户管理员可以从[Azure 帐户中心](https://account.azure.com/subscriptions)查看和管理计费数据，如发票和付款。 但是，他们无法查看成本数据或管理 Azure 门户中的资源。 若要向帐户管理员授予访问权限，请使用前面提到的成本管理角色。

与 EA 不同，单独的 Azure 订阅帐户管理员可以在 Azure 门户中查看其发票。 请记住，成本管理读者和成本管理参与者角色不提供对发票的访问权限。 有关详细信息，请参阅[如何授予对发票的访问权限](../manage/manage-billing-access.md#give-read-only-access-to-billing)。

## <a name="microsoft-customer-agreement-scopes"></a>Microsoft 客户协议范围

Microsoft 客户协议计费帐户具有以下作用域：

- **计费帐户**-表示针对多个 Microsoft 产品和服务的客户协议。 客户协议计费帐户的功能与 EA 注册不同。 EA 注册更接近于计费配置文件。

    资源类型： `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **计费配置文件**-定义发票中包含的订阅。 计费配置文件在功能上等同于 EA 注册，因为这是生成发票的范围。 同样，不基于使用情况的采购（如 Marketplace 和预订）仅在此范围内可用。 它们不包含在发票部分。

    资源类型： `Microsoft.Billing/billingAccounts/billingProfiles`

- **发票部分**-表示发票或计费配置文件中的一组订阅。 发票部分类似于部门，多个人可以访问发票部分。

    资源类型： `Microsoft.Billing/billingAccounts/invoiceSections`

- **Customer** -表示一组订阅，这些订阅与通过合作伙伴载入到 Microsoft 客户协议的特定客户关联。 此作用域特定于 CSP。

与 EA 计费范围不同，客户协议计费_帐户绑定_到单个目录，并且不能跨多个 Azure AD 目录拥有订阅。

客户协议计费范围不适用于合作伙伴。 合作伙伴角色和权限在[分配用户角色和权限](/partner-center/permissions-overview)中进行了介绍。

客户协议计费范围支持下列角色：

- **所有者**–可以管理计费设置和访问，查看所有成本和管理成本配置。 例如，预算和出口。 在函数中，此客户协议计费范围与[成本管理参与者 AZURE RBAC 角色](../../role-based-access-control/built-in-roles.md#cost-management-contributor)相同。
- **参与者**-可以管理计费设置，但访问、查看所有成本和管理成本配置。 例如，预算和出口。 在函数中，此客户协议计费范围与[成本管理参与者 AZURE RBAC 角色](../../role-based-access-control/built-in-roles.md#cost-management-contributor)相同。
- **读者**–可以查看计费设置、成本数据和成本配置。 例如，预算和出口。 在函数中，此客户协议计费范围与[成本管理读者 AZURE RBAC 角色](../../role-based-access-control/built-in-roles.md#cost-management-reader)相同。
- **发票管理器**–可以查看和支付发票，还可以查看成本数据和配置。 例如，预算和出口。 在函数中，此客户协议计费范围与[成本管理读者 AZURE RBAC 角色](../../role-based-access-control/built-in-roles.md#cost-management-reader)相同。
- **Azure 订阅创建者**-可以创建 azure 订阅、查看成本和管理成本配置。 例如，预算和出口。 在函数中，此客户协议计费范围与 EA 注册帐户所有者角色相同。

Azure 订阅嵌套在 "发票" 部分下，例如它们在 "EA 注册帐户" 下的方式。 计费用户有权访问其各自范围内的订阅和资源组的成本数据。 但是，他们无权查看或管理 Azure 门户中的资源。 计费用户可以通过导航到服务 Azure 门户列表中的 "**成本管理 + 计费**" 来查看成本。 然后，将成本筛选为需要报告的特定订阅和资源组。

计费用户无权访问管理组，因为他们没有明确地归属于计费帐户下。 但是，如果为组织启用了管理组，则会将所有订阅成本汇总到计费帐户和根管理组，因为它们均受限于单个目录。 管理组只包含基于使用情况的采购。 管理组中不包含预订和第三方 Marketplace 产品。 因此，计费帐户和根管理组可能会报告不同的总计。 若要查看这些开销，请使用计费帐户或各自的计费配置文件。

## <a name="aws-scopes"></a>AWS 范围

完成 AWS 集成后，请参阅[安装和配置 AWS 集成](aws-integration-set-up-configure.md)。 可用范围如下：

- **外部计费帐户**-表示与第三方供应商签订的客户协议。 这类似于 EA 计费帐户。

    资源类型： `Microsoft.CostManagement/externalBillingAccounts`

- **外部订阅**-表示包含第三方供应商的客户操作帐户。 这类似于 Azure 订阅。

    资源类型： `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>云解决方案提供程序（CSP）范围

对于包含 Microsoft 客户协议的客户，Csp 支持以下范围：

- **计费帐户**-表示针对多个 Microsoft 产品和服务的客户协议。 客户协议计费帐户的功能与 EA 注册不同。 EA 注册更接近于计费配置文件。

    资源类型： `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **计费配置文件**-定义发票中包含的订阅。 计费配置文件在功能上等同于 EA 注册，因为这是生成发票的范围。 同样，不基于使用情况的采购（如 Marketplace 和预订）仅在此范围内可用。

    资源类型： `Microsoft.Billing/billingAccounts/billingProfiles`

- **Customer** -表示与特定客户关联的一组订阅，由合作伙伴载入到 Microsoft 客户协议。

只有拥有*全局管理员*和*管理代理*角色的用户才能直接在合作伙伴的 Azure 租户中管理和查看计费帐户、计费配置文件和客户的成本。 有关合作伙伴中心角色的详细信息，请参阅[分配用户角色和权限](/partner-center/permissions-overview)。

如果客户有 Microsoft 客户协议，Azure 成本管理仅支持 CSP 合作伙伴客户。 对于尚不是 Microsoft 客户协议的 CSP 支持的客户，请参阅[合作伙伴中心](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)。

## <a name="switch-between-scopes-in-cost-management"></a>在成本管理中切换范围

Azure 门户中的所有成本管理视图都包含视图左上角的 "**范围**选择" 欣然。 使用它可以快速更改范围。 单击 "**作用域**" 欣然打开 "作用域选取器"。 它显示了计费帐户、根管理组以及未嵌套在根管理组下的任何订阅。 若要选择一个范围，请单击背景以突出显示它，然后单击底部的 "**选择**"。 若要深入到嵌套的作用域（如订阅中的资源组），请单击 "作用域名称" 链接。 若要选择任何嵌套级别的父作用域，请单击 "作用域选取器" 顶部的 "**选择此 &lt;范围"&gt;** 。

## <a name="identify-the-resource-id-for-a-scope"></a>标识作用域的资源 ID

使用成本管理 Api 时，知道范围是至关重要的。 使用以下信息为成本管理 Api 生成正确的范围 URI。

### <a name="billing-accounts"></a>计费帐户

1. 打开 Azure 门户，然后在服务列表中导航到 "**成本管理 + 计费**"。
2. 在 "计费帐户" 菜单中选择 "**属性**"。
3. 复制计费帐户 ID。
4. 作用域为： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>计费对象信息

1. 打开 Azure 门户，然后在服务列表中导航到 "**成本管理 + 计费**"。
2. 在 "计费帐户" 菜单中选择 "**计费配置文件**"。
3. 单击所需计费配置文件的名称。
4. 选择 "计费配置文件" 菜单中的 "**属性**"。
5. 复制计费帐户和计费配置文件 Id。
6. 作用域为： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>发票科目

1. 打开 Azure 门户，然后在服务列表中导航到 "**成本管理 + 计费**"。
2. 在 "计费帐户" 菜单中选择 "**发票部分**"。
3. 单击所需发票部分的名称。
4. 在 "发票" 部分菜单中选择 "**属性**"。
5. 复制计费帐户和发票部分 Id。
6. 作用域为： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA 部门

1. 打开 Azure 门户，然后在服务列表中导航到 "**成本管理 + 计费**"。
2. 在 "计费帐户" 菜单中选择 "**部门**"。
3. 单击所需部门的名称。
4. 在 "部门" 菜单中选择 "**属性**"。
5. 复制计费帐户和部门 Id。
6. 作用域为： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA 注册帐户

1. 打开 Azure 门户，并在服务列表中导航到 "**成本管理 + 计费**"。
2. 在 "计费帐户" 菜单中选择 "**注册帐户**"。
3. 单击所需注册帐户的名称。
4. 在 "注册帐户" 菜单中选择 "**属性**"。
5. 复制计费帐户和注册帐户 Id。
6. 作用域为： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>管理组

1. 打开 Azure 门户，导航到服务列表中的 "**管理组**"。
2. 导航到所需的管理组。
3. 从表中复制管理组 ID。
4. 作用域为： `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>订阅

1. 打开 Azure 门户并导航到服务列表中的 "**订阅**"。
2. 从表中复制订阅 ID。
3. 作用域为： `"/subscriptions/{id}"`

### <a name="resource-groups"></a>资源组

1. 打开 Azure 门户，导航到服务列表中的 "**资源组**"。
2. 单击所需资源组的名称。
3. 在 "资源组" 菜单中选择 "**属性**"。
4. 复制 "资源 ID" 字段值。
5. 作用域为： `"/subscriptions/{id}/resourceGroups/{name}"`

[Azure 全球](https://management.azure.com)和[azure 政府](https://management.usgovcloudapi.net)目前支持成本管理。 有关 Azure 政府版的详细信息，请参阅[Azure 全球和政府 API 终结点](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping)。

## <a name="next-steps"></a>后续步骤

- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](quick-acm-cost-analysis.md)。
