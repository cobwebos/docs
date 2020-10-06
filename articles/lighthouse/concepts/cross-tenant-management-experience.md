---
title: 跨租户管理体验
description: Azure 委派资源管理可实现跨租户管理体验。
ms.date: 09/30/2020
ms.topic: conceptual
ms.openlocfilehash: 60eab197e38c7b6ef3b7f2d9442a0b7583f66d09
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91739725"
---
# <a name="cross-tenant-management-experiences"></a>跨租户管理体验

作为服务提供商，你可以使用 [Azure Lighthouse](../overview.md) 在你自己的 Azure Active Directory (Azure AD) 租户内管理多个客户的资源。 可以使用 [Azure 委托资源管理](../concepts/azure-delegated-resource-management.md)跨托管租户执行许多任务和服务。

> [!TIP]
> 还可[在具有多个其自己的 Azure AD 租户的企业中](enterprise.md)使用 Azure 委托资源管理，以简化跨租户管理。

## <a name="understanding-tenants-and-delegation"></a>了解租户和委托

Azure AD 租户是组织的表示形式。 它是组织通过注册 Azure、Microsoft 365 或其他服务与 Microsoft 建立关系时接收的 Azure AD 的专用实例。 每个 Azure AD 租户都是独特的，独立于其他 Azure AD 租户，且具有其自己的租户 ID (GUID)。 有关详细信息，请参阅[什么是 Azure Active Directory？](../../active-directory/fundamentals/active-directory-whatis.md)

通常，为管理客户的 Azure 资源，服务提供商必须使用与该客户的租户相关联的帐户登录 Azure 门户，要求客户租户中的管理员为该服务提供商创建和管理用户帐户。

使用 Azure Lighthouse，载入过程可指定服务提供商的租户中的用户，该用户能够在客户的租户中处理委派的订阅和资源组。 然后，这些用户可以使用自己的凭据登录到 Azure 门户。 在 Microsoft Azure 门户中，这些用户可以管理其有权访问的所有客户的资源。 为此，可以访问 Microsoft Azure 门户中的[我的客户](../how-to/view-manage-customers.md)页，或直接在该客户订阅的上下文中工作（在 Azure 门户中或通过 API）。

Azure Lighthouse 允许更灵活地管理多个客户的资源，而无需登录到不同租户中的不同帐户。 例如，服务提供商可能有两个客户，各自有不同的职责和访问级别。 使用 Azure Lighthouse，授权用户可以登录到服务提供商的租户来访问这些资源。

![显示通过一个服务提供商租户管理的客户资源的关系图。](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API 和管理工具支持

可以直接在门户中对委派资源执行管理任务，也可以使用 API 和管理工具（如 Azure CLI 和 Azure PowerShell）对委派资源执行管理任务。 在处理委托的资源时，只要跨租户管理支持该功能，并且用户具有相应的权限，可以使用所有现有 API。

Azure PowerShell [AzSubscription cmdlet](/powershell/module/Az.Accounts/Get-AzSubscription) 显示 `HomeTenantId` `ManagedByTenantIds` 每个订阅的和属性，使你能够确定返回的订阅是属于托管租户还是属于你的管理租户。

同样，Azure CLI 命令（如 [az account list](/cli/azure/account#az-account-list) ）会显示 `homeTenantId` 和 `managedByTenants` 属性。

> [!TIP]
> 如果在使用 Azure CLI 时看不到这些值，请尝试通过先运行 `az account clear` 再运行 `az login --identity` 来清除缓存。

我们还提供了特定于执行 Azure Lighthouse 任务的 Api。 有关详细信息，请参阅“参考”部分。

## <a name="enhanced-services-and-scenarios"></a>增强的服务和方案

大多数任务和服务都可对跨托管租户的委托资源执行。 下面是一些关键方案，在这些方案中，跨租户管理特别有效。

[Azure Arc](../../azure-arc/index.yml)：

- 大规模管理混合服务器- [启用 Azure Arc 的服务器](../../azure-arc/servers/overview.md)：
  - [管理 azure 上的 Windows Server 或 Linux 计算机，这些计算机连接](../../azure-arc/servers/onboard-portal.md) 到 azure 中的委派订阅和/或资源组
  - 使用 Azure 构造（如Azure Policy 和标记）管理连接的计算机
  - 确保跨客户的混合环境应用相同的一组策略
  - 使用 Azure 安全中心监视客户混合环境中的符合性
- 大规模管理混合 Kubernetes 群集- [启用 Azure Arc 的 Kubernetes (预览) ](../../azure-arc/kubernetes/overview.md)：
  - 管理连接到 Azure 中的委托订阅和/或资源组[的 Kubernetes 群集](../../azure-arc/kubernetes/connect-cluster.md)
  - 对连接的群集[使用 GitOps](../../azure-arc/kubernetes/use-gitops-connected-cluster.md)
  - 跨连接群集强制实施策略

[Azure 自动化](../../automation/index.yml)：

- 使用自动化帐户访问和处理委托的资源

[Azure 备份](../../backup/index.yml)：

- 备份和还原[本地工作负荷、Azure vm、azure 文件共享](../..//backup/backup-overview.md#what-can-i-back-up)等的客户数据
- 使用[备份资源管理器](../../backup/monitor-azure-backup-with-backup-explorer.md)可以查看备份项（包括尚未配置用于备份的 Azure 资源）的操作信息以及委托订阅的监视信息（作业和警报）。 备份资源管理器当前仅可用于 Azure VM 数据。
- 跨委托订阅使用[备份报告](../../backup/configure-reports.md)来跟踪历史趋势、分析备份存储消耗，以及审核备份和还原。

[Azure 成本管理 + 计费](../../cost-management-billing/index.yml)：

- 从管理租户中，CSP 合作伙伴可以查看、管理和分析预计费的消耗成本 (不包含在 Azure 计划下的客户) 购买。 费用将基于零售价和 Azure 基于角色的访问控制， (合作伙伴对客户订阅的 Azure RBAC) 访问权限。

[Azure Kubernetes 服务 (AKS)](../../aks/index.yml)：

- 管理托管的 Kubernetes 环境并部署和管理客户租户中的容器化应用程序

[Azure Monitor](../../azure-monitor/index.yml)：

- 查看委派订阅的警报，并能够查看所有订阅的警报
- 查看委派订阅的活动日志详细信息
- Log analytics：从多个租户中的远程工作区查询数据
- 在通过 webhook 管理租户中触发自动化的客户租户（如 Azure 自动化 runbook 或 Azure Functions）中创建警报
- 在客户租户中创建诊断设置，以将资源日志发送到管理租户中的工作区
- 对于 SAP 工作负荷，请 [使用跨客户租户的聚合视图监视 Sap 解决方案指标](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Azure 网络](../../networking/networking-overview.md)：

- 在托管租户中部署和管理 [Azure 虚拟网络](../../virtual-network/index.yml) 和虚拟网络接口卡 (vnic) 
- 部署和配置 [Azure 防火墙](../../firewall/overview.md)，以保护客户的虚拟网络资源
- 管理连接服务，如 [Azure 虚拟 WAN](../../virtual-wan/virtual-wan-about.md)、 [ExpressRoute](../../expressroute/expressroute-introduction.md)和 [VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
- 使用 Azure Lighthouse 支持 [Azure 网络 MSP 计划](../../networking/networking-partners-msp.md)的关键方案

[Azure Policy](../../governance/policy/index.yml)：

- 符合性快照显示委派订阅中分配的策略的详细信息
- 在委派的订阅中创建和编辑策略定义
- 在委派的订阅中分配客户定义的策略定义
- 客户将看到由服务提供商和自己创建的策略
- 可以 [修正 deployIfNotExists 或修改托管租户内的分配](../how-to/deploy-policy-remediation.md)

[Azure Resource Graph](../../governance/resource-graph/index.yml)：

- 现在在返回的查询结果中包含租户 ID，以便您可以确定订阅是否属于托管租户

[Azure 安全中心](../../security-center/index.yml)：

- 跨租户可见性
  - 对是否符合安全策略进行监视，确保安全措施涵盖所有租户的资源
  - 单个视图中跨多个租户的连续法规遵从性监视
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
- 请注意，必须将整个订阅委托给管理租户;委托资源组不支持 Azure 安全中心方案

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md)：

- 管理[客户租户中的](../../sentinel/multiple-tenants-service-providers.md) Azure Sentinel 资源
- [跟踪攻击并查看跨多个租户的安全警报](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- 跨租户跨多个 Sentinel 工作区[查看事件](../../sentinel/multiple-workspace-view.md)

[Azure 服务运行状况](../../service-health/index.yml)：

- 通过 Azure 资源运行状况监视客户资源的运行状况
- 跟踪客户使用的 Azure 服务的运行状况

[Azure Site Recovery](../../site-recovery/index.yml)：

- 为客户租户中的 Azure 虚拟机管理灾难恢复选项 (请注意，不能使用 `RunAs` 帐户复制 VM 扩展) 

[Azure 虚拟机](../../virtual-machines/index.yml)：

- 使用虚拟机扩展在 Azure Vm 上提供部署后配置和自动化任务
- 使用启动诊断对 Azure Vm 进行故障排除
- 使用串行控制台访问 Vm
- 通过 [策略使用托管标识](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret)，将 vm 与密码、机密或加密密钥的 Azure Key Vault 集成，以确保机密存储在托管租户的 Key Vault 中
- 请注意，不能使用 Azure Active Directory 远程登录到 Vm

支持请求：

- 在委派资源的 Azure 门户中[打开支持请求**Help + support** ](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) ， (选择可用于委派的作用域的支持计划) 

## <a name="current-limitations"></a>当前限制

对于所有方案，都请注意以下当前限制：

- 可以使用 Azure Lighthouse 来执行 Azure 资源管理器处理的请求。 这些请求的操作 URI 都以 `https://management.azure.com` 开头。 但是，Azure Lighthouse 不支持 Key Vault 机密访问或存储数据访问)  (资源类型实例处理的请求。 这些请求的操作 URI 通常以实例特有的地址开头，例如 `https://myaccount.blob.core.windows.net` 或 `https://mykeyvault.vault.azure.net/`。 后者通常也是数据操作，而不是管理操作。
- 角色分配必须使用基于角色的访问控制 (RBAC) [内置角色](../../role-based-access-control/built-in-roles.md)。 除所有者或具有权限的任何内置角色外，Azure 委托资源管理当前支持所有内置角色 [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) 。 仅在[向托管标识分配角色](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)时才支持使用用户访问管理员角色。  不支持自定义角色和[经典订阅管理员角色](../../role-based-access-control/classic-administrators.md)。
- 尽管你可以加入使用 Azure Databricks 的订阅，但管理租户中的用户目前无法在委托订阅上启动 Azure Databricks 工作区。
- 尽管可以加入具有资源锁的订阅和资源组，但这些锁定不会阻止管理租户中的用户执行操作。 用于保护系统管理资源（例如由 Azure 托管应用程序或 Azure 蓝图创建的资源）的[拒绝分配](../../role-based-access-control/deny-assignments.md)（系统分配的拒绝分配）会阻止管理租户中的用户对这些资源进行操作；但是，目前客户租户中的用户无法创建自己的拒绝分配（用户分配的拒绝分配）。

## <a name="next-steps"></a>后续步骤

- 通过 [使用 azure 资源管理器模板](../how-to/onboard-customer.md) 或 [向 azure Marketplace 发布专用或公共托管服务产品/服务](../how-to/publish-managed-services-offers.md)，将客户加入 azure Lighthouse。
- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](../how-to/view-manage-customers.md)。
