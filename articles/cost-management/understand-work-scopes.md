---
title: 了解和使用 Azure 成本管理作用域 |Microsoft Docs
description: 本文可帮助你了解在 Azure 以及如何使用成本管理和 Api 中的作用域中可用的计费和资源管理范围。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 717c0f110ebbeee53e2c9b9207350385288d57c3
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991384"
---
# <a name="understand-and-work-with-scopes"></a>了解并使用范围

本文可帮助你了解在 Azure 以及如何使用成本管理和 Api 中的作用域中可用的计费和资源管理范围。

## <a name="scopes"></a>范围

一个_作用域_是 Azure AD 用户的访问和管理服务的 Azure 资源层次结构中的节点。 大多数 Azure 资源创建和部署到资源组，后者是订阅的一部分。 Microsoft 还提供了具有一定的专业角色来管理帐单数据的 Azure 订阅上的两个层次结构：
- 帐单数据，如付款和发票
- 云服务，例如成本和策略管理

作用域是在其中管理计费数据、 具有角色特有的付款、 查看发票，以及执行常规帐户管理。 计费和帐户的角色分开管理所使用的资源管理，使用该[Azure RBAC](../role-based-access-control/overview.md)。 为了清楚地区分不同的作用域，包括访问控制不同的内容，目的引用了这些作为_计费作用域_并_RBAC 作用域_分别。

## <a name="how-cost-management-uses-scopes"></a>成本管理如何使用作用域

成本的管理的工作原理根本的范围上面的资源，以便组织可以管理他们拥有访问权限，在该级别的成本，无论是整个计费帐户或单个资源组。 尽管 Microsoft 协议 （订阅类型） 上基于不同计费作用域，RBAC 作用域不适用。

## <a name="azure-rbac-scopes"></a>Azure RBAC 作用域

Azure 支持资源管理的三个作用的域。 每个作用域支持管理访问和管理，包括但不是限于，成本管理。

- [**管理组**](../governance/management-groups/index.md) -层次结构的容器，最多八个级别，来组织 Azure 订阅。

    资源类型：[Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **订阅**-Azure 资源的主要容器。

    资源类型：[Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**资源组**](../azure-resource-manager/resource-group-overview.md#resource-groups) -共享相同的生命周期的 Azure 解决方案的相关资源的逻辑分组。 有关示例部署，并一起删除的资源。

    资源类型：[Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

管理组可以组织到层次结构的订阅。 例如，可以创建使用管理组的逻辑组织层次结构。 然后，为团队订阅提供生产和开发/测试工作负荷。 然后在要管理的每个子系统或组件的订阅中创建资源组。

创建表示组织层次结构允许成本和策略符合性汇总组织。 然后，每个领导可以查看和分析其当前的成本。 然后它们可以创建预算来找到错误消费模式和优化顾问建议的最低级别的成本。

授予访问权限来查看成本和根据需要管理成本的配置，例如预算和导出，使用 Azure RBAC 的管理作用域上执行。 使用 Azure RBAC 授予 Azure AD 用户和组访问以执行一组预定义在特定的作用域及更低的角色中定义的操作。 例如，分配到管理组作用域的角色还授予到嵌套的订阅和资源组相同的权限。

成本的管理支持以下内置角色，对于每个以下范围：

- [**所有者**](../role-based-access-control/built-in-roles.md#owner) -可以查看成本和管理所有内容，包括成本配置。
- [**参与者**](../role-based-access-control/built-in-roles.md#contributor) -可以查看成本和管理所有内容，包括成本配置，但不包括访问控制。
- [**读取器**](../role-based-access-control/built-in-roles.md#reader) – 可以查看所有内容，其中包括成本数据和配置，但不能进行任何更改。
- [**成本管理参与者**](../role-based-access-control/built-in-roles.md#cost-management-contributor) – 可以查看成本、 管理成本配置，并查看建议。
- [**成本管理读取者**](../role-based-access-control/built-in-roles.md#cost-management-reader) -可以查看成本数据，成本配置和查看的建议。

成本的管理参与者是推荐的最小特权角色。 它允许创建和管理预算的访问权限的用户，并将导出更有效地监视和报告的成本。 成本管理促成因素，可能还需要其他角色，以支持端到端成本管理方案。 请考虑以下方案：

- **处理超过预算时**– 成本管理 contributors （参与者） 还需要有权创建和/或管理操作组，以自动对超额部分做出反应。 请考虑授予[监视参与者](../role-based-access-control/built-in-roles.md#monitoring-contributor)到资源组包含的操作组时要使用超出预算阈值。 自动执行特定操作需要使用自动化和 Azure Functions 之类的特定服务的其他角色。
- **计划成本数据导出**– 成本管理 contributors （参与者） 还需要管理计划的导出将数据复制到存储帐户的存储帐户的访问权限。 请考虑授予[存储帐户参与者](../role-based-access-control/built-in-roles.md#storage-account-contributor)帐户位置的成本数据导出到包含存储的资源组。
- **查看节约成本的建议**– 成本管理读取器和成本管理 contributors （参与者） 有权访问*视图*默认情况下成本建议。 但是，作用于成本建议的访问权限需要对单个资源的访问。 请考虑授予[特定于服务的角色](../role-based-access-control/built-in-roles.md#built-in-role-descriptions)如果你想要对基于成本的建议。

## <a name="enterprise-agreement-scopes"></a>企业协议作用域

企业协议 (EA) 计费帐户，也称为注册，具有以下作用域：

- [**计费帐户**](../billing/billing-view-all-accounts.md) -表示 EA 注册。 在此作用域生成发票。 在此作用域不是基于使用情况，如 Marketplace 和预订，购买才可用。 它们不表示在部门或注册帐户。

    资源类型： `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **部门**-可选分组的注册帐户。

    资源类型： `Billing/billingAccounts/departments`

- **注册帐户**-表示单个帐户所有者。 不支持授予对多个用户访问权限。

    资源类型： `Microsoft.Billing/billingAccounts/enrollmentAccounts`

管理作用域绑定到单个目录，但不是 EA 计费作用域。 EA 计费帐户可能跨任意数量的 Azure AD 目录拥有订阅。

EA 计费作用域支持以下角色：

- **企业管理员**– 可以管理计费帐户设置和访问权限、 可以查看所有成本和可管理成本配置。 例如，预算跟踪，并将导出。 在函数中，EA 计费作用域是与相同[成本管理参与者 Azure RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-contributor)。
- **企业只读用户**– 可以查看计费帐户设置、 成本数据和成本配置。 例如，预算跟踪，并将导出。 在函数中，EA 计费作用域是与相同[成本管理读取器 Azure RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-reader)。
- **部门管理员**– 可以管理部门设置，如成本中心，并可以访问、 查看所有成本和管理成本配置。 例如，预算跟踪，并将导出。  **DA 查看费用**计费帐户设置必须为启用部门管理员和只读用户要查看的成本。 如果**DA 查看费用**是禁用，部门用户看不到任何级别的成本即使它们是帐户或订阅所有者。
- **部门只读用户**– 可以查看部门设置、 成本数据和成本配置。 例如，预算跟踪，并将导出。 如果**DA 查看费用**是禁用，部门用户看不到任何级别的成本即使它们是帐户或订阅所有者。
- **帐户所有者**– 可以管理注册帐户设置 （如成本中心）、 查看所有成本和管理成本配置 （如预算和导出） 注册帐户。 **AO 查看费用**计费帐户设置必须为启用的帐户所有者和 RBAC 用户要查看的成本。

EA 计费帐户用户不能直接访问发票。 可从外部批量许可系统发票。

Azure 订阅将嵌套在注册帐户下。 计费用户有权访问的订阅和资源组是在其各自的作用域下的成本数据。 它们不具有查看或管理在 Azure 门户中的资源的访问权限。 计费用户可以通过导航到查看成本**成本管理 + 计费**在 Azure 门户的服务列表中。 然后，他们可以筛选到特定的订阅和资源组所需报告的成本。

计费用户没有对管理组的访问，因为它们不属于显式特定计费帐户。 必须显式到管理组授予访问权限。 管理组所有嵌套的订阅中的汇总成本。 但是，它们仅包括基于使用情况的购买。 它们不包括如保留项和第三方 Marketplace 产品/服务购买。 若要查看这些成本，请使用 EA 计费帐户。

## <a name="individual-agreement-pay-as-you-go-scopes"></a>单独的协议 （即用即付） 作用域

即用即付 (PAYG) 订阅，包括相关的类型如免费/试用版和开发/测试产品/服务，没有显式的计费帐户作用域。 相反，每个订阅具有的帐户所有者或帐户管理员，如 EA 帐户所有者。

- [**计费帐户**](../billing/billing-view-all-accounts.md) -表示为一个或多个 Azure 订阅的单个帐户所有者。 它当前不支持多个人员或对聚合的成本视图访问权限授予访问权限。

    资源类型：不适用

PAYG 订阅帐户管理员可以查看和管理计费数据，如发票和付款、 从[Azure 帐户中心](https://account.azure.com/subscriptions)。 但是，它们不能查看成本数据或管理在 Azure 门户中的资源。 若要授予访问权限的帐户管理员，使用前面提到的成本管理角色。

与 EA，不同 PAYG 订阅帐户管理员可以看到其在 Azure 门户中的发票。 请记住，成本管理读取者和成本管理参与者角色不提供发票的访问权限。 有关详细信息，请参阅[如何授予访问 PAYG 发票](../billing/billing-manage-access.md#give-access-to-billing)。

## <a name="customer-agreement-scopes"></a>客户协议作用域

Microsoft 客户协议计费帐户具有以下作用域：

- **计费帐户**-表示多个 Microsoft 产品和服务的客户协议。 客户协议计费帐户不是功能上与 EA 注册相同。 EA 注册计费的配置文件更紧密地对齐。

    资源类型： `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **计费配置文件**-定义包含在发票中的订阅。 计费配置文件是在功能上等同的 EA 注册，因为这是在生成发票的作用域。 同样，不是基于使用情况 （如 Marketplace 和预订） 的购买可仅在此作用域。 它们不包含在发票部分。

    资源类型： `Microsoft.Billing/billingAccounts/billingProfiles`

- **发票部分**-表示订阅的发票或计费的配置文件中的一组。 发票部分如下所示部门-多个人员可以向发票内容拥有访问权限。

    资源类型： `Microsoft.Billing/billingAccounts/invoiceSections`

与 EA 不同计费作用域，计费帐户的客户协议_是_绑定到单个目录，并且不能跨多个 Azure AD 目录拥有订阅。

客户协议计费作用域支持以下角色：

- **所有者**– 可以管理的计费设置和访问、 查看所有成本和管理成本配置。 例如，预算跟踪，并将导出。 在函数中，此客户协议计费作用域是与相同[成本管理参与者 Azure RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-contributor)。
- **参与者**-可管理除访问权限以外的计费设置、 查看所有成本和管理成本配置。 例如，预算跟踪，并将导出。 在函数中，此客户协议计费作用域是与相同[成本管理参与者 Azure RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-contributor)。
- **读取器**– 可以查看帐单设置、 成本数据和成本配置。 例如，预算跟踪，并将导出。 在函数中，此客户协议计费作用域是与相同[成本管理读取器 Azure RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-reader)。
- **发票管理器**– 可以查看和支付发票并查看成本数据和配置。 例如，预算跟踪，并将导出。 在函数中，此客户协议计费作用域是与相同[成本管理读取器 Azure RBAC 角色](../role-based-access-control/built-in-roles.md#cost-management-reader)。
- **Azure 订阅创建者**-可以创建 Azure 订阅、 查看成本和管理成本配置。 例如，预算跟踪，并将导出。 在函数中，此客户协议计费作用域是与 EA 注册帐户所有者角色相同。

Azure 订阅都嵌套在发票节，就像它们正在 EA 注册帐户下。 计费用户有权访问的订阅和资源组下其各自的范围的成本数据。 但是，它们不能查看或管理在 Azure 门户中的资源的访问权限。 计费用户可以通过导航到查看成本**成本管理 + 计费**在 Azure 门户的服务列表中。 然后，筛选到特定的订阅和资源组所需报告的成本。

计费用户没有对管理组的访问，因为它们不显式属于计费帐户。 但是，为组织启用的管理组后，所有订阅成本是汇总的计费帐户和根管理组因为它们被限制到单个目录。 管理组只包括基于使用情况的购买。 保留项和第三方 Marketplace 产品/服务等的购买不包括在管理组。 因此，计费帐户和根管理组可能会报告不同的总计。 若要查看这些成本，请使用对应的计费个人的计费帐户。

## <a name="cloud-solution-provider-csp-scopes"></a>云解决方案提供商 (CSP) 作用域

云解决方案提供商 (CSP) 合作伙伴不是目前支持在成本管理中。 相反，可以使用[合作伙伴中心](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)。

## <a name="switch-between-scopes-in-cost-management"></a>切换在成本管理中的作用域

在 Azure 门户中的所有成本管理视图都包括**作用域**药丸在左上方的视图。 使用它来快速更改作用域。 单击**作用域**药丸来打开范围选择器。 它显示计费帐户，根管理组，并不嵌套在根管理组下的任何订阅。 若要选择一个作用域，单击背景来突出显示它，然后单击**选择**底部。 要向下钻入到嵌套作用域，例如资源组在订阅中，单击作用域名称链接。 若要选择任何嵌套级别的父作用域，请单击**选择此选项&lt;作用域&gt;** 范围选取器的顶部。

## <a name="identify-the-resource-id-for-a-scope"></a>标识作用域的资源 ID

使用成本管理 Api 时，了解作用域关键。 使用以下信息的成本管理 Api 生成适当的范围 URI。

### <a name="billing-accounts"></a>结算帐户

1. 打开 Azure 门户，然后导航到**成本管理 + 计费**在服务列表中。
2. 选择**属性**计费帐户菜单中。
3. 复制计费帐户 id。
4. 你的作用域是： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>计费对象信息

1. 打开 Azure 门户，然后导航到**成本管理 + 计费**在服务列表中。
2. 选择**计费配置文件**计费帐户菜单中。
3. 单击所需的计费配置文件的名称。
4. 选择**属性**计费的配置文件菜单中。
5. 将复制的计费帐户和计费配置文件 Id。
6. 你的作用域是： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>发票科目

1. 打开 Azure 门户，然后导航到**成本管理 + 计费**在服务列表中。
2. 选择**发票部分**计费帐户菜单中。
3. 单击所需的发票部分的名称。
4. 选择**属性**发票部分菜单中。
5. 将复制的计费帐户和发票部分 Id。
6. 你的作用域是： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>EA 部门

1. 打开 Azure 门户，然后导航到**成本管理 + 计费**在服务列表中。
2. 选择**部门**计费帐户菜单中。
3. 单击所需的部门的名称。
4. 选择**属性**部门菜单中。
5. 将复制的计费帐户和部门 Id。
6. 你的作用域是： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>EA 注册帐户

1. 打开 Azure 门户并导航到**成本管理 + 计费**在服务列表中。
2. 选择**注册帐户**计费帐户菜单中。
3. 单击所需的注册帐户的名称。
4. 选择**属性**注册帐户菜单中。
5. 将复制的计费帐户和注册帐户 Id。
6. 你的作用域是： `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>管理组

1. 打开 Azure 门户并导航到**管理组**在服务列表中。
2. 导航到所需的管理组。
3. 从表中复制的管理组 ID。
4. 你的作用域是： `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>订阅

1. 打开 Azure 门户并导航到**订阅**在服务列表中。
2. 从表中复制订阅 ID。
3. 你的作用域是： `"/subscriptions/{id}"`

### <a name="resource-groups"></a>资源组

1. 打开 Azure 门户并导航到**资源组**在服务列表中。
2. 单击所需的资源组的名称。
3. 选择**属性**资源组菜单中。
4. 将复制的资源 ID 字段值。
5. 你的作用域是： `"/subscriptions/{id}/resourceGroups/{name}"`

成本的管理中当前支持[Azure 全球](https://management.azure.com)并[Azure 政府版](https://management.usgovcloudapi.net)。 了解 Azure 政府版的详细信息，请参阅[Azure 全局和政府 API 终结点](../azure-government/documentation-government-developer-guide.md#endpoint-mapping)_。_

## <a name="next-steps"></a>后续步骤

- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](quick-acm-cost-analysis.md)。
