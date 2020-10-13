---
title: 为 Windows 群集启用磁盘加密
description: 本文介绍如何在 Azure 资源管理器中使用 Azure Key Vault 为 Azure Service Fabric 群集节点启用磁盘加密。
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "78251820"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>为 Windows 中的 Azure Service Fabric 群集节点启用磁盘加密 
> [!div class="op_single_selector"]
> * [适用于 Windows 的磁盘加密](service-fabric-enable-azure-disk-encryption-windows.md)
> * [适用于 Linux 的磁盘加密](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

本教程介绍如何对 Windows 中的 Service Fabric 群集节点启用磁盘加密。 对于每个节点类型和虚拟机规模集，需要执行以下步骤。 为了加密节点，我们将使用虚拟机规模集上的 Azure 磁盘加密功能。

本指南涵盖以下主题：

* 对 Windows 中的 Service Fabric 群集虚拟机规模集启用磁盘加密时要注意的重要概念。
* 对 Windows 中的 Service Fabric 群集节点启用磁盘加密之前所要遵循的步骤。
* 对 Windows 中的 Service Fabric 群集节点启用磁盘加密所要遵循的步骤。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备条件

**自我注册** 

针对虚拟机规模集的磁盘加密预览版需要自我注册。 请使用以下步骤： 

1. 首先运行以下命令：
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 等待大约 10 分钟，直到状态显示为“已注册”。  可通过运行以下命令来检查状态。 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. 在规模集所在的同一订阅和区域中创建 Key Vault，然后在该 Key Vault 中使用相应的 PowerShell cmdlet 选择“EnabledForDiskEncryption”访问策略。  也可以运行以下命令，在 Azure 门户中使用 Azure Key Vault UI 设置策略。
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 安装最新版本的 [Azure CLI](/cli/azure/install-azure-cli)，其中包含新的加密命令。
3. 安装最新版本的 [Azure PowerShell 中的 Azure SDK](https://github.com/Azure/azure-powershell/releases)。 以下是在规模集实例上启用 ([Set](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) 加密、检索 ([Get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) 加密状态以及删除 ([disable](/powershell/module/az.compute/disable-azvmssdiskencryption)) 加密所需的虚拟机规模集 Azure 磁盘加密 cmdlet。

| Command | 版本 |  源  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 或更高版本 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 或更高版本 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>受支持的磁盘加密方案
* 只有使用托管磁盘创建的规模集才支持虚拟机规模集加密， 本机（或非托管）磁盘规模集则不支持。
* Windows 虚拟机规模集中的 OS 和数据卷支持加密。 Windows 虚拟机规模集中的 OS 和数据卷还支持禁用加密。
* 当前预览版不支持对虚拟机规模集执行虚拟机重置映像和升级操作。


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

如果需要根据需求编写自定义模板，我们强烈建议从 [Azure Service Fabric 群集创建模板示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)页中提供的某个模板着手。 若要[自定义群集模板][customize-your-cluster-template]，请参阅以下指导。

如果已有一个自定义模板，请仔细检查模板中的所有三个证书相关参数以及参数文件已按如下所示命名且如下所示的值为 null：

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>将应用程序部署到 Windows 中的 Service Fabric 群集
若要将应用程序部署到群集，请遵循[使用 PowerShell 部署和删除应用程序](service-fabric-deploy-remove-applications.md)中的步骤和指导。


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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>验证是否为 Windows 中的虚拟机规模集启用了磁盘加密
运行以下命令，获取整个虚拟机规模集或规模集中任意实例的状态。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


此外，可以登录到虚拟机规模集，并确保驱动器已加密。

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
现在，你应已获得一个安全群集，并知道了如何为 Service Fabric 群集节点与虚拟机规模集启用和禁用磁盘加密。 有关适用于 Linux 中 Service Fabric 群集节点的类似指导，请参阅[适用于 Linux 的磁盘加密](service-fabric-enable-azure-disk-encryption-linux.md)。

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
