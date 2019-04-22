---
title: 适用于 Azure Service Fabric Linux 群集启用磁盘加密 |Microsoft Docs
description: 本文介绍如何使用 Azure 资源管理器 Azure Key Vault 为 Azure 中的 Service Fabric 群集规模集启用磁盘加密。
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
ms.openlocfilehash: f580bf02b222f01a3d5aad1254f208791ea22b38
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046968"
---
# <a name="enable-disk-encryption-for-service-fabric-linux-cluster-nodes"></a>为 Service Fabric Linux 群集节点启用磁盘加密 
> [!div class="op_single_selector"]
> * [适用于 Linux 的磁盘加密](service-fabric-enable-azure-disk-encryption-linux.md)
> * [适用于 Windows 的磁盘加密](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

按照以下步骤在 Service Fabric Linux 群集节点上启用磁盘加密。 将需要为每个节点类型/虚拟机规模集执行这些步骤。 为了对节点加密，我们将利用虚拟机规模集上的 Azure 磁盘加密功能。

该指南包含下列步骤：

* 在 Service Fabric Linux 群集虚拟机规模集上启用磁盘加密时需要注意的关键概念。
* 在 Service Fabric Linux 群集虚拟机规模集上启用磁盘加密之前必须遵循的先决条件步骤。
* 在 Service Fabric Linux 群集虚拟机规模集上启用磁盘加密时要遵循的步骤。



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

* **自行注册**：若要使用虚拟机规模集磁盘加密预览版，需自行注册
* 可通过运行以下步骤自行注册订阅： 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* 等待大约 10 分钟，直到状态为“已注册”。 可通过运行以下命令来检查状态： 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault**：在与虚拟机规模集相同的订阅和区域中创建 KeyVault，并在 KeyVault 上使用其 PS cmdlet 设置访问策略“EnabledForDiskEncryption”。 也可以使用 Azure 门户中的 KeyVault UI 设置该策略： 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* 安装最新[Azure CLI](/cli/azure/install-azure-cli) ，其中包含新的加密命令。
* 从 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) 发行版安装最新版本的 Azure SDK。 以下是虚拟机规模集启用 ADE cmdlet ([设置](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) 加密，检索 ([获取](/powershell/module/az.compute/get-azvmssvmdiskencryption)) 加密状态和删除 ([禁用](/powershell/module/az.compute/disable-azvmssdiskencryption)) 刻度上的加密将实例设置。 

| 命令 | 版本 |  源  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 或更高版本 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 或更高版本 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>受支持的磁盘加密方案
* 只有使用托管磁盘创建的规模集才支持虚拟机规模集加密，本机（或非托管）磁盘规模集则不支持。
* Linux 虚拟机规模集的数据卷支持虚拟机规模集加密。 Linux 的当前预览版不支持 OS 磁盘加密。
* 虚拟机规模集 VM 重置映像和当前预览版中不支持的升级操作。


### <a name="create-new-linux-cluster-and-enable-disk-encryption"></a>新建 Linux 群集并启用磁盘加密

使用以下命令创建群集并启用磁盘加密使用 Azure 资源管理器模板和自签名的证书。

### <a name="sign-in-to-azure"></a>登录 Azure  

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>使用现有的自定义模板 

如果需要根据需要编写自定义模板，我们强烈建议从适用于 Linux 群集的 [Azure Service Fabric 模板示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)中提供的模板之一着手。 

如果已有一个自定义模板，请确保模板中的所有三个证书相关参数以及参数文件已按如下所示命名并使用 null 值。

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

由于 Linux 虚拟机规模集仅支持数据磁盘加密，因此，我们需要使用 Azure 资源管理器模板来添加数据磁盘。 更新模板的数据磁盘预配，如下所示：

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

下面是可实现相同目的的等效 CLI 命令。 将声明语句中的值更改为适当的值。 CLI 支持上述 PowerShell 命令所支持的其他所有参数。

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

#### <a name="linux-data-disk-mounting"></a>Linux 数据磁盘装载
在继续进行 Linux 虚拟机规模集加密之前，需确认是否已正确装载添加的数据磁盘。 登录到 Linux 群集 VM 并运行 LSBLK 命令。 输出应在装入点列中显示添加的数据磁盘。


#### <a name="deploy-application-to-linux-service-fabric-cluster"></a>将应用程序部署到 Linux Service Fabric 群集
遵照相关步骤和指南[将应用程序部署到群集](service-fabric-quickstart-containers-linux.md)


#### <a name="enable-disk-encryption-for-service-fabric-linux-cluster-virtual-machine-scale-set-created-above"></a>为上面创建的 Service Fabric Linux 群集虚拟机规模集启用磁盘加密
 
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

#### <a name="validate-if-disk-encryption-enabled-for-linux-virtual-machine-scale-set"></a>验证是否为 Linux 虚拟机规模集启用了磁盘加密。
获取整个虚拟机规模集或规模集中任意实例 VM 的状态。 请参阅下面的命令。
此外，用户可以登录到 Linux 群集 VM 和运行 LSBLK 命令。 输出应在装入点列中显示添加的数据磁盘，并且对于添加的数据磁盘，“类型”列应显示为“加密”。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>为 Service Fabric 群集虚拟机规模集禁用磁盘加密 
禁用磁盘加密适用于整个虚拟机规模集，而不适用于单个实例 

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>后续步骤
此时，通过了解如何为 Linux Service Fabric 群集虚拟机规模集启用/禁用磁盘加密，你已经拥有一个安全的群集。 接下来，[适用于 Windows 的磁盘加密](service-fabric-enable-azure-disk-encryption-windows.md) 
