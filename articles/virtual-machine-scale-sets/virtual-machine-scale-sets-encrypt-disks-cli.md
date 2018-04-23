---
title: Azure 虚拟机规模集加密磁盘 | Microsoft Docs
description: 了解如何对虚拟机规模集中附加的磁盘进行加密。
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: iainfou
ms.openlocfilehash: 570764ad5d657a8b1efa2425423a89ddc518451c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>对虚拟机规模集中的操作系统和附加的数据磁盘进行加密
Azure [虚拟机规模集](/azure/virtual-machine-scale-sets/)支持 Azure 磁盘加密 (ADE)。  可针对 Windows 和 Linux 虚拟机启用 Azure 磁盘加密，用于通过符合行业标准的加密技术来保护静态的规模集数据。 有关详细信息，请了解针对 Windows 和 Linux 虚拟机的 Azure 磁盘加密。

> [!NOTE]
>  目前已公开发布针对虚拟机规模集的 Azure 磁盘加密，可在所有 Azure 公共区域使用。

下述项支持 Azure 磁盘加密：
- 使用托管磁盘创建的规模集，本机（或未托管）磁盘规模集不受支持。
- Windows 规模集中的 OS 和数据卷。 可对 Windows 规模集的 OS 和数据卷禁用加密功能。
- Linux 规模集中的数据卷。 Linux 规模集的当前预览版中不支持 OS 磁盘加密。

不可在当前预览版中升级规模集 VM 和重置其映像。 建议仅在测试环境中对虚拟机规模集预览版进行 Azure 磁盘加密。 请勿在预览版的生产环境中启用磁盘加密功能，因为你可能需要在加密的规模集中升级 OS 映像。

可查看[此处](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)，了解针对 Linux 规模集数据磁盘加密的端到端批处理文件示例。  此示例创建一个资源组（Linux 规模集），装载一个 5 GB 的数据磁盘，并对虚拟机规模集进行加密。

## <a name="prerequisites"></a>先决条件
安装最新版本的 [Azure Powershell](https://github.com/Azure/azure-powershell/releases) 或 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)（内附有加密命令）。

要对虚拟机规模集预览版使用 Azure 磁盘加密功能，需要使用以下 PowerShell 命令自行注册订阅： 

```powershell
Connect-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

请稍等 10 分钟左右，直到以下命令返回“已注册”状态： 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>创建 Azure Key Vault（针对磁盘加密启用）
在包含规模集的订阅和区域新建一个 Key Vault，并设置“EnabledForDiskEncryption”访问策略。

```azurecli
rgname="linuxdatadiskencryptiontest"
VaultName="encryptionvault321"

az keyvault create --name $VaultName --resource-group $rgname --enabled-for-disk-encryption
```

或者在包含规模集的订阅和区域中启用一个现有的 Key Vault 进行磁盘加密。

```azurecli
VaultName="encryptionvault321"
az keyvault update --name $VaultName --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>启用加密功能

以下命令可使用同一资源组中的 Key Vault 对正在运行的规模集中的数据磁盘进行加密。 还可使用模板对正在运行的 [Windows 规模集](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)或 [Linux 规模集](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)中的磁盘进行加密。

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"
EncryptionKeyVaultUrl="https://encryptionvaultlinuxsf.vault.azure.net"
VaultResourceId="/subscriptions/0754ecc2-d80d-426a-902c-b83f4cfbdc95/resourceGroups/linuxdatadiskencryptiontest/providers/Microsoft.KeyVault/vaults/encryptionvaultlinuxsf"

az vmss encryption enable -g $ResourceGroup -n $VmssName --disk-encryption-keyvault $VaultResourceId --volume-type DATA
az vmss update-instances -g $ResourceGroup -n $VmssName --instance-ids *
```

## <a name="check-encryption-progress"></a>查看加密进度

使用以下命令显示规模集的加密状态。

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption show -g $ResourceGroup -n $VmssName
```

## <a name="disable-encryption"></a>禁用加密功能
使用以下命令禁止对正在运行的虚拟机规模集进行加密。 还可使用模板禁止对正在运行的 [Windows VM 规模集](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows) 或 [Linux VM 规模集](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux) 进行加密。

```azurecli
ResourceGroup="linuxdatadiskencryptiontest"
VmssName="nt1vm"

az vmss encryption disable -g $ResourceGroup -n $VmssName
```

