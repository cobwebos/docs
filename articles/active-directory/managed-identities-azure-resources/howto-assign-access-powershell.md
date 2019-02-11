---
title: 如何使用 PowerShell 向托管标识分配对 Azure 资源的访问权限
description: 分步说明如何使用 PowerShell 将托管标识分配给一个资源，将访问权限分配给另一个资源。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: priyamo
ms.openlocfilehash: 765276ce179c0d9858a39a62adc5ea0e96ae79ea
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188773"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>使用 PowerShell 向托管标识分配对资源的访问权限

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

为 Azure 资源配置托管标识后，便可以授予该托管标识对其他资源的访问权限，这一点与安全主体一样。 本示例展示了如何使用 PowerShell 为 Azure 虚拟机的托管标识提供对 Azure 存储帐户的访问权限。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#how-does-it-work)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 安装[最新版本的 Azure PowerShell](/powershell/azure/install-az-ps)（如果尚未安装）。

## <a name="use-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>使用 RBAC 授予托管标识对另一资源的访问权限

在 Azure 资源（[如 Azure VM](qs-configure-powershell-windows-vm.md)）上启用托管标识后：

1. 使用 `Connect-AzAccount` cmdlet 登录到 Azure。 使用与配置了托管标识的 Azure 订阅关联的帐户：

   ```powershell
   Connect-AzAccount
   ```
2. 此示例要授予 Azure VM 对存储帐户的访问权限。 首先，我们使用 [Get-AzVM](/powershell/module/az.compute/get-azvm) 获取名为 `myVM` 的 VM 的服务主体，该 VM 是在启用托管标识时创建的。 然后，使用 [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) 向 VM 提供对名为 `myStorageAcct` 的存储帐户的“读者”访问权限：

    ```powershell
    $spID = (Get-Az -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>后续步骤

- [Azure 资源托管标识概述](overview.md)
- 若要启用 Azure VM 上的托管标识，请参阅[使用 PowerShell 在 VM 上配置 Azure 资源的托管标识](qs-configure-powershell-windows-vm.md)。
