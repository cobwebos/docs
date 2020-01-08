---
title: 为 Windows 群集启用磁盘加密
description: 本文介绍如何使用 Azure 资源管理器中的 Azure Key Vault 为 Azure Service Fabric 群集节点启用磁盘加密。
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: 24405c3c34bfd7b88932101c42a8d0fc96c90fa1
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614445"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>在 Windows 中启用 Azure Service Fabric 群集节点的磁盘加密 
> [!div class="op_single_selector"]
> * [适用于 Windows 的磁盘加密](service-fabric-enable-azure-disk-encryption-windows.md)
> * [适用于 Linux 的磁盘加密](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

在本教程中，你将了解如何在 Windows 中 Service Fabric 群集节点上启用磁盘加密。 对于每个节点类型和虚拟机规模集，需要执行以下步骤。 对于加密节点，我们将在虚拟机规模集上使用 Azure 磁盘加密功能。

本指南包括以下主题：

* 在 Windows 中 Service Fabric 群集虚拟机规模集上启用磁盘加密时要注意的关键概念。
* 在 Windows Service Fabric 群集节点上启用磁盘加密之前要遵循的步骤。
* 在 Windows Service Fabric 群集节点上启用磁盘加密所遵循的步骤。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件

**自注册** 

虚拟机规模集的磁盘加密预览需要自行注册。 请使用以下步骤： 

1. 首先，运行以下命令：
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 等待大约10分钟，直到*注册*了状态。 可以通过运行以下命令来检查状态： 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. 在与规模集相同的订阅和区域中创建密钥保管库，并使用其 PowerShell cmdlet 在 key vault 上选择**EnabledForDiskEncryption**访问策略。 还可以通过使用 Azure 门户中的 Key Vault UI，使用以下命令设置策略：
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 安装最新版本的[Azure CLI](/cli/azure/install-azure-cli)，其中包含新的加密命令。
3. 从 Azure PowerShell 版本安装最新版本的[AZURE SDK](https://github.com/Azure/azure-powershell/releases) 。 下面是虚拟机规模集的 Azure 磁盘加密 cmdlet，可在规模集实例上启用（[设置](/powershell/module/az.compute/set-azvmssdiskencryptionextension)）加密、检索（[获取](/powershell/module/az.compute/get-azvmssvmdiskencryption)）加密状态以及删除（[禁用](/powershell/module/az.compute/disable-azvmssdiskencryption)）加密。

| 命令 | 版本 |  源  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 或更高版本 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 或更高版本 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 或更高版本 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>受支持的磁盘加密方案
* 仅对用托管磁盘创建的规模集支持虚拟机规模集加密。 本机（或非托管）磁盘规模集则不支持。
* Windows 中虚拟机规模集的 OS 和数据卷支持加密。 Windows 中的虚拟机规模集的 OS 和数据卷也支持禁用加密。
* 当前预览不支持虚拟机规模集的虚拟机重置映像和升级操作。


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>创建新群集并启用磁盘加密

可使用以下命令通过 Azure 资源管理器模板和自签名证书来创建群集并启用磁盘加密。

### <a name="sign-in-to-azure"></a>登录 Azure 
用以下命令登录：
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>使用现有的自定义模板 

如果你需要创作自定义模板以满足你的需求，我们强烈建议你从[Azure Service Fabric 群集创建模板示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)"页上提供的一个模板开始。 若要[自定义群集模板][customize-your-cluster-template]部分，请参阅以下指南。

如果已经有一个自定义模板，请仔细检查模板和参数文件中的所有三个证书相关参数的名称是否如下，并按如下所示命名值：

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
若要将应用程序部署到群集，请按照[使用 PowerShell 部署和删除应用程序](service-fabric-deploy-remove-applications.md)中的步骤和指南进行操作。


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>为之前创建的虚拟机规模集启用磁盘加密

若要为通过前面的步骤创建的虚拟机规模集启用磁盘加密，请运行以下命令：
 
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
通过运行以下命令，获取整个虚拟机规模集或规模集中的任何实例的状态。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


此外，还可以登录到虚拟机规模集，确保驱动器已加密。

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>禁用 Service Fabric 群集中虚拟机规模集的磁盘加密 
通过运行以下命令为虚拟机规模集禁用磁盘加密。 请注意，禁用磁盘加密适用于整个虚拟机规模集，而不是单个实例。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>后续步骤
此时，应该有一个安全的群集，并且知道如何启用和禁用 Service Fabric 群集节点和虚拟机规模集的磁盘加密。 有关 Linux 中 Service Fabric 群集节点的类似指导，请参阅[适用于 linux 的磁盘加密](service-fabric-enable-azure-disk-encryption-linux.md)。

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
