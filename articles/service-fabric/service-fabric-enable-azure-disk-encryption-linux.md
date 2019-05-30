---
title: 适用于 Azure Service Fabric Linux 群集启用磁盘加密 |Microsoft Docs
description: 本文介绍如何使用 Azure 资源管理器和 Azure 密钥保管库启用适用于在 Linux 中的 Azure Service Fabric 群集节点磁盘加密。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: 47b07188d1757708fb494c6a66e93379657e806a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258765"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>为 Azure Service Fabric 群集节点，在 Linux 中启用磁盘加密 
> [!div class="op_single_selector"]
> * [适用于 Linux 的磁盘加密](service-fabric-enable-azure-disk-encryption-linux.md)
> * [适用于 Windows 的磁盘加密](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

在本教程中，您将了解如何在 Linux 中的 Azure Service Fabric 群集节点上启用磁盘加密。 你将需要为每个节点类型和虚拟机规模集执行以下步骤。 用于加密节点，我们将虚拟机规模集上使用的 Azure 磁盘加密功能。

本指南涵盖以下主题：

* 需要注意的 Service Fabric 群集虚拟机规模上的启用磁盘加密在 Linux 中的设置时的重要概念。
* 启用 Service Fabric 上的磁盘加密之前，需要遵循的步骤群集在 Linux 中的节点。
* 若要在 Linux 中的 Service Fabric 群集节点上启用磁盘加密，需要遵循的步骤。



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

 **Self-registration**

虚拟机规模集磁盘加密预览版需要自注册。 请执行以下步骤：

1. 运行以下命令： 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 等待大约 10 分钟，直到状态显示为*已注册*。 可以通过运行以下命令检查状态：
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. 在规模集所在的同一订阅和区域中创建 Key Vault。 然后选择**EnabledForDiskEncryption**使用其 PowerShell cmdlet 访问密钥保管库上的策略。 此外可以使用以下命令在 Azure 门户中使用密钥保管库 UI 设置策略：
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 安装最新版本[Azure CLI](/cli/azure/install-azure-cli)，其中包含新的加密命令。

3. 安装最新版本[从 Azure PowerShell 的 Azure SDK](https://github.com/Azure/azure-powershell/releases)发布。 以下是虚拟机规模集启用 Azure 磁盘加密 cmdlet ([设置](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) 加密，检索 ([获取](/powershell/module/az.compute/get-azvmssvmdiskencryption)) 加密状态和删除 ([禁用](/powershell/module/az.compute/disable-azvmssdiskencryption))在刻度上的加密设置实例。


| 命令 | Version |  源  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 或更高版本 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 或更高版本 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>受支持的磁盘加密方案
* 加密虚拟机规模集仅支持使用托管磁盘创建的规模集。 本机（或非托管）磁盘规模集则不支持。
* 加密和禁用加密支持在 Linux 中的虚拟机规模集的 OS 和数据卷。 
* 虚拟机规模集的虚拟机 (VM) 重置映像和升级操作不支持在当前的预览版。


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>创建新群集并启用磁盘加密

使用以下命令创建群集并通过使用 Azure 资源管理器模板和自签名的证书启用磁盘加密。

### <a name="sign-in-to-azure"></a>登录 Azure  

使用以下命令登录：

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>使用现有的自定义模板 

如果您需要编写自定义模板，我们强烈建议您在使用其中一个模板[Azure Service Fabric 群集创建模板示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)页。 

如果已有自定义模板，请仔细检查中的所有三个与证书相关参数的模板和参数文件命名，如下所示。 此外请确保值为 null，如下所示：

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

因为唯一数据磁盘加密支持的虚拟机规模集在 Linux 中，您必须使用资源管理器模板添加数据磁盘。 按如下所示更新数据磁盘预配模板：

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

下面是等效的 CLI 命令。 将在 declare 语句中的值更改为适当的值。 CLI 支持上述 PowerShell 命令所支持的所有其他参数。

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>装载到 Linux 实例的数据磁盘
继续使用虚拟机规模集上的加密之前，请确保正确地装载添加的数据磁盘。 登录到 Linux 群集 VM 并运行**LSBLK**命令。 输出应显示在该添加的数据磁盘**装入点**列。


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>应用程序部署到在 Linux 中的 Service Fabric 群集
若要部署到群集的应用程序，请按照步骤和相关指导[快速入门：将 Linux 容器部署到 Service Fabric](service-fabric-quickstart-containers-linux.md)。


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>为以前创建的虚拟机规模集启用磁盘加密
若要启用的虚拟机规模集的磁盘加密设置你通过前面的步骤，运行以下命令创建：
 
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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>验证是否为虚拟机规模集在 Linux 中启用磁盘加密
若要在规模集中获取整个虚拟机规模集或任何实例的状态，请运行以下命令。
此外，你可以登录到 Linux 群集 VM 并运行**LSBLK**命令。 输出应显示中添加的数据磁盘**装入点**列中，并**类型**列应阅读*Crypt*。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>禁用虚拟机规模集的 Service Fabric 群集的磁盘加密
为禁用磁盘加密的虚拟机规模集通过运行以下命令。 请注意，禁用磁盘加密适用于整个虚拟机规模集而不是单个实例。

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>后续步骤
此时，应已创建安全群集，并了解如何启用和禁用适用于 Service Fabric 群集节点和虚拟机规模集磁盘加密。 在 Linux 中的 Service Fabric 群集节点上的类似指南，请参阅[磁盘加密的 Windows](service-fabric-enable-azure-disk-encryption-windows.md)。 
