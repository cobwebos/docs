---
title: 启用 Azure Service Fabric Linux 群集的磁盘加密 |Microsoft Docs
description: 本文介绍如何使用 Azure 资源管理器和 Azure Key Vault 为 Linux 中的 Azure Service Fabric 群集节点启用磁盘加密。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: atsenthi
ms.openlocfilehash: 5bcfad63df69010851dde66b0c8935e63a509455
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599589"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>在 Linux 中启用 Azure Service Fabric 群集节点的磁盘加密 
> [!div class="op_single_selector"]
> * [适用于 Linux 的磁盘加密](service-fabric-enable-azure-disk-encryption-linux.md)
> * [适用于 Windows 的磁盘加密](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

在本教程中, 你将了解如何在 Linux 中的 Azure Service Fabric 群集节点上启用磁盘加密。 对于每个节点类型和虚拟机规模集, 需要执行以下步骤。 对于加密节点, 我们将在虚拟机规模集上使用 Azure 磁盘加密功能。

本指南包括以下主题:

* 在 Linux 中 Service Fabric 群集虚拟机规模集上启用磁盘加密时要注意的关键概念。
* 在 Linux 中 Service Fabric 群集节点上启用磁盘加密之前要遵循的步骤。
* 在 Linux Service Fabric 群集节点上启用磁盘加密所遵循的步骤。



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>系统必备

 **自注册**

虚拟机规模集的磁盘加密预览需要自行注册。 请执行以下步骤：

1. 运行下面的命令： 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 等待大约10分钟, 直到*注册*了状态。 可以通过运行以下命令来检查状态:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. 在规模集所在的同一订阅和区域中创建 Key Vault。 然后, 使用其 PowerShell cmdlet 选择密钥保管库上的**EnabledForDiskEncryption**访问策略。 还可以通过使用 Azure 门户中的 Key Vault UI, 使用以下命令设置策略:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 安装最新版本的[Azure CLI](/cli/azure/install-azure-cli), 其中包含新的加密命令。

3. 从 Azure PowerShell 版本安装最新版本的[AZURE SDK](https://github.com/Azure/azure-powershell/releases) 。 下面是虚拟机规模集的 Azure 磁盘加密 cmdlet, 可在规模集实例上启用 ([设置](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) 加密、检索 ([获取](/powershell/module/az.compute/get-azvmssvmdiskencryption)) 加密状态以及删除 ([禁用](/powershell/module/az.compute/disable-azvmssdiskencryption)) 加密。


| Command | Version |  Source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 或更高版本 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 或更高版本 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>受支持的磁盘加密方案
* 仅对用托管磁盘创建的规模集支持虚拟机规模集加密。 本机（或非托管）磁盘规模集则不支持。
* Linux 中的虚拟机规模集的 OS 和数据卷都支持加密和禁用加密。 
* 当前预览不支持虚拟机规模集的虚拟机 (VM) 映像和升级操作。


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>创建新群集并启用磁盘加密

使用以下命令创建群集, 并使用 Azure 资源管理器模板和自签名证书启用磁盘加密。

### <a name="sign-in-to-azure"></a>登录  Azure  

用以下命令登录:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>使用现有的自定义模板 

如果需要创作自定义模板, 我们强烈建议使用[Azure Service Fabric 群集创建模板示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)页上的模板之一。 

如果你已经有一个自定义模板, 请仔细检查模板中的所有三个证书相关参数以及参数文件的名称是否如下所示。 还要确保值为 null, 如下所示:

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

由于 Linux 中的虚拟机规模集仅支持数据磁盘加密, 因此你必须使用资源管理器模板添加数据磁盘。 按如下所示更新数据磁盘预配模板:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

下面是等效的 CLI 命令。 将 declare 语句中的值更改为相应的值。 CLI 支持上述 PowerShell 命令支持的所有其他参数。

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="mount-a-data-disk-to-a-linux-instance"></a>将数据磁盘装载到 Linux 实例
在虚拟机规模集上继续加密之前, 请确保已正确装入已添加的数据磁盘。 登录到 Linux 群集 VM, 并运行**LSBLK**命令。 输出应显示在**装入点**列中添加的数据磁盘。


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>将应用程序部署到 Linux 中的 Service Fabric 群集
若要将应用程序部署到群集, 请按照[快速入门中的步骤和指南进行操作:将 Linux 容器部署到](service-fabric-quickstart-containers-linux.md)Service Fabric。


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>为之前创建的虚拟机规模集启用磁盘加密
若要为通过前面的步骤创建的虚拟机规模集启用磁盘加密, 请运行以下命令:
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>验证是否为 Linux 中的虚拟机规模集启用了磁盘加密
若要获取整个虚拟机规模集或规模集中的任何实例的状态, 请运行以下命令。
此外, 还可以登录到 Linux 群集 VM 并运行**LSBLK**命令。 输出应在**装入点**列中显示添加的数据磁盘, 并且**类型**列应读取*dm-crypt*。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>禁用 Service Fabric 群集中虚拟机规模集的磁盘加密
通过运行以下命令为虚拟机规模集禁用磁盘加密。 请注意, 禁用磁盘加密适用于整个虚拟机规模集, 而不是单个实例。

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>后续步骤
此时, 应该有一个安全的群集, 并且知道如何启用和禁用 Service Fabric 群集节点和虚拟机规模集的磁盘加密。 有关 Linux 中 Service Fabric 群集节点的类似指导, 请参阅适用于[Windows 的磁盘加密](service-fabric-enable-azure-disk-encryption-windows.md)。 
