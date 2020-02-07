---
title: 了解和使用 Azure 成本管理范围
description: 本文帮助你了解 Azure 中提供的计费和资源管理范围，以及如何在成本管理和 API 中使用范围。
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "75990133"
---
# <a name="understand-and-work-with-scopes"></a>了解和使用范围

本文帮助你了解 Azure 中提供的计费和资源管理范围，以及如何在成本管理和 API 中使用范围。

## <a name="scopes"></a>作用域

范围是 Azure 资源层次结构中的一个节点，Azure AD 用户在其中访问和管理服务。  大多数 Azure 资源在创建后将部署到属于订阅的一部分的资源组中。 在提供专用角色管理计费数据的 Azure 订阅上方，Microsoft 还会提供两个层次结构：
- 计费数据，例如付款和发票
- 云服务，例如成本和策略监管

你将在特定的范围内管理计费数据、获取特定于付款的角色、查看发票，以及执行常规的帐户管理。 对计费和帐户角色的管理独立于用于资源管理的范围（使用 [Azure RBAC](../../role-based-access-control/overview.md)）的管理。 为了明确区分不同范围的意图（包括访问控制的差异），这些范围分别称作计费范围和 RBAC 范围。  

## <a name="how-cost-management-uses-scopes"></a>成本管理如何使用范围

成本管理在资源上方的所有范围内工作，使组织能够在它们有权访问的级别管理成本，无论该级别是整个计费帐户还是单个资源组。 计费范围根据 Microsoft 协议（订阅类型）而异，但 RBAC 范围并非如此。

## <a name="azure-rbac-scopes"></a>Azure RBAC 范围

Azure 支持用于资源管理的三个范围。 每个范围支持管理访问权限和监管，包括但不限于成本管理。

- [**管理组**](../../governance/management-groups/overview.md) - 用于组织 Azure 订阅的分层容器，最多有八个级别。

    资源类型：[Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **订阅** - Azure 资源的主容器。

    资源类型：[Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**资源组**](../../azure-resource-manager/management/overview.md#resource-groups) - 共享相同生命周期的 Azure 解决方案的相关资源的逻辑分组。 例如，一同部署和删除的资源。

    资源类型：[Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

使用管理组可将订阅组织到层次结构中。 例如，可以使用管理组来创建逻辑组织层次结构。 然后，为团队提供用于生产和开发/测试工作负荷的订阅。 接下来，在订阅中创建用于管理每个子系统或组件的资源组。

创建组织层次结构能够以组织有序的方式推行成本和策略合规性计划。 然后，每个主管可以查看和分析其当前成本。 他们可以创建预算来抑制不良的支出模式，并在最低级别根据顾问的建议优化成本。

授予查看成本和（可选）管理成本配置（例如预算和导出）的访问权限是使用 Azure RBAC 在监管范围执行的。 可以使用 Azure RBAC 为 Azure AD 用户和组授予访问权限，以便在特定范围和更低的范围执行角色中预定义的一组操作。 例如，分配到管理组范围的角色还可以向嵌套的订阅和资源组授予相同的权限。

对于以下每个范围，Azure 成本管理支持以下内置角色：

- [**所有者**](../../role-based-access-control/built-in-roles.md#owner) – 可以查看成本和管理所有内容（包括成本配置）。
- [**参与者**](../../role-based-access-control/built-in-roles.md#contributor) – 可以查看成本和管理所有内容（包括成本配置，但不包括访问控制）。
- [**读取者**](../../role-based-access-control/built-in-roles.md#reader) – 可以查看所有内容（包括成本数据和配置），但无法进行任何更改。
- [**成本管理参与者**](../../role-based-access-control/built-in-roles.md#cost-management-contributor) – 可以查看成本、管理成本配置和查看建议。
- [**成本管理读取者**](../../role-based-access-control/built-in-roles.md#cost-management-reader) – 可以查看成本数据、成本配置和建议。

成本管理参与者是建议的最低特权角色。 此角色授予创建和管理预算和导出的权限，以便更有效地监视和报告成本。 成本管理参与者还可能需要有其他角色才能支持端到端的成本管理方案。 请考虑下列情形：

- **超出预算时采取措施** – 成本管理参与者还需要拥有创建和/或管理操作组以自动对超额问题做出反应的访问权限。 请考虑将[监视参与者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)授予某个资源组，该资源组包含超出预算阈值时要使用的操作组。 自动执行特定的操作需要所用特定服务（例如自动化和 Azure Functions）的其他角色。
- **计划成本数据导出** – 成本管理参与者还需要以下访问权限：管理存储帐户，以计划通过导出将数据复制到存储帐户。 请考虑将[存储帐户参与者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)授予某个资源组，该资源组包含要将成本数据导出到的存储帐户。
- **查看成本节省建议** – 默认情况下，成本管理读取者和成本管理参与者拥有查看成本建议的访问权限。  但是，需要访问各个资源才能获得处理成本建议的访问权限。 若要处理基于成本的建议，请考虑授予[服务特定的角色](../../role-based-access-control/built-in-roles.md#built-in-role-descriptions)。

## <a name="enterprise-agreement-scopes"></a>企业协议范围

企业协议 (EA) 计费帐户（也称为“注册”）具有以下范围：

- [**计费帐户**](../manage/view-all-accounts.md) - 代表 EA 注册。 发票就是在此范围生成的。 不是基于用量的购买内容（例如市场项和预留项）只能在此范围使用。 部门或注册帐户中不会反映这些购买内容。

    资源类型：`Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **部门** - 注册帐户的可选分组。

    资源类型：`Billing/billingAccounts/departments`

- **注册帐户** - 代表单个帐户所有者。 不支持向多个用户授予访问权限。

    资源类型：`Microsoft.Billing/billingAccounts/enrollmentAccounts`

监管范围绑定到单个目录，但 EA 计费范围并非如此。 一个 EA 计费帐户可以包含任意数量的 Azure AD 目录中的订阅。

EA 计费范围支持以下角色：

- **企业管理员** – 可以管理计费帐户设置和访问权限，可以查看所有成本，并可以管理成本配置。 例如预算和导出。 在功能方面，EA 计费范围与[成本管理参与者 Azure RBAC 角色](../../role-based-access-control/built-in-roles.md#cost-management-contributor)相同。
- **企业只读用户** – 可以查看计费帐户设置、成本数据和成本配置。 例如预算和导出。 在功能方面，EA 计费范围与[成本管理读取者 Azure RBAC 角色](../../role-based-access-control/built-in-roles.md#cost-management-reader)相同。
- **部门管理员** – 可以管理部门设置（例如成本中心），可以访问和查看所有成本，并可以管理成本配置。 例如预算和导出。  必须启用“DA 查看费用”计费帐户设置，才能让部门管理员和只读用户查看成本。  如果禁用了“DA 查看费用”，则即使部门用户是帐户或订阅所有者，他们也无法在任何级别查看成本。 
- **部门只读用户** – 可以查看部门设置、成本数据和成本配置。 例如预算和导出。 如果禁用了“DA 查看费用”，则即使部门用户是帐户或订阅所有者，他们也无法在任何级别查看成本。 
- **帐户所有者** – 可以管理注册帐户设置（例如成本中心）、查看所有成本，以及管理注册帐户的成本配置（例如预算和导出）。 必须启用“AO 查看费用”计费帐户设置，才能让帐户所有者和 RBAC 用户查看成本。 

EA 计费帐户用户无法直接访问发票。 可以从外部批量授权系统访问发票。

Azure 订阅嵌套在注册帐户下。 计费用户有权在其各自范围下访问订阅和资源组的成本数据。 他们无权在 Azure 门户中查看或管理资源。 计费用户可以通过导航到 Azure 门户服务列表中的“成本管理 + 计费”来查看成本。  然后，他们可以按照需要报告的特定订阅和资源组筛选成本。

计费用户无权访问管理组，因为他们并不显式属于特定的计费帐户。 必须向管理组显式授予访问权限。 管理组从所有嵌套订阅汇总成本。 但是，它们只包含基于用量的购买内容。 它们不包含预留项和第三方市场套餐等购买内容。 若要查看这些成本，请使用 EA 计费帐户。

## <a name="individual-agreement-scopes"></a>单个协议范围

从单个套餐创建的 Azure 订阅（例如即用即付套餐，以及免费试用和开发/测试等相关套餐类型）没有显式的计费帐户范围。 而是每个订阅都有一个帐户所有者或帐户管理员，例如 EA 帐户所有者。

- [**计费帐户**](../manage/view-all-accounts.md) - 代表一个或多个 Azure 订阅的单个帐户所有者。 它目前不支持授予向多个人员授予访问权限，或授予聚合成本视图的访问权限。

    资源类型：不适用

个人 Azure 订阅帐户管理员可以在 [Azure 帐户中心](https://account.azure.com/subscriptions)查看和管理计费数据，例如发票和付款。 但是，他们无法在 Azure 门户中查看成本数据或管理资源。 若要向帐户管理员授予访问权限，请使用前面所述的成本管理角色。

与 EA 不同，个人 Azure 订阅帐户管理员可以在 Azure 门户中查看其发票。 请记住，成本管理读取者和成本管理参与者角色不提供对发票的访问权限。 有关详细信息，请参阅[如何授予对发票的访问权限](../manage/manage-billing-access.md#give-read-only-access-to-billing)。

## <a name="microsoft-customer-agreement-scopes"></a>Microsoft 客户协议范围

Microsoft 客户协议计费帐户具有以下范围：

- **计费帐户** - 代表多个 Microsoft 产品和服务的客户协议。 客户协议计费帐户在功能上与 EA 注册不同。 EA 注册更接近于计费配置文件。

    资源类型：`Microsoft.Billing/billingAccounts (accountType = Organization)`

- **计费配置文件** - 定义发票中包含的订阅。 计费配置文件在功能上等同于 EA 注册，因为它就是生成发票时所在的范围。 同样，不是基于用量的购买内容（例如市场项和预留项）只能在此范围使用。 它们不会包含在发票科目中。

    资源类型：`Microsoft.Billing/billingAccounts/billingProfiles`

- **发票科目** - 代表发票或计费配置文件中的一组订阅。 发票科目类似于部门 — 多个人员可以访问某个发票科目。

    资源类型：`Microsoft.Billing/billingAccounts/invoiceSections`

- **客户** - 代表关联到特定客户的一组订阅，该客户已由合作伙伴加入到 Microsoft 客户协议。 此范围特定于 CSP。

与 EA 计费范围不同，客户协议计费帐户绑定到单个目录，不能包含多个 Azure AD 目录中的订阅。 

客户协议计费范围不适用于合作伙伴。 [分配用户角色和权限](/partner-center/permissions-overview)中介绍了合作伙伴角色和权限。

客户协议计费范围支持以下角色：

- **所有者** – 可以管理计费设置和访问权限、查看所有成本，以及管理成本配置。 例如预算和导出。 在功能方面，此客户协议计费范围与[成本管理参与者 Azure RBAC 角色](../../role-based-access-control/built-in-roles.md#cost-management-contributor)相同。
- **参与者** – 可以管理计费设置（访问权限除外）、查看所有成本，以及管理成本配置。 例如预算和导出。 在功能方面，此客户协议计费范围与[成本管理参与者 Azure RBAC 角色](../../role-based-access-control/built-in-roles.md#cost-management-contributor)相同。
- **读取者** – 可以查看计费设置、成本数据和成本配置。 例如预算和导出。 在功能方面，此客户协议计费范围与[成本管理读取者 Azure RBAC 角色](../../role-based-access-control/built-in-roles.md#cost-management-reader)相同。
- **发票管理者** – 可以查看和支付发票，并可以查看成本数据和配置。 例如预算和导出。 在功能方面，此客户协议计费范围与[成本管理读取者 Azure RBAC 角色](../../role-based-access-control/built-in-roles.md#cost-management-reader)相同。
- **Azure 订阅创建者** – 可以创建 Azure 订阅、查看成本和管理成本配置。 例如预算和导出。 在功能方面，此客户协议计费范围与 EA 注册帐户所有者角色相同。

Azure 订阅嵌套在发票科目下，就如同嵌套在 EA 注册帐户下一样。 计费用户有权在其各自范围下访问订阅和资源组的成本数据。 但是，他们无权在 Azure 门户中查看或管理资源。 计费用户可以通过导航到 Azure 门户服务列表中的“成本管理 + 计费”来查看成本。  然后，按照需要报告的特定订阅和资源组筛选成本。

计费用户无权访问管理组，因为他们并不显式属于计费帐户。 但是，为组织启用管理组后，所有订阅成本将汇总到计费帐户和根管理组，因为两者均限制为单个目录。 管理组仅包含基于用量的购买内容。 预留项和第三方市场套餐等购买内容不会包含在管理组中。 因此，计费帐户和根管理组可能会报告不同的总计。 若要查看这些成本，请使用计费帐户或相应的计费配置文件。

## <a name="aws-scopes"></a>AWS 范围

完成 AWS 集成后，请参阅[设置和配置 AWS 集成](aws-integration-set-up-configure.md)。 以下范围可用：

- **外部计费帐户** - 表示与第三方供应商签署的客户协议。 它类似于 EA 计费帐户。

    资源类型：`Microsoft.CostManagement/externalBillingAccounts`

- **外部订阅** - 代表第三方供应商的客户操作帐户。 它类似于 Azure 订阅。

    资源类型：`Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>云解决方案提供商 (CSP) 范围

具有 Microsoft 客户协议客户的 CSP 支持以下范围：

- **计费帐户** - 代表多个 Microsoft 产品和服务的客户协议。 客户协议计费帐户在功能上与 EA 注册不同。 EA 注册更接近于计费配置文件。

    资源类型：`Microsoft.Billing/billingAccounts (accountType = Organization)`

- **计费配置文件** - 定义发票中包含的订阅。 计费配置文件在功能上等同于 EA 注册，因为它就是生成发票时所在的范围。 同样，不是基于用量的购买内容（例如市场项和预留项）只能在此范围使用。

    资源类型：`Microsoft.Billing/billingAccounts/billingProfiles`

- **客户** - 代表关联到特定客户的一组订阅，该客户已由合作伙伴加入到 Microsoft 客户协议。

只有具有“全局管理员”和“管理员代理”角色的用户才能直接在合作伙伴的 Azure 租户中管理和查看计费帐户、计费配置文件与客户的成本。   有关合作伙伴中心角色的详细信息，请参阅[分配用户角色和权限](/partner-center/permissions-overview)。

仅当 CSP 合作伙伴客户签署了 Microsoft 客户协议时，Azure 成本管理才支持这些客户。 有关 CSP 支持的但尚未签署 Microsoft 客户协议的客户，请参阅[合作伙伴中心](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)。

## <a name="switch-between-scopes-in-cost-management"></a>在成本管理中切换范围

Azure 门户中所有成本管理视图的左上角都包含一个“范围”选项椭圆图标。  使用该图标可以快速更改范围。 单击“范围”椭圆图标打开范围选取器。  其中显示了计费帐户、根管理组，以及未嵌套在根管理组下的任何订阅。 若要选择范围，请单击背景将其突出显示，然后单击底部的“选择”。  若要深入到嵌套的范围（例如订阅中的资源组），请单击范围名称链接。 若要选择位于任一嵌套级别的父范围，请单击范围选取器顶部的“选择此 &lt;范围&gt;”。 

## <a name="identify-the-resource-id-for-a-scope"></a>标识范围的资源 ID

使用成本管理 API 时，了解范围是非常重要的。 使用以下信息为成本管理 API 生成正确的范围 URI。

### <a name="billing-accounts"></a>计费帐户

1. 打开 Azure 门户，然后导航到服务列表中的“成本管理 + 计费”。 
2. 在计费帐户菜单中选择“属性”。 
3. 复制计费帐户 ID。
4. 范围为：`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>计费对象信息

1. 打开 Azure 门户，然后导航到服务列表中的“成本管理 + 计费”。 
2. 在计费帐户菜单中选择“计费配置文件”。 
3. 单击所需计费配置文件的名称。
4. 在计费配置文件菜单中选择“属性”。 
5. 复制计费帐户和计费配置文件 ID。
6. 范围为：`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>发票科目

1. 打开 Azure 门户，然后导航到服务列表中的“成本管理 + 计费”。 
2. 在计费帐户菜单中选择“发票科目”。 
3. 单击所需发票科目的名称。
4. 在发票科目菜单中选择“属性”。 
5. 复制计费帐户和发票科目 ID。
6. 范围为：`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA 部门

1. 打开 Azure 门户，然后导航到服务列表中的“成本管理 + 计费”。 
2. 在计费帐户菜单中选择“部门”。 
3. 单击所需部门的名称。
4. 在部门菜单中选择“属性”。 
5. 复制计费帐户和部门 ID。
6. 范围为：`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA 注册帐户

1. 打开 Azure 门户，然后导航到服务列表中的“成本管理 + 计费”。 
2. 在计费帐户菜单中选择“注册帐户”。 
3. 单击所需注册帐户的名称。
4. 在注册帐户菜单中选择“属性”。 
5. 复制计费帐户和注册帐户 ID。
6. 范围为：`"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>管理组

1. 打开 Azure 门户，然后导航到服务列表中的“管理组”。 
2. 导航到所需的管理组。
3. 复制表中的管理组 ID。
4. 范围为：`"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>订阅

1. 打开 Azure 门户，然后导航到服务列表中的“订阅”。 
2. 复制表中的订阅 ID。
3. 范围为：`"/subscriptions/{id}"`

### <a name="resource-groups"></a>资源组

1. 打开 Azure 门户，然后导航到服务列表中的“资源组”。 
2. 单击所需资源组的名称。
3. 在资源组菜单中选择“属性”。 
4. 复制资源 ID 字段值。
5. 范围为：`"/subscriptions/{id}/resourceGroups/{name}"`

[Azure 公有云](https://management.azure.com)和 [Azure 政府版](https://management.usgovcloudapi.net)目前支持成本管理。 有关 Azure 政府版的详细信息，请参阅 [Azure 公有云和政府版 API 终结点](../../azure-government/documentation-government-developer-guide.md#endpoint-mapping)。

## <a name="next-steps"></a>后续步骤

- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](quick-acm-cost-analysis.md)。
