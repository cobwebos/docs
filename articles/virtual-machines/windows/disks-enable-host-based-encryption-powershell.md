---
title: Azure PowerShell-在 VM 主机上启用端到端加密
description: 如何在主机上使用加密对 Azure Vm 启用端到端加密。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/10/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a13c17b2fcd6a42947b2f7d97f087735a5b18204
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235799"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-powershell"></a>使用主机上的加密启用端到端加密-Azure PowerShell

当你在主机上启用加密时，存储在 VM 主机上的数据将静态加密，并加密为存储服务。 有关主机加密以及其他托管磁盘加密类型的概念信息，请参阅[VM 数据的主机端对端加密](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)。

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>支持的区域

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>支持的 VM 大小

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

你还可以通过编程方式找到 VM 大小。 若要了解如何以编程方式检索它们，请参阅[查找支持的 VM 大小](#finding-supported-vm-sizes)部分。

## <a name="prerequisites"></a>必备知识

为了能够在 Vm 或虚拟机规模集的主机上使用加密，必须在订阅上启用该功能。 encryptionAtHost@microsoft使用你的订阅 id 向 .com 发送电子邮件，以便为你的订阅启用该功能。

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>创建 Azure Key Vault 和 DiskEncryptionSet

启用该功能后，需要设置 Azure Key Vault 和 DiskEncryptionSet （如果尚未这样做）。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>在主机上为附加到 VM 和虚拟机规模集的磁盘启用加密

可以通过在 Vm 或虚拟机规模集 securityProfile 下使用 API 版本**2020-06-01**及更高版本设置新的属性 EncryptionAtHost，在主机上启用加密。

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>示例脚本

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>在主机上为使用客户托管密钥连接到 VM 的磁盘启用加密

使用前面创建的 DiskEncryptionSet 的资源 URI 创建具有托管磁盘的 VM。

替换 `<yourPassword>` 、 `<yourVMName>` 、 `<yourVMSize>` 、 `<yourDESName>` 、 `<yoursubscriptionID>` 、 `<yourResourceGroupName>` 和 `<yourRegion>` ，然后运行该脚本。

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-diskEncryptionSetId "/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" `
-region "<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>在主机上为使用平台管理密钥连接到 VM 的磁盘启用加密

请替换 `<yourPassword>` 、、 `<yourVMName>` `<yourVMSize>` 、 `<yourResourceGroupName>` 和 `<yourRegion>` ，然后运行该脚本。

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-region "<yourRegion>"
```

## <a name="finding-supported-vm-sizes"></a>查找支持的 VM 大小

不支持旧的 VM 大小。 可以通过以下任一方法查找受支持的 VM 大小列表：

调用[资源 SKU API](https://docs.microsoft.com/rest/api/compute/resourceskus/list)并检查 `EncryptionAtHostSupported` 功能是否设置为**True**。

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

或调用[AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) PowerShell cmdlet。

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