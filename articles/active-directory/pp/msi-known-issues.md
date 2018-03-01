---
title: "Azure Active Directory 的托管服务标识 (MSI) 的常见问题解答和已知问题"
description: "Azure Active Directory 的托管服务标识存在的已知问题。"
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: b0b5b991b7d2baaa40ebedbd19247717e91aa027
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/23/2018
---
# <a name="faq-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Azure Active Directory 的托管服务标识 (MSI) 的常见问题解答和已知问题

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

## <a name="frequently-asked-questions"></a>常见问题

### <a name="does-msi-work-with-azure-cloud-services"></a>MSI 能否用于 Azure 云服务？

否，没有在 Azure 云服务中支持 MSI 的计划。

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>MSI 能否用于 Active Directory Authentication Library (ADAL) 或 Microsoft Authentication Library (MSAL)？

不能，MSI 尚未与 ADAL 或 MSAL 集成。 有关使用 MSI REST 终结点获取 MSI 令牌的详细信息，请参阅[如何使用 Azure VM 托管服务标识 (MSI) 获取令牌](msi-how-to-use-vm-msi-token.md)。

### <a name="what-are-the-supported-linux-distributions"></a>有哪些受支持的 Linux 发行版？

以下 Linux 发行版支持 MSI： 

- CoreOS Stable
- CentOS 7.1
- RedHat 7.2
- Ubuntu 15.04

目前不支持其他 Linux 发行版，该扩展在不受支持的发行版上运行可能会失败。

该扩展适用于 CentOS 6.9。 但是，由于在 6.9 中缺少系统支持，该扩展如果发生故障或停止，将不会自动重启。 当 VM 重启时，它会重启。 若要手动重启该扩展，请参阅[如何重启 MSI 扩展？](#how-do-you-restart-the-msi-extension)

### <a name="how-do-you-restart-the-msi-extension"></a>如何重启 MSI 扩展？
在 Windows 和某些 Linux 版本中，如果该扩展停止，可使用以下 cmdlet 手动重启该扩展：

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

其中： 
- 适用于 Windows 的扩展名称和类型是：ManagedIdentityExtensionForWindows
- 适用于 Linux 的扩展名称和类型是：ManagedIdentityExtensionForLinux

### <a name="are-there-rbac-roles-for-user-assigned-identities"></a>是否有用于用户分配的标识的 RBAC 角色？
有：
1. MSI 参与者： 

- 可以：对用户分配的标识进行 CRUD 操作。 
- 不能：将用户分配的标识分配给资源。 （即，将标识分配给 VM）

2. MSI 操作员： 

- 可以：将用户分配的标识分配给资源。 （即，将标识分配给 VM）
- 不能：对用户分配的标识进行 CRUD 操作。

注意：内置的参与者角色可以执行上述所有操作： 
- 对用户分配的标识进行 CRUD 操作
- 将用户分配的标识分配给资源。 （即，将标识分配给 VM）


## <a name="known-issues"></a>已知问题

### <a name="automation-script-fails-when-attempting-schema-export-for-msi-extension"></a>尝试对 MSI 扩展进行架构导出时，“自动化脚本”失败

如果在 VM 上启用了托管服务标识，当尝试将“自动化脚本”功能用于 VM 或其资源组时，将显示以下错误：

![MSI 自动化脚本导出错误](~/articles/active-directory/media/msi-known-issues/automation-script-export-error.png)

托管服务标识 VM 扩展当前不支持将其架构导出到资源组模板的功能。 因此，生成的模板不显示用于在资源上启用托管服务标识的配置参数。 可以按照[使用模板配置 VM 托管服务标识](msi-qs-configure-template-windows-vm.md)中的示例，手动添加这些节。

当 MSI VM 扩展的架构导出功能变为可用时，它将在[导出包含 VM 扩展的资源组](~/articles/virtual-machines/windows/extensions-export-templates.md#supported-virtual-machine-extensions)中列出。

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Azure 门户中不显示“配置”边栏选项卡

如果 VM 中不显示“VM 配置”边栏选项卡，表明所在区域的门户中尚未启用 MSI。  请稍后再看看。  也可以使用 [PowerShell](msi-qs-configure-powershell-windows-vm.md) 或 [Azure CLI](msi-qs-configure-cli-windows-vm.md) 为 VM 启用 MSI。

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>无法在“访问控制(IAM)”边栏选项卡中向虚拟机授予访问权限

如果在 Azure 门户中依次转到“访问控制(IAM)” > “添加权限”后，“将访问权限分配给”中没有“虚拟机”选项，表明所在区域的门户中尚未启用托管服务标识。 请稍后再看看。  仍可以搜索 MSI 的服务主体，选择用于角色分配的托管服务标识。  在“选择”字段中输入 VM 名称，服务主体就会出现在搜索结果中。

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>从资源组或订阅迁移后无法启动 VM

如果迁移处于运行状态的 VM，它将在迁移期间继续运行。 不过，如果在迁移后停止并重启 VM，那么它将无法启动。 导致此问题发生的原因是，VM 未更新对 MSI 标识的引用，仍然指向旧资源组中的标识。

**解决方法** 
 
在 VM 上触发更新，以便它可以获取正确的 MSI 值。 可以更改 VM 属性，从而更新对 MSI 标识的引用。 例如，可以运行下列命令，在 VM 上设置新的标记值：

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
此命令在 VM 上设置值为 1 的新标记“fixVM”。 
 
通过设置此属性，VM 可以更新包含正确的 MSI 资源 URI，然后就应该能启动 VM 了。 
 
在 VM 启动后，便可以运行下列命令，从而删除此标记：

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

## <a name="known-issues-with-user-assigned-msi-private-preview-feature"></a>用户分配的 MSI 的已知问题（专用预览功能）

- 删除所有用户分配的 MSI 的唯一方式是启用系统分配的 MSI。 
- 由于 DNS 查找失败，配置 VM 的 VM 扩展可能失败。 重新启动 VM，然后重试。 
- 添加“不存在”的 MSI 会导致 VM 出现故障。 *注意：对 MSI 不存在时无法分配标识的修复即将推出*
- 目前，Azure 存储教程仅提供于美国中部 EUAP。 
- 不支持创建名称中具有特殊字符（即下划线）的用户分配的 MSI。
- 如果再添加一个用户分配的标识，clientID 可能无法供其请求令牌使用。 使用以下两个 bash 命令重新启动 MSI VM 扩展可缓解此问题：
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler disable"`
 - `sudo bash -c "/var/lib/waagent/Microsoft.ManagedIdentity.ManagedIdentityExtensionForLinux-1.0.0.8/msi-extension-handler enable"`
- Windows 上的 VMAgent 当前不支持用户分配的 MSI。 
- 如果 VM 有用户分配的 MSI，但没有系统分配的 MSI，门户 UI 会显示已启用 MSI。 若要启用系统分配的 MSI，请使用 Azure 资源管理器模板、Azure CLI 或 SDK。
