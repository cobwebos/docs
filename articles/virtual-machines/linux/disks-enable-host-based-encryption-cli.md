---
title: 使用 Azure CLI 托管磁盘上的加密来启用端到端加密
description: 使用主机上的加密在 Azure 托管磁盘上启用端到端加密。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/10/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 9f61835887c26e41b3338286065df4ca9d05f513
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029002"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-cli"></a>使用主机上的加密启用端到端加密-Azure CLI

当你在主机上启用加密时，存储在 VM 主机上的数据将静态加密，并加密为存储服务。 有关主机加密以及其他托管磁盘加密类型的概念信息，请参阅[VM 数据的主机端对端加密](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>支持的区域

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>支持的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

你还可以通过编程方式找到 VM 大小。 若要了解如何以编程方式检索它们，请参阅[查找支持的 VM 大小](#finding-supported-vm-sizes)部分。

## <a name="prerequisites"></a>先决条件

为了能够在 Vm 或虚拟机规模集的主机上使用加密，必须在订阅上启用该功能。 使用你的订阅 Id 向发送电子邮件 encryptionAtHost@microsoft.com ，以便为你的订阅启用该功能。

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>创建 Azure Key Vault 和 DiskEncryptionSet

启用该功能后，需要设置 Azure Key Vault 和 DiskEncryptionSet （如果尚未这样做）。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-cli](../../../includes/virtual-machines-disks-encryption-create-key-vault-cli.md)]

## <a name="examples"></a>示例

### <a name="create-a-vm-with-encryption-at-host-enabled-with-customer-managed-keys"></a>在启用了客户管理密钥的主机上创建具有加密的 VM。 

使用之前创建的 DiskEncryptionSet 的资源 URI 创建具有托管磁盘的 VM，以使用客户管理的密钥加密 OS 和数据磁盘的缓存。 临时磁盘用平台托管密钥进行加密。 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 128 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-vm-with-encryption-at-host-enabled-with-platform-managed-keys"></a>在启用了平台管理的密钥的主机上创建具有加密的 VM。 

创建一个在主机上启用了加密的 VM，以便使用平台管理的密钥加密 OS/数据磁盘和临时磁盘的缓存。 

```azurecli
rgName=yourRGName
vmName=yourVMName
location=eastus
vmSize=Standard_DS2_v2
image=UbuntuLTS 

az vm create -g $rgName \
-n $vmName \
-l $location \
--encryption-at-host \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 128 \
```

### <a name="update-a-vm-to-enable-encryption-at-host"></a>更新 VM 以便在主机上启用加密。 

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm update -n $vmName \
-g $rgName \
--set securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-vm"></a>在主机上检查 VM 的加密状态

```azurecli
rgName=yourRGName
vmName=yourVMName

az vm show -n $vmName \
-g $rgName \
--query [securityProfile.encryptionAtHost] -o tsv
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-customer-managed-keys"></a>使用客户托管密钥启用的主机上的加密创建虚拟机规模集。 

使用之前创建的 DiskEncryptionSet 的资源 URI 创建具有托管磁盘的虚拟机规模集，以便使用客户管理的密钥加密 OS 和数据磁盘的缓存。 临时磁盘用平台托管密钥进行加密。 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskEncryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--os-disk-encryption-set $diskEncryptionSetId \
--data-disk-sizes-gb 64 128 \
--data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

### <a name="create-a-virtual-machine-scale-set-with-encryption-at-host-enabled-with-platform-managed-keys"></a>使用启用了平台托管密钥的主机上的加密创建虚拟机规模集。 

创建在主机上启用了加密的虚拟机规模集，以便使用平台管理的密钥加密 OS/数据磁盘和临时磁盘的缓存。 

```azurecli
rgName=yourRGName
vmssName=yourVMSSName
location=westus2
vmSize=Standard_DS3_V2
image=UbuntuLTS 

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--admin-username azureuser \
--generate-ssh-keys \
--data-disk-sizes-gb 64 128 \
```

### <a name="update-a-virtual-machine-scale-set-to-enable-encryption-at-host"></a>更新虚拟机规模集以便在主机上启用加密。 

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss update -n $vmssName \
-g $rgName \
--set virtualMachineProfile.securityProfile.encryptionAtHost=true
```

### <a name="check-the-status-of-encryption-at-host-for-a-virtual-machine-scale-set"></a>在主机上检查虚拟机规模集的加密状态

```azurecli
rgName=yourRGName
vmssName=yourVMName

az vmss show -n $vmssName \
-g $rgName \
--query [virtualMachineProfile.securityProfile.encryptionAtHost] -o tsv
```

## <a name="finding-supported-vm-sizes"></a>查找支持的 VM 大小

不支持旧的 VM 大小。 可以通过以下任一方法查找受支持的 VM 大小列表：

调用[资源 SKU API](/rest/api/compute/resourceskus/list)并检查 `EncryptionAtHostSupported` 功能是否设置为**True**。

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

或调用[AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) PowerShell cmdlet。

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>后续步骤

现在，已创建并配置了这些资源，可以使用它们来保护托管磁盘。 下面的链接包含示例脚本，每个脚本都有各自的方案，可用于保护托管磁盘。

[Azure 资源管理器模板示例](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
