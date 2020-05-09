---
title: Azure Application Insights 中的资源、角色和访问控制 | Microsoft Docs
description: 组织的见解的所有者、参与者和读者。
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: ee14fee66e3aa6f4e8d999f6f98debe361387515
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628221"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Application Insights 中的资源、角色和访问控制

通过使用 [Microsoft Azure 中基于角色的访问控制](../../role-based-access-control/role-assignments-portal.md)，可以控制哪些用户对 Azure [Application Insights][start] 中的数据具有读取和更新访问权限。

> [!IMPORTANT]
> 将访问权限分配给应用程序资源所属**资源组或订阅**中的用户（并非资源本身）。 分配 **Application Insights 组件参与者**角色。 这可确保对 Web 测试和警报及应用程序资源的访问实现统一控制。 [了解详细信息](#access)。


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>资源、组和订阅

首先，某些定义如下：

* **资源** - Microsoft Azure 服务的实例。 Application Insights 资源会收集、分析并显示从应用程序发送的遥测数据。  其他类型的 Azure 资源包括 Web 应用、数据库和 VM。
  
    要查看资源，请打开 [Azure 门户][portal]并登录，然后单击“所有资源”。 为找到资源，请在筛选器字段中键入部分名称。
  
    ![Azure 资源的列表](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**资源组**][group] - 每个资源都属于一个组。 组是一种管理相关资源的简便方式，对于访问控制尤其如此。 例如，可以将 Web 应用放入资源组中，监视应用和存储资源以保存导出的数据。

* [**订阅**](https://portal.azure.com) - 若要使用 Application Insights 或其他 Azure 资源，则登录到 Azure 订阅。 每个资源组都属于一个 Azure 订阅，从中选择价格封装，并选择成员及其访问权限（如果它是组织订阅）。
* [**Microsoft 帐户**][account] - 用于登录到 Microsoft Azure 订阅、XBox Live、Outlook.com 和其他 Microsoft 服务的用户名和密码。

## <a name="control-access-in-the-resource-group"></a><a name="access"></a> 控制资源组中的访问

请务必了解，除了为应用程序创建的资源外，也有为警报和 Web 测试单独隐藏的资源。 它们会附加到与 Application Insights 资源相同的[资源组](#resource-group)。 还可以在其中放置其他 Azure 服务，例如网站或存储。

## <a name="to-provide-access-to-another-user"></a>对其他用户提供访问权限

必须对订阅或资源组拥有所有者权限。

用户必须具有 [Microsoft 帐户][account]，或对[组织 Microsoft 帐户](../../active-directory/fundamentals/sign-up-organization.md)的访问权限。 可以将访问权限提供给个人，也可以提供给 Azure Active Directory 中定义的用户组。

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>导航到资源组或直接导航到该资源本身

从左侧菜单中选择“访问控制 (IAM)”****。

![Azure 门户中的访问控制按钮的屏幕截图](./media/resources-roles-access-control/0001-access-control.png)

选择**添加角色分配**

![以红色突出显示添加按钮的访问控制菜单的屏幕截图](./media/resources-roles-access-control/0002-add.png)

下面的“添加权限”**** 视图主要特定于 Application Insights 资源，如果从更高级别（如资源组）查看访问控制权限，则将看到其他并非以 Application Insights 为中心的角色。

若要查看有关所有基于 Azure 角色的访问控制内置角色的信息，请使用[官方参考内容](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)。

![访问控制用户角色列表的屏幕截图](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>选择角色

如适用，我们将链接到相关联的官方参考文档。

| 角色 | 在资源组中 |
| --- | --- |
| [所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) |可以更改任何内容，包括用户访问权限。 |
| [参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) |可以编辑任何内容，包括所有资源。 |
| [Application Insights 组件参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-component-contributor) |可以编辑 Application Insights 资源。 |
| [读取者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) |可以查看但无法更改任何内容。 |
| [Application Insights 快照调试器](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#application-insights-snapshot-debugger) | 授予用户使用 Application Insights 快照调试器功能的权限。 请注意，此角色既不包含在所有者角色中，也不包含在参与者角色中。 |
| Azure 服务部署版本管理参与者 | 通过 Azure 服务部署进行部署的服务的参与者角色。 |
| [数据清除程序](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#data-purger) | 清除个人数据的特殊角色。 有关更多信息，请参阅我们的[个人数据指南](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data)。   |
| ExpressRoute 管理员 | 可以创建删除和管理快速路由。|
| [Log Analytics 参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-contributor) | Log Analytics 参与者可以读取所有监视数据并编辑监视设置。 编辑监视设置包括向 VM 添加 VM 扩展、读取存储帐户密钥以便能够从 Azure 存储配置日志收集、创建和配置自动化帐户、添加解决方案以及配置所有 Azure 资源上的 Azure 诊断。  |
| [Log Analytics 读者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader) | Log Analytics 读者可以查看和搜索所有监视数据并查看监视设置，其中包括查看所有 Azure 资源上的 Azure 诊断的配置。 |
| masterreader | 允许用户查看所有内容，但不能进行更改。 |
| [监视参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) | 允许读取所有监视数据并更新监视设置。|
| [监视指标发布者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-metrics-publisher) | 允许针对 Azure 资源发布指标。 |
| [监视查阅者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) | 可以读取所有监视数据。 |
| 资源策略参与者（预览） | 通过 EA 回填的用户，具有创建/修改资源策略、创建支持票证和读取资源/层次结构的权限。  |
| [用户访问管理员](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) | 允许用户管理其他用户对 Azure 资源的访问。|
| [网站参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#website-contributor) | 允许管理网站（而非 Web 计划），但不允许访问这些网站。|

“编辑”包括创建、删除和更新：

* 资源
* Web 测试
* 警报
* 连续导出

#### <a name="select-the-user"></a>选择用户

如果希望的用户不在目录中，可以邀请具有 Microsoft 帐户的任何人。
（如果他们使用诸如 Outlook.com、OneDrive、Windows Phone 或 XBox Live 等服务，他们就会有 Microsoft 帐户。）

## <a name="related-content"></a>相关内容

* [Azure 中基于角色的访问控制](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>PowerShell 查询确定角色成员身份

由于某些角色可以链接到通知和电子邮件警报，因此能够生成属于给定角色的用户列表会很有帮助。 为了帮助生成这些类型的列表，我们提供了以下示例查询，可以根据特定需求进行调整：

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>为管理员角色 + 参与者角色查询整个订阅

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>在所有者和参与者的特定 Application Insights 资源的上下文中进行查询

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>在所有者和参与者的特定资源组的上下文中进行查询

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md
