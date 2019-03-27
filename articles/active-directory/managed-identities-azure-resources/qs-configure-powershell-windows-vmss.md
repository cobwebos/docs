---
title: 如何使用 PowerShell 在虚拟机规模集上配置 Azure 资源的托管标识
description: 分步说明如何使用 PowerShell 在虚拟机规模集上配置系统分配的托管标识和用户分配的托管标识。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4917720af2396b68ccd36cc0410c9acbbba2d9b2
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448582"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>使用 PowerShell 在虚拟机规模集上配置 Azure 资源的托管标识

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

在本文中，你将了解如何使用 PowerShell 在虚拟机规模集上执行 Azure 资源的托管标识操作：
- 在虚拟机规模集上启用和禁用系统分配的托管标识
- 在虚拟机规模集上添加和删除用户分配的托管标识

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要执行本文中的管理操作，帐户需要以下基于 Azure 角色的访问控制分配：

    > [!NOTE]
    > 无需其他 Azure AD 目录角色分配。

    - [虚拟机参与者](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)，可创建虚拟机规模集，并从虚拟机规模集启用和删除系统分配的托管标识和/或用户分配的托管标识。
    - [托管标识参与者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色，可以创建用户分配的托管标识。
    - [托管标识操作员](/azure/role-based-access-control/built-in-roles#managed-identity-operator)角色，可在虚拟机规模集中分配和删除用户分配的托管标识。
- 安装[最新版本的 Azure PowerShell](/powershell/azure/install-az-ps)（如果尚未安装）。 

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识

在此部分中，将了解如何使用 Azure PowerShell 启用和删除系统分配的托管标识。

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>在创建 Azure 虚拟机规模集的过程中启用系统分配的托管标识

要创建启用了系统分配托管标识的虚拟机规模集，请执行以下操作：

1. 请参阅*示例 1*中[新建 AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) cmdlet 参考文章，以便创建虚拟机规模集设置系统分配的托管标识。  将参数 `-IdentityType SystemAssigned` 添加到 `New-AzVmssConfig` cmdlet：

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```
> [!NOTE]
> 对于 Azure 资源虚拟机规模集扩展，但将很快被弃用，可能会选择性地预配托管的标识。 我们建议使用 Azure 实例元数据标识终结点进行身份验证。 有关详细信息，请参阅[停止使用 VM 扩展并开始使用 Azure IMDS 终结点进行身份验证](howto-migrate-vm-extension.md)。


## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>在现有 Azure 虚拟机规模集上启用系统分配的托管标识

如果需要在现有 Azure 虚拟机规模集上启用系统分配的托管标识，请执行以下操作：

1. 使用 `Connect-AzAccount` 登录到 Azure 门户。 使用与包含虚拟机规模集的 Azure 订阅关联的帐户。 此外，请确保该帐户所属角色可授予对虚拟机规模集的写入权限，如“虚拟机参与者”：

   ```powershell
   Connect-AzAccount
   ```

2. 首先使用 [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) cmdlet 检索虚拟机规模集属性。 然后，若要启用系统分配的托管标识，请在 [Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet 上使用 `-IdentityType` 开关：

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

> [!NOTE]
> 对于 Azure 资源虚拟机规模集扩展，但将很快被弃用，可能会选择性地预配托管的标识。 我们建议使用 Azure 实例元数据标识终结点进行身份验证。 有关详细信息，请参阅[从 VM 扩展迁移到 Azure IMDS 终结点进行身份验证](howto-migrate-vm-extension.md)。

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>从 Azure 虚拟机规模集中禁用系统分配的托管标识

如果某个虚拟机规模集不再需要系统分配的托管标识，但仍需要用户分配的托管标识，请使用以下 cmdlet：

1. 使用 `Connect-AzAccount` 登录到 Azure 门户。 使用与包含 VM 的 Azure 订阅关联的帐户。 此外，请确保该帐户所属角色可授予对虚拟机规模集的写入权限，如“虚拟机参与者”：

2. 运行以下 cmdlet：

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

如果某个虚拟机规模集不再需要系统分配的托管标识，且没有用户分配的托管标识，请使用以下命令：

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>用户分配的托管标识

本部分介绍如何使用 Azure PowerShell 从虚拟机规模集中添加和删除用户分配的托管标识。

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>在创建 Azure 虚拟机规模集的过程中启用用户分配的托管标识

目前不支持通过 PowerShell 创建具有用户分配的托管标识的新虚拟机规模集。 有关如何将用户分配的托管标识添加到现有虚拟机规模集的信息，请参阅下一部分。 请关注后续更新。

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>将用户分配的托管标识分配到现有 Azure 虚拟机规模集

将用户分配的托管标识分配到现有 Azure 虚拟机规模集：

1. 使用 `Connect-AzAccount` 登录到 Azure 门户。 使用与包含虚拟机规模集的 Azure 订阅关联的帐户。 此外，请确保该帐户所属角色可授予对虚拟机规模集的写入权限，如“虚拟机参与者”：

   ```powershell
   Connect-AzAccount
   ```

2. 首先使用 `Get-AzVM` cmdlet 检索虚拟机规模集属性。 然后，若要向虚拟机规模集分配用户分配的托管标识，请在 [Update-AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet 上使用 `-IdentityType` 和 `-IdentityID` 开关。 将 `<VM NAME>`、`<SUBSCRIPTION ID>`、`<RESROURCE GROUP>`、`<USER ASSIGNED ID1>`、`USER ASSIGNED ID2` 替换为自己的值。

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>从 Azure 虚拟机规模集删除用户分配的托管标识

如果虚拟机规模集有多个用户分配的托管标识，则可以使用以下命令将这些标识删除到只剩一个。 请务必将 `<RESOURCE GROUP>` 和 `<VIRTUAL MACHINE SCALE SET NAME>` 参数值替换为自己的值。 `<USER ASSIGNED IDENTITY NAME>` 是用户分配的托管标识的名称属性，该属性应保留在虚拟机规模集上。 可通过 `az vmss show` 在虚拟机规模集的标识部分中找到此信息：

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
如果虚拟机规模集没有系统分配的托管标识，并且你想要从中删除所有用户分配的托管标识，请使用以下命令：

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
如果虚拟机规模集同时具有系统分配的托管标识和用户分配的托管标识，则可通过切换为仅使用系统分配的托管标识，删除所有用户分配的托管标识。

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>后续步骤

- [Azure 资源的托管标识概述](overview.md)
- 有关完整的 Azure VM 创建快速入门，请参阅：
  
  - [使用 PowerShell 创建 Windows 虚拟机](../../virtual-machines/windows/quick-create-powershell.md) 
  - [使用 PowerShell 创建 Linux 虚拟机](../../virtual-machines/linux/quick-create-powershell.md) 

















