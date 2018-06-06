---
title: 如何使用 PowerShell 将 MSI 访问权限分配给 Azure 资源
description: 分步说明如何使用 PowerShell 将 MSI 分配给一个资源，将访问权限分配给另一个资源。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 6d503526ed97dd1f61269acd83810cd44598d72f
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "33929123"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-powershell"></a>使用 PowerShell 将托管服务标识 (MSI) 访问权限分配给一个资源

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

为一个 Azure 资源配置 MSI 后，便可以向另一个资源授予 MSI 访问权限，就像任何安全主体一样。 本示例展示了如何使用 PowerShell 为 Azure 虚拟机的 MSI 提供对 Azure 存储帐户的访问权限。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

此处，安装 [Azure PowerShell 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1)（如果尚未安装）。

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>使用 RBAC 将 MSI 访问权限分配给另一个资源

在 Azure 资源（[如 Azure VM](qs-configure-powershell-windows-vm.md)）上启用 MSI 后：

1. 使用 `Connect-AzureRmAccount` cmdlet 登录到 Azure。 使用与其下配置了 MSI 的 Azure 订阅关联的帐户：

   ```powershell
   Connect-AzureRmAccount
   ```
2. 在本示例中，我们将向 Azure VM 提供对存储帐户的访问权限。 首先，我们使用 [Get-AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) 获取名为“myVM”的 VM 的服务主体，该 VM 在启用 MSI 时创建。 然后，我们使用 [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) 向 VM 提供对名为“myStorageAcct”的存储帐户的“读者”访问权限：

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="troubleshooting"></a>故障排除

如果资源的 MSI 未显示在可用标识列表中，请验证是否已正确启用 MSI。 在此示例中，可以在 [Azure 门户](https://portal.azure.com)中返回到 Azure VM，并执行以下操作：

- 查看“配置”页，并确保“MSI 已启用”配置为“是”。
- 查看“扩展”页，确保已成功部署 MSI 扩展。

如果有任一项不正确，可能都需要在资源上再次重新部署 MSI，或排查部署故障。

## <a name="related-content"></a>相关内容

- 有关 MSI 的概述，请参阅[托管服务标识概述](overview.md)。
- 若要在 Azure VM 上启用 MSI，请参阅[使用 PowerShell 配置 Azure VM 托管服务标识 (MSI)](qs-configure-powershell-windows-vm.md)。

使用以下评论部分提供反馈，帮助我们改进内容。

