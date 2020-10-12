---
title: 为 Linux 群集启用磁盘加密
description: 本文介绍如何使用 Azure 资源管理器和 Azure Key Vault 为 Linux 中的 Azure Service Fabric 群集节点启用磁盘加密。
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "78252817"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>为 Linux 中的 Azure Service Fabric 群集节点启用磁盘加密 
> [!div class="op_single_selector"]
> * [适用于 Linux 的磁盘加密](service-fabric-enable-azure-disk-encryption-linux.md)
> * [适用于 Windows 的磁盘加密](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

本教程介绍如何对 Linux 中的 Azure Service Fabric 群集节点启用磁盘加密。 对于每个节点类型和虚拟机规模集，需要执行以下步骤。 为了加密节点，我们将使用虚拟机规模集上的 Azure 磁盘加密功能。

本指南涵盖以下主题：

* 对 Linux 中的 Service Fabric 群集虚拟机规模集启用磁盘加密时要注意的重要概念。
* 对 Linux 中的 Service Fabric 群集节点启用磁盘加密之前所要遵循的步骤。
* 对 Linux 中的 Service Fabric 群集节点启用磁盘加密所要遵循的步骤。



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先决条件

 **自我注册**

针对虚拟机规模集的磁盘加密预览版需要自我注册。 使用以下步骤：

1. 运行以下命令： 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 等待大约 10 分钟，直到状态显示为“已注册”。  可通过运行以下命令来检查状态。
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure 密钥保管库**

1. 在规模集所在的同一订阅和区域中创建 Key Vault。 然后，使用相应的 PowerShell cmdlet 在 Key Vault 中选择 **EnabledForDiskEncryption** 访问策略。 也可以运行以下命令，在 Azure 门户中使用 Azure Key Vault UI 设置策略。
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 安装最新版本的 [Azure CLI](/cli/azure/install-azure-cli)，其中包含新的加密命令。

3. 安装最新版本的 [Azure PowerShell 中的 Azure SDK](https://github.com/Azure/azure-powershell/releases)。 以下是在规模集实例上启用 ([Set](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) 加密、检索 ([Get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) 加密状态以及删除 ([disable](/powershell/module/az.compute/disable-azvmssdiskencryption)) 加密所需的虚拟机规模集 Azure 磁盘加密 cmdlet。


| 命令 | 版本 |  Source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 或更高版本 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 或更高版本 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>受支持的磁盘加密方案
* 只有使用托管磁盘创建的规模集才支持虚拟机规模集加密， 本机（或非托管）磁盘规模集则不支持。
* Linux 中的虚拟机规模集的 OS 和数据卷都支持加密和禁用加密。 
* 当前预览版不支持对虚拟机规模集执行虚拟机 (VM) 重置映像和升级操作。


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>新建群集并启用磁盘加密

可使用以下命令通过 Azure 资源管理器模板和自签名证书来创建群集并启用磁盘加密。

### <a name="sign-in-to-azure"></a>登录 Azure  

使用以下命令登录：

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>使用现有的自定义模板 

如果需要创作自定义模板，我们强烈建议使用 [Azure Service Fabric 群集创建模板示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)页上的某个模板。 

如果已有一个自定义模板，请反复检查模板中的所有三个证书相关参数以及参数文件是否已按如下所示命名。 另请确保值为 null，如下所示：

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

由于 Linux 中的虚拟机规模集仅支持数据磁盘加密，因此必须使用资源管理器模板添加一个数据磁盘。 更新模板的数据磁盘预配，如下所示：

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

下面是等效的 CLI 命令。 将声明语句中的值更改为适当的值。 CLI 支持上述 PowerShell 命令所支持的其他所有参数。

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
在继续对虚拟机规模集加密之前，请确保正确装载已添加的数据磁盘。 登录到 Linux 群集 VM 并运行 **LSBLK** 命令。 输出应在“装入点”列中显示添加的数据磁盘。 


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>将应用程序部署到 Linux 中的 Service Fabric 群集
若要将应用程序部署到群集，请遵循以下文章中的步骤和指导：[快速入门：将 Linux 容器部署到 Service Fabric](service-fabric-quickstart-containers-linux.md)。


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>为前面创建的虚拟机规模集启用磁盘加密
若要为通过上述步骤创建的虚拟机规模集启用磁盘加密，请运行以下命令：
 
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
若要获取整个虚拟机规模集或规模集中任意实例的状态，请运行以下命令。
此外，可以登录到 Linux 群集 VM 并运行 **LSBLK** 命令。 输出应在“装入点”列中显示已添加的数据磁盘，“类型”列应显示为“加密”。   

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>为 Service Fabric 群集中的虚拟机规模集禁用磁盘加密
运行以下命令，为虚拟机规模集禁用磁盘加密。 请注意，禁用磁盘加密适用于整个虚拟机规模集，而不适用于单个实例。

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>后续步骤
现在，你应已获得一个安全群集，并知道了如何为 Service Fabric 群集节点与虚拟机规模集启用和禁用磁盘加密。 有关适用于 Linux 中 Service Fabric 群集节点的类似指导，请参阅[适用于 Windows 的磁盘加密](service-fabric-enable-azure-disk-encryption-windows.md)。 
