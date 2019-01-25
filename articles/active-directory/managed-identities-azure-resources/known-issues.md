---
title: Azure 资源托管标识的 FAQ 和已知问题
description: Azure 资源托管标识的已知问题。
services: active-directory
documentationcenter: ''
author: daveba
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: daveba
ms.openlocfilehash: a23bd99b05184316ee1547ab0917547b5d0da18f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54421923"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Azure 资源托管标识的 FAQ 和已知问题

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>常见问题解答 (FAQ)

> [!NOTE]
> Azure 资源托管标识是以前称为托管服务标识 (MSI) 的服务的新名称。

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Azure 资源托管标识可以用于 Azure 云服务吗？

否，Azure 云服务中没有支持 Azure 资源托管标识的计划。

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Azure 资源托管标识能否用于 Active Directory 身份验证库 (ADAL) 或 Microsoft 身份验证库 (MSAL)？

否，Azure 资源托管标识尚未与 ADAL 或 MSAL 集成。 有关使用 REST 终结点获取 Azure 资源托管标识的令牌的详细信息，请参阅[如何在 Azure VM 上使用 Azure 资源托管标识来获取访问令牌](how-to-use-vm-token.md)。

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>什么是 Azure 资源托管标识的安全边界？

标识的安全边界是标识所附加到的资源。 例如，启用了 Azure 资源托管标识的虚拟机的安全边界是虚拟机。 在该 VM 上运行的任何代码都可以调用 Azure 资源托管标识终结点和请求令牌。 使用支持 Azure 资源托管标识的其他资源也具有类似体验。

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>如果没有在请求中指定标识，IMDS 将默认采用什么标识？

- 如果启用了系统分配的托管标识并且没有在请求中指定标识，则 IMDS 将默认采用系统分配的托管标识。
- 如果未启用系统分配的托管标识并且仅存在一个用户分配的托管标识，则 IMDS 将默认采用该单一用户分配的托管标识。 
- 如果未启用系统分配的托管的标识，并且存在多个用户分配的托管标识，则必须在请求中指定一个托管标识。

### <a name="should-i-use-the-managed-identities-for-azure-resources-vm-imds-endpoint-or-the-vm-extension-endpoint"></a>我应该使用 Azure 资源托管标识 VM IMDS 终结点还是 VM 扩展终结点？

将 Azure 资源托管标识与 VM 一起使用时，我们建议使用 Azure 资源托管标识 IMDS 终结点。 Azure 实例元数据服务是一个 REST 终结点，可供通过 Azure 资源管理器创建的所有 IaaS VM 使用。 通过 IMDS 使用 Azure 资源托管标识的好处包括：
    - 所有 Azure IaaS 支持的操作系统都可以通过 IMDS 使用 Azure 资源托管标识。
    - 不再需要在 VM 上安装扩展即可启用 Azure 资源托管标识。 
    - Azure 资源托管标识使用的证书将不再出现在 VM 中。
    - IMDS 终结点是一个已知不可路由的 IP 地址，该地址只能在 VM 中访问。

Azure 资源托管标识 VM 扩展目前仍可使用；但在以后，我们会默认使用 IMDS 终结点。 Azure 资源托管标识 VM 扩展将于 2019 年 1 月弃用。 

有关 Azure 实例元数据服务的详细信息，请参阅 [IMDS 文档](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>如果我将订阅移动到另一个目录中，是否会自动重新创建托管标识？

不是。 如果你将订阅移动到另一个目录中，则必须手动重新创建标识并重新向它们授予 Azure RBAC 角色分配。
    - 对于系统分配的托管标识：禁用并重新启用。
    - 对于用户分配的托管标识：删除、重新创建并重新将其附加到所需的资源（例如虚拟机）

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>是否可以使用托管标识来访问不同目录/租户中的资源？

不是。 托管标识当前不支持跨目录方案。 

### <a name="what-are-the-supported-linux-distributions"></a>有哪些受支持的 Linux 发行版？

Azure IaaS 支持的所有 Linux 发行版都可以通过 IMDS 终结点与 Azure 资源托管标识配合使用。 

Azure 资源托管标识 VM 扩展（计划在 2019 年 1 月弃用）仅支持以下 Linux 发行版：
- CoreOS Stable
- CentOS 7.1
- Red Hat 7.2
- Ubuntu 15.04
- Ubuntu 16.04

目前不支持其他 Linux 发行版，该扩展在不受支持的发行版上运行可能会失败。

该扩展适用于 CentOS 6.9。 但是，由于在 6.9 中缺少系统支持，该扩展如果发生故障或停止，将不会自动重启。 当 VM 重启时，它会重启。 要手动重启该扩展，请参阅[如何重启 Azure 资源托管标识扩展？](#how-do-you-restart-the-managed-identities-for-Azure-resources-extension)

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>如何重启 Azure 资源托管标识扩展？
在 Windows 和某些 Linux 版本中，如果该扩展停止，可使用以下 cmdlet 手动重启该扩展：

```powershell
Set-AzureRmVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

其中： 
- 适用于 Windows 的扩展名称和类型是：ManagedIdentityExtensionForWindows
- 适用于 Linux 的扩展名称和类型是：ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>已知问题

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>尝试 Azure 资源托管标识扩展的架构导出功能时，“自动化脚本”失败

如果在 VM 上启用了 Azure 资源托管标识，当尝试将“自动化脚本”功能用于 VM 或其资源组时，将显示以下错误：

![Azure 资源托管标识自动化脚本导出错误](./media/msi-known-issues/automation-script-export-error.png)

Azure 资源托管标识 VM 扩展（计划在 2019 年 1 月弃用）当前不支持将其架构导出到资源组模板的功能。 因此，生成的模板不显示用于在资源上启用 Azure 资源托管标识的配置参数。 按照[使用模板在 Azure VM 上配置 Azure 资源托管标识](qs-configure-template-windows-vm.md)中的示例，可以手动添加这些部分。

当架构导出功能可用于 Azure 资源托管标识 VM 扩展（计划在 2019 年 1 月弃用）时，它将在[导出包含 VM 扩展的资源组](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)中列出。

### <a name="configuration-blade-does-not-appear-in-the-azure-portal"></a>Azure 门户中不显示“配置”边栏选项卡

如果 VM 中不显示“VM 配置”边栏选项卡，表明所在区域的门户中尚未启用 Azure 资源托管标识。  请稍后再看看。  还可以使用 [PowerShell](qs-configure-powershell-windows-vm.md) 或 [Azure CLI](qs-configure-cli-windows-vm.md) 为 VM 启用 Azure 资源托管标识。

### <a name="cannot-assign-access-to-virtual-machines-in-the-access-control-iam-blade"></a>无法在“访问控制(IAM)”边栏选项卡中向虚拟机授予访问权限

如果在 Azure 门户中依次转到“访问控制(IAM)” > “添加角色分配”后，“将访问权限分配给”中没有“虚拟机”选项，表明所在区域的门户中尚未启用 Azure 资源托管标识。 请稍后再看看。  仍可以通过搜索 Azure 资源托管标识服务主体，选择用于角色分配的 VM 的标识。  在“选择”字段中输入 VM 名称，服务主体就会出现在搜索结果中。

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>从资源组或订阅迁移后无法启动 VM

如果迁移处于运行状态的 VM，它将在迁移期间继续运行。 不过，如果在迁移后停止并重启 VM，那么它将无法启动。 导致此问题发生的原因是，VM 未更新对 Azure 资源托管标识的标识的引用，仍然继续指向旧资源组中的标识。

**解决方法** 
 
在 VM 上触发更新，以便它可以获取正确的 Azure 资源托管标识的值。 可以更改 VM 属性，从而更新对 Azure 资源托管标识的标识的引用。 例如，可以运行下列命令，在 VM 上设置新的标记值：

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
此命令在 VM 上设置值为 1 的新标记“fixVM”。 
 
通过设置此属性，VM 可以更新包含正确的 Azure 资源托管标识资源 URI，然后就应该能启动 VM 了。 
 
在 VM 启动后，便可以运行下列命令，从而删除此标记：

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>VM 扩展预配失败

由于 DNS 查找失败，VM 扩展的预配可能会失败。 重新启动 VM，然后重试。
 
> [!NOTE]
> VM 扩展计划在 2019 年 1 月前弃用。 我们建议你改为使用 IMDS 终结点。

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>在 Azure AD 目录之间转移订阅

将订阅移动/转移到另一个目录时，托管标识不会更新。 因此，任何现存的系统分配的或用户分配的托管标识将被破坏。 

作为一种解决方法，在订阅移动后，你可以禁用系统分配的托管标识并重新启用它们。 类似地，你可以删除并重新创建用户分配的任何托管标识。 

## <a name="known-issues-with-user-assigned-managed-identities"></a>用户分配的托管标识的已知问题

- 不支持创建名称中有特殊字符（即下划线）的用户分配托管标识。
- 用户分配的标识名称限制为 24 个字符。 如果名称超过 24 个字符，则无法将该标识分配给资源（即虚拟机）。
- 如果使用的是托管标识虚拟机扩展（计划在 2019 年 1 月弃用），则支持的限制为 32 个用户分配托管标识。 如果不使用托管标识虚拟机扩展，支持的限制为 512 个。  
- 将用户分配的托管标识移动到另一个资源组将导致标识被破坏。 因此，你将无法请求该标识的令牌。 
- 将订阅转移到另一个目录将破坏任何现存的用户分配的托管标识。 