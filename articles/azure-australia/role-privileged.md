---
title: Azure 中基于角色的访问控制和 Privileged Identity Management
description: 在澳大利亚地区实施基于角色的访问控制和 Privileged Identity Management 的指导, 以满足澳大利亚政府政策、法规和法规的特定要求。
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: e2a94f82e4830bd1e9c96039f5ef8fe6546b0d0b
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982676"
---
# <a name="role-based-access-control-rbac-and-privileged-identity-management-pim"></a>基于角色的访问控制 (RBAC) 和 Privileged Identity Management (PIM)

管理权限是确保任何 IT 环境中的安全性的关键步骤。 使用最小特权安全机制的限制管理权限是[ACSC ISM](https://acsc.gov.au/infosec/ism/index.htm)的要求, 并且是[ACSC 基本的 8](https://www.acsc.gov.au/infosec/mitigationstrategies.htm)个安全建议列表的一部分。

Microsoft 提供了一套控件来实现 Microsoft Azure 中的实时和足够的访问。 了解这些控件对于云中的有效安全状况是必不可少的。 本指南将提供控件本身的概述以及实现这些控件的关键设计注意事项。

## <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)

基于角色的访问控制是管理对 Microsoft Azure 中所有资源的访问权限以及 Azure Active Directory (Azure AD) 的管理的核心。 可以通过在 Azure 中提供的许多补充功能实现 RBAC。 本文重点介绍如何使用 Azure 管理组、Azure Active Directory 组和 Azure Privileged Identity Management (PIM) 实现有效的 RBAC。

在高级别上, 实现 RBAC 需要三个组件:

![RBAC-概述](media/rbac-overview.png)

* **安全主体**:安全主体可以是以下任一项:用户、组、[服务主体](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)或[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)。 应使用 Azure Active Directory 组为安全主体分配权限。

* **角色定义**:角色定义也称为角色, 是权限的集合。 这些权限定义可由分配给角色定义的安全主体执行的操作。 此功能由 Azure 资源角色和 Azure Active Directory 管理员角色提供。 Azure 随附了一组内置角色 (链接), 可通过自定义角色进行扩展。

* **范围**：作用域是角色定义适用的一组 Azure 资源。 可以使用 Azure 管理组将 azure 角色分配到 Azure 资源。

这三个组件组合在一起, 将角色定义中定义的访问权限授予 Azure 管理组 "范围内的所有资源, 这称为角色分配。 可以将多个角色定义分配给一个安全主体, 并且可以将多个安全主体分配给一个作用域。

### <a name="azure-active-directory-groups"></a>Azure Active Directory 组

将权限分配给个人或团队时, 应尽可能将分配链接到 Azure Active Directory 组, 而不是直接分配给相关用户。 这与从本地 Active Directory 实现中继承的建议做法相同。 应尽可能为每个团队创建 Azure Active Directory 组, 这是你创建的 Azure 管理组的逻辑结构的补充。

在混合云方案中, 本地 Windows Server Active Directory 安全组可以同步到 Azure Active Directory 实例。 如果已使用这些 Windows Server Active Directory 安全组实现了 RBAC 本地部署, 则这些组一旦同步后即可用于实现 Azure 资源的 RBAC。 否则, 你可以将云环境视为一个全新的平板, 用于设计和实施围绕你的 Azure Active Directory 实现构建的强大权限管理计划。

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Azure 资源角色与 Azure Active Directory 管理员角色

Microsoft Azure 为[Azure 资源](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)和[Azure Active Directory 管理](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)提供各种内置角色。 这两种类型的角色都提供对 Azure 资源或 Azure AD 管理员的特定粒度访问权限。 请务必注意, Azure 资源角色不能用于提供对 Azure AD 的管理访问权限, Azure AD 角色不提供对 Azure 资源的特定访问权限。

可以使用内置角色分配给 Azure 资源的访问类型的一些示例包括:

* 允许一个用户管理订阅中的虚拟机，并允许另一个用户管理虚拟网络
* 允许 DBA 组管理订阅中的 SQL 数据库
* 允许某个用户管理资源组中的所有资源，例如虚拟机、网站和子网
* 允许某个应用程序访问资源组中的所有资源

可为 Azure AD 管理分配的访问类型的示例包括:

* 允许技术支持人员重置用户密码
* 允许员工邀请外部用户使用 Azure AD 实例进行 B2B 协作
* 允许管理人员读取登录和审核报告的权限
* 允许人员管理所有用户和组, 包括重置密码。

必须花些时间了解内置角色提供的允许操作的完整列表, 以确保不会授予不必要的访问权限。 内置角色及其提供的访问权限的列表不断发展, 可以通过查看以上链接的文档或使用 Azure PowerShell cmdlet 来查看角色及其定义的完整列表:

```PowerShell
PS C:\> Get-AzRoleDefinition

Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

或 Azure CLI 命令:

```bash
PS C:\> az role definition list
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

还可以根据需要创建自定义的 Azure 资源角色。 可以通过 PowerShell 或 Azure CLI 在 Azure 门户中创建这些自定义角色。 创建自定义角色时, 务必确保角色的用途是唯一的, 并且其功能尚未由现有的 Azure 资源角色提供。 这会减少日常管理的复杂性并降低收到不必要权限的安全主体的风险。 例如, 创建一个位于内置 Azure 资源角色 "虚拟机参与者" 和 "虚拟机管理员登录名" 的自定义 Azure 资源角色。

自定义角色可能基于现有参与者角色, 该角色将授予以下访问权限:

| Azure 资源 | 访问级别 |
| --- | --- |
| 虚拟机 | 可以管理但无法访问 |
| 附加到 VM 的虚拟网络 | 无法访问 |
| 附加到 VM 的存储 | 无法访问 |
|

自定义角色可保留此基本访问权限, 但允许指定用户使用一些基本的额外权限来修改虚拟机的网络配置。

Azure 资源角色还具有可通过 Azure 管理组分配给资源的好处。

### <a name="azure-management-groups"></a>Azure 管理组

企业可以使用 azure 管理组来管理将角色分配给 Azure 租户中所有订阅及其资源的角色。 Azure 管理组旨在使你能够创建管理层次结构, 包括在 Azure 中按层次结构方式映射组织结构。 将组织的业务单元创建为单独的逻辑实体后, 便可以根据每个团队的特定要求在组织内应用权限。 Azure 管理组可用于定义最多包含六个级别的管理层次结构。

![管理组](media/management-groups.png)

Azure 管理组映射到 Azure 租户中的 Azure 订阅。 这使组织能够将属于特定业务部门的 Azure 资源隔离开来, 并提供对成本管理和特权分配的精细控制。

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Microsoft 通过 Azure Privileged Identity Management 实现了实时 (JIT) 访问权限 (JEA)。 此服务使管理人员能够控制、管理和监视对 Azure 资源的特权访问。 PIM 允许管理人员为角色使安全主体成为 "符合条件", 从而允许用户通过 Azure 门户或通过 PowerShell cmdlet 来请求激活角色。 默认情况下, 可以在1到72小时之间激活角色分配。 必要时, 用户可以请求对其角色分配的扩展, 并且存在使角色分配永久存在的选项。 或者, 在用户请求激活其符合条件的角色时, 可以强制实施多重身份验证。 角色激活的分配时间段到期后, 安全主体将不再具有角色授予的特权访问权限。

使用 PIM 可防止常见权限分配问题, 这些问题可能发生在不使用实时访问的环境中, 或者不会执行特权分配的例行审核。 一个常见问题是, 在完成需要提升权限的任务后, 会忘记并保留较长时间的已提升权限的分配。 另一个问题是, 在配置其他类似安全主体时, 通过克隆分配到安全主体的访问权限, 在环境中增加了提升的权限。

## <a name="key-design-considerations"></a>关键设计考虑因素

设计 RBAC 策略时, 如果希望强制实施最低权限安全性, 应考虑以下安全要求:

* 验证特权访问请求
* 管理权限仅限于执行特定任务所需的最低访问权限
* 管理权限仅限于执行特定任务所需的最短时间段
* 应定期查看授予的管理权限

设计 RBAC 策略的过程需要对业务职能进行详细评审, 以了解不同业务角色之间的访问和需要提升权限的类型和工作频率之间的不同之处。 备份操作员、安全管理员和审核员之间的不同之处在于, 在不同的时间进行审核时, 在不同的时间使用不同的访问级别。

## <a name="validate-requests-for-access"></a>验证访问请求

必须显式批准提升的权限。 为了支持这一点, 必须制定批准流程, 并负责验证是否所有其他权限请求都是合法的。 Privileged Identity Management 提供了用于审批角色分配的多个选项。 可以将角色激活请求配置为允许自行批准或将其设置为带外批准, 并要求被任命为审批者手动查看和批准所有角色激活请求。 还可以将激活请求配置为要求在激活请求中包含附加支持信息, 如票据号。

### <a name="restrict-privilege-based-on-duties"></a>基于职责限制权限

限制授予安全主体的特权级别至关重要, 因为权限分配是常见的 IT 安全攻击向量。 必须对所管理的资源的类型以及负责的团队进行评估, 以便可以分配每天职责所需的最小特权级别。 超过日常义务所需的其他权限仅应在执行特定任务所需的时间段内进行授予。 这种情况的一个示例是向客户的管理员提供 "参与者" 访问权限, 但允许他们针对需要临时高级访问权限的特定任务请求 "所有者" 权限。

这可确保每个单独的管理员仅在最短的时间内具有提升的访问权限。 遵循这些做法可减少任何组织的 IT 基础结构的总体攻击面。

### <a name="regular-evaluation-of-administrative-privilege"></a>定期评估管理权限

应定期审核环境中的安全主体, 以确保当前已分配正确的权限级别, 这一点非常重要。 Microsoft Azure 提供了许多方法来审核和评估分配给 Azure 安全主体的特权。 Privileged Identity Management 允许管理人员定期执行授予安全主体的角色的 "访问评审"。 可以采取访问评审来审核 Azure 资源角色分配, 并 Azure Active Directory 管理角色分配。 可以使用以下属性配置访问评审:

* **查看名称并查看开始和结束日期**:应将审阅配置为足够长的时间, 以供命名用户完成。

* **要查看的角色**:每个访问评审都侧重于单个 Azure 角色。

* **命名审阅者**:有三个选项用于执行审阅。 可以将审阅分配给其他人完成、自行执行，也可以让每个用户审阅自己的访问。

* **要求用户提供访问原因**:完成访问评审时, 可能需要用户输入维护其权限级别的原因。

可以通过 Azure 门户中的仪表板随时监视挂起的访问评审的进度。 在访问评审完成之前, 对所查看的角色的访问权限将保持不变。 还可以在指定的时间段内[审核](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-use-audit-log)所有 PIM 用户分配和激活。

## <a name="next-steps"></a>后续步骤

查看[Azure 澳大利亚的系统监视](system-monitor.md)一文。
