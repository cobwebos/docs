---
title: 跨租户管理体验
description: Azure 委派资源管理可实现跨租户管理体验。
ms.date: 02/14/2020
ms.topic: conceptual
ms.openlocfilehash: cb484ea936bbb64b3ca3d7fcf648de0d0ef73c66
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78328674"
---
# <a name="cross-tenant-management-experiences"></a>跨租户管理体验

作为服务提供商，可以使用 [Azure 委派资源管理](../concepts/azure-delegated-resource-management.md)，以便在 [Azure 门户](https://portal.azure.com)中为租户内的多个客户管理 Azure 资源。 大多数任务和服务都可以跨托管租户在委派的 Azure 资源上执行。 本文介绍了一些可实现有效 Azure 委托资源管理的增强方案。

> [!NOTE]
> 还可以[在具有多个 Azure AD 租户的企业内](enterprise.md)使用 Azure 委派的资源管理来简化跨租户管理。

## <a name="understanding-customer-tenants"></a>理解客户租户

Azure Active Directory (Azure AD) 租户表示组织。 它是组织通过注册 Azure、Microsoft 365 或其他服务与 Microsoft 建立关系时接收的 Azure AD 的专用实例。 每个 Azure AD 租户都是独特的，独立于其他 Azure AD 租户，且具有其自己的租户 ID (GUID)。 有关详细信息，请参阅[什么是 Azure Active Directory？](../../active-directory/fundamentals/active-directory-whatis.md)

通常，为管理客户的 Azure 资源，服务提供商必须使用与该客户的租户相关联的帐户登录 Azure 门户，要求客户租户中的管理员为该服务提供商创建和管理用户帐户。

使用 Azure 委派资源管理时，加入过程会指定服务提供商租户中能够访问和管理客户租户中的订阅、资源组和资源的用户。 然后，这些用户可以使用自己的凭据登录到 Azure 门户。 在 Microsoft Azure 门户中，这些用户可以管理其有权访问的所有客户的资源。 为此，可以访问 Microsoft Azure 门户中的[我的客户](../how-to/view-manage-customers.md)页，或直接在该客户订阅的上下文中工作（在 Azure 门户中或通过 API）。

Azure 委派资源管理可更灵活地管理多个客户的资源，而无需登录到不同租户中的不同帐户。 例如，服务提供商可能有三个客户，各自具有不同的责任和访问级别，如下所示：

![显示服务提供者责任的三个客户租户](../media/azure-delegated-resource-management-customer-tenants.jpg)

使用 Azure 委派资源管理，授权用户可以登录到服务提供商的租户来访问这些资源，如下所示：

![通过一个服务提供商租户管理的客户资源](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API 和管理工具支持

可以直接在门户中对委派资源执行管理任务，也可以使用 API 和管理工具（如 Azure CLI 和 Azure PowerShell）对委派资源执行管理任务。 在处理委托的资源时，只要跨租户管理支持该功能，并且用户具有相应的权限，可以使用所有现有 API。

我们还提供用于执行 Azure 委派资源管理任务的 API。 有关详细信息，请参阅“参考”部分。

## <a name="enhanced-services-and-scenarios"></a>增强的服务和方案

大多数任务和服务都可对跨托管租户的委托资源执行。 下面是可有效进行跨租户管理的一些关键方案。

[用于服务器的 Azure Arc（预览版）](../../azure-arc/servers/overview.md)：

- [将 Azure 外部的 Windows Server 或 Linux 计算机连接](../../azure-arc/servers/quickstart-onboard-portal.md)到 Azure 中的委派订阅和/或资源组
- 使用 Azure 构造（如Azure Policy 和标记）管理连接的计算机

[Azure 自动化](../../automation/index.yml)：

- 通过自动化帐户来访问和使用委派的客户资源

[Azure 备份](../../backup/index.yml)：

- 备份并还原客户租户中的客户数据
- 使用[备份资源管理器](../../backup/monitor-azure-backup-with-backup-explorer.md)来帮助查看备份项（包括尚未针对备份配置的 Azure 资源）的操作信息，以及为委托订阅监视信息（作业和警报）。 备份资源管理器当前仅对 Azure VM 数据可用。

[Azure Kubernetes 服务 (AKS)](../../aks/index.yml)：

- 管理托管的 Kubernetes 环境并部署和管理客户租户中的容器化应用程序

[Azure Monitor](../../azure-monitor/index.yml)：

- 查看委派订阅的警报，并能够查看所有订阅的警报
- 查看委派订阅的活动日志详细信息
- Log analytics：从多个租户中的远程客户工作区查询数据
- 通过 Webhook 在服务提供商租户中创建触发自动化（例如 Azure Automation Runbook 或 Azure Functions）的客户租户警报

[Azure Policy](../../governance/policy/index.yml)：

- 符合性快照显示委派订阅中分配的策略的详细信息
- 在委派订阅中创建和编辑策略定义
- 在委派订阅中分配客户定义的策略定义
- 客户将看到由服务提供商和自己创建的策略
- 可以[修正 deployIfNotExists 或修改客户租户内的分配](../how-to/deploy-policy-remediation.md)

[Azure Resource Graph](../../governance/resource-graph/index.yml)：

- 现在，在返回的查询结果中包含租户 ID，以便确定订阅是属于客户租户还是服务提供商租户

[Azure 安全中心](../../security-center/index.yml)：

- 跨租户可见性
  - 监视安全策略的符合性，并确保跨所有租户资源的安全覆盖
  - 单个视图中跨多个客户的连续合规性监视
  - 通过安全分数计算监视、会审可操作安全建议，并设置其优先级
- 跨租户安全状况管理
  - 管理安全策略
  - 对不符合可操作安全建议的资源执行操作
  - 收集并存储安全相关数据
- 跨租户威胁检测和保护
  - 跨租户资源检测威胁
  - 应用高级威胁防护控制（如实时 (JIT) VM 访问）
  - 通过自适应网络强化来强化网络安全组配置
  - 通过自适应应用程序控制，确保服务器仅运行适当的应用程序和进程
  - 运用文件完整性监视 (FIM) 监视对重要文件和注册表项的更改

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md)：

- 管理[客户租户中的](../../sentinel/multiple-tenants-service-providers.md)Azure Sentinel 资源
- [跟踪攻击并查看跨多个客户租户的安全警报](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Azure 服务运行状况](../../service-health/index.yml)：

- 通过 Azure 资源运行状况监视客户资源的运行状况
- 跟踪客户使用的 Azure 服务的运行状况

[Azure Site Recovery](../../site-recovery/index.yml)：

- 为客户租户中的 Azure 虚拟机管理灾难恢复选项（请注意，不能使用运行方式帐户复制 VM 扩展）

[Azure 虚拟机](../../virtual-machines/index.yml)：

- 使用虚拟机扩展在客户租户的 Azure VM 上提供部署后配置和自动化任务
- 使用启动诊断对客户租户中的 Azure VM 进行故障排除
- 使用客户租户中的串行控制台访问 VM
- 请注意，不能使用 Azure Active Directory 远程登录 VM，并且不能将 VM 与密码、机密或加密密钥的保管库相集成以进行磁盘加密

[Azure 虚拟网络](../../virtual-network/index.yml)：

- 在客户租户内部署和管理虚拟网络和虚拟网络接口卡 (vNIC)

支持请求：

- 从 Microsoft Azure 门户中的“帮助 + 支持”边栏选项卡中，打开对委派资源的支持请求（选择对委派范围可用的支持计划）

## <a name="current-limitations"></a>当前限制
对于所有方案，都请注意以下当前限制：

- 可以使用 Azure 委派资源管理执行 Azure 资源管理器处理的请求。 这些请求的操作 URI 都以 `https://management.azure.com` 开头。 但是，Azure 委派资源管理不支持由资源类型的实例处理的请求（如 KeyVault 机密访问或存储数据访问）。 这些请求的操作 URI 通常以实例特有的地址开头，例如 `https://myaccount.blob.core.windows.net` 或 `https://mykeyvault.vault.azure.net/`。 后者通常也是数据操作，而不是管理操作。 
- 角色分配必须使用基于角色的访问控制 (RBAC) [内置角色](../../role-based-access-control/built-in-roles.md)。 除了所有者或具有 [DataActions](../../role-based-access-control/role-definitions.md#dataactions) 权限的任何内置角色之外，Azure 委派资源管理当前支持其他所有内置角色。 仅在[向托管标识分配角色](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)时才支持使用用户访问管理员角色。  不支持自定义角色和[经典订阅管理员角色](../../role-based-access-control/classic-administrators.md)。
- 虽然你可以将使用 Azure Databricks 的订阅加入其中，但管理租户中的用户目前无法在委派的订阅上启动 Azure Databricks 工作区。
- 虽然可以为具有资源锁的 Azure 委托资源管理加入订阅和资源组，但这些锁不会阻止管理租户中的用户执行操作。 用于保护系统管理资源（例如由 Azure 托管应用程序或 Azure 蓝图创建的资源）的[拒绝分配](../../role-based-access-control/deny-assignments.md)（系统分配的拒绝分配）会阻止管理租户中的用户对这些资源进行操作；但是，此时客户租户中的用户无法创建自己的拒绝分配（用户分配的拒绝分配）。

## <a name="next-steps"></a>后续步骤

- 要将客户加入 Azure 委派资源管理，可以[使用 Azure 资源管理器模板](../how-to/onboard-customer.md)，或[将专用或公共托管服务发布到 Microsoft Azure 市场](../how-to/publish-managed-services-offers.md)。
- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](../how-to/view-manage-customers.md)。
