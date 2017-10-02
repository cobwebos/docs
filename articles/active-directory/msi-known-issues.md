---
title: "Azure Active Directory 的托管服务标识 (MSI) 的常见问题解答和已知问题"
description: "Azure Active Directory 的托管服务标识存在的已知问题。"
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 09/22/2017
ms.author: skwan
ms.translationtype: HT
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: 3cfd1eb55a031696635270a56ed5028e3b249543
ms.contentlocale: zh-cn
ms.lasthandoff: 09/23/2017

---

# <a name="faqs-and-known-issues-with-managed-service-identity-msi-for-azure-active-directory"></a>Azure Active Directory 的托管服务标识 (MSI) 的常见问题解答和已知问题

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>常见问题解答 (FAQ)

### <a name="does-msi-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>MSI 能否用于 Active Directory Authentication Library (ADAL) 或 Microsoft Authentication Library (MSAL)？

不能，MSI 尚未与 ADAL 或 MSAL 集成。

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

## <a name="known-issues"></a>已知问题

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

