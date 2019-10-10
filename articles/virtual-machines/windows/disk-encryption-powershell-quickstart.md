---
title: 使用 Azure PowerShell 创建和加密 Windows VM
description: 本快速入门介绍如何使用 Azure PowerShell 创建和加密 Windows 虚拟机
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: b5e5b44742c85591b913b94e622c76a2aba111ce
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245707"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>快速入门：使用 PowerShell 在 Azure 中创建和加密 Windows 虚拟机

Azure PowerShell 模块用于从 PowerShell 命令行或脚本创建和管理 Azure 资源。 本快速入门介绍如何使用 Azure PowerShell 模块创建 Windows 虚拟机 (VM)、创建用于存储加密密钥的密钥保管库以及加密 VM。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。


## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 创建 Azure 资源组。 资源组是在其中部署和管理 Azure 资源的逻辑容器：

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [New-AzVM](/powershell/module/az.compute/new-azvm) 创建 Azure 虚拟机。 必须向 cmdlet 提供凭据。 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

部署 VM 需要数分钟。 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>创建为加密密钥配置的密钥保管库

Azure 磁盘加密将其加密密钥存储在 Azure 密钥保管库中。 使用 [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) 创建一个密钥保管库。 要使密钥保管库能够存储加密密钥，请使用 -EnabledForDiskEncryption 参数。

> [!Important]
> 每个密钥保管库必须具有唯一的名称。 以下示例创建名为“myKV”  的密钥保管库，但你必须将其命名为不同的名称。

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>加密虚拟机

使用 [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) 加密 VM。 

Set-AzVmDiskEncryptionExtension 需要密钥保管库对象中的一些值。 可以通过将密钥保管库的唯一名称传递给 [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault) 来获取这些值。

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

几分钟后，进程将返回以下内容：

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

可以通过运行 [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus) 来验证加密过程。

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

启用加密后，你将在返回的输出中看到以下内容：

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>清理资源

不再需要时，可以使用 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet 删除资源组、VM 和所有相关资源：

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你创建了一个虚拟机，创建了一个启用加密密钥的密钥保管库，并对 VM 进行了加密。  请继续学习下一篇文章，详细了解 IaaS VM 的 Azure 磁盘加密先决条件。

> [!div class="nextstepaction"]
> [Azure 磁盘加密概述](disk-encryption-overview.md)