---
title: 使用 Azure CLI 加密 Azure 规模集磁盘 | Microsoft Docs
description: 了解如何使用 Azure CLI 2.0 加密 Linux 虚拟机规模集中的 VM 实例和附加磁盘
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: cynthn
ms.openlocfilehash: a01a0ae09b91b550af4617a46f7c0d8647a5f4be
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38704549"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli-20-preview"></a>使用 Azure CLI 2.0（预览版）对虚拟机规模集中的 OS 和附加数据磁盘进行加密

虚拟机规模集支持 Azure 磁盘加密 (ADE)，可让用户使用行业标准的加密技术来保护静态数据。 可为 Linux 和 Windows 虚拟机规模集启用加密。 有关详细信息，请参阅[适用于 Linux 和 Windows 的 Azure 磁盘加密](../security/azure-security-disk-encryption.md)。

> [!NOTE]
>  目前已公开发布针对虚拟机规模集的 Azure 磁盘加密，可在所有 Azure 公共区域使用。

下述项支持 Azure 磁盘加密：
- 使用托管磁盘创建的规模集，本机（或未托管）磁盘规模集不受支持。
- Windows 规模集中的 OS 和数据卷。 可对 Windows 规模集的 OS 和数据卷禁用加密功能。
- Linux 规模集中的数据卷。 Linux 规模集的当前预览版中不支持 OS 磁盘加密。

不可在当前预览版中升级规模集 VM 和重置其映像。 建议仅在测试环境中对虚拟机规模集预览版进行 Azure 磁盘加密。 请勿在预览版的生产环境中启用磁盘加密功能，因为你可能需要在加密的规模集中升级 OS 映像。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.31 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="register-for-disk-encryption-preview"></a>注册磁盘加密预览版

要对虚拟机规模集预览版使用 Azure 磁盘加密功能，需要使用 [az feature register](/cli/azure/feature#az_feature_register) 自行注册订阅。 只需在首次使用磁盘加密预览版功能时执行以下步骤：

```azurecli-interactive
az feature register --name UnifiedDiskEncryption --namespace Microsoft.Compute
```

传播注册请求最多可能需要 10 分钟。 可以通过 [az feature show](/cli/azure/feature#az_feature_show) 查看注册状态。 `State` 报告处于“已注册”状态后，通过 [az provider register](/cli/azure/provider#az_provider_register) 重新注册 Mirosoft.Compute 提供程序：

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="create-a-scale-set"></a>创建规模集

使用 [az group create](/cli/azure/group#az_group_create) 创建资源组，才能创建规模集。 以下示例在 eastus 位置创建名为 myResourceGroup 的资源组：

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

现在，使用 [az vmss create](/cli/azure/vmss#az_vmss_create) 创建虚拟机规模集。 以下示例创建名为 *myScaleSet* 的规模集，该规模集设置为在应用更改时自动更新；如果 *~/.ssh/id_rsa* 中没有 SSH 密钥，此示例还会生成 SSH 密钥。 每个 VM 实例附有 32Gb 的数据磁盘，可使用 Azure [自定义脚本扩展](../virtual-machines/linux/extensions-customscript.md)通过 [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) 准备数据磁盘：

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

创建和配置所有的规模集资源和 VM 需要几分钟时间。

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>创建 Azure Key Vault（针对磁盘加密启用）

Azure 密钥保管库可以存储能够在应用程序和服务中安全实现的密钥、机密或密码。 使用软件保护将加密密钥存储在 Azure 密钥保管库中，或者，可在已通过 FIPS 140-2 级别 2 标准认证的硬件安全模块 (HSM) 中导入或生成密钥。 这些加密密钥用于加密和解密附加到 VM 的虚拟磁盘。 可以控制这些加密密钥，以及审核对它们的使用。

定义自己的唯一 keyvault_name。 然后，在规模集所在的同一订阅和区域中，通过 [az keyvault create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) 创建 KeyVault，并设置 --enabled-for-disk-encryption 访问策略。

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>使用现有的密钥保管库

仅当你要将现有的 Key Vault 用于磁盘加密时，才需要执行此步骤。 如果在上一部分中创建了 Key Vault，请跳过此步骤。

定义自己的唯一 keyvault_name。 然后，通过 [az keyvault update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) 更新 KeyVault 并设置 --enabled-for-disk-encryption 访问策略。

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>启用加密功能

若要加密规模集中的 VM 实例，请先使用 [az keyvault show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show) 获取有关 Key Vault 资源 ID 的信息。 然后，通过 [az vmss encryption enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable) 使用这些变量启动加密过程：

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

启动加密过程可能需要一到两分钟时间。

因为之前步骤中创建的规模集上的升级策略设置为自动，所以 VM 实例将自动启动加密过程。 在升级策略设为手动的规模集上，通过 [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances) 在 VM 实例上启动加密策略。

## <a name="check-encryption-progress"></a>查看加密进度

若要检查磁盘加密状态，请使用 [az vmss encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show)：

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

加密 VM 实例后，状态代码将报告 EncryptionState/encrypted，如下面的示例输出所示：

```bash
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>禁用加密功能

如果不再想要使用加密的 VM 实例磁盘，可以使用 [az vmss encryption disable](/cli/azure/vmss/encryption?view=azure-cli-latest#az-vmss-encryption-disable) 禁用加密，如下所示：

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>后续步骤

在本文中，已使用 Azure CLI 2.0 加密虚拟机规模集。 也可以使用 [Azure PowerShell](virtual-machine-scale-sets-encrypt-disks-ps.md)，或适用于 [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) 或 [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox) 的模板。

可查看[此处](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat)，了解针对 Linux 规模集数据磁盘加密的端到端批处理文件示例。 此示例创建一个资源组（Linux 规模集），装载一个 5 GB 的数据磁盘，并对虚拟机规模集进行加密。