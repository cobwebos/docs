---
title: 创建 Azure Service Fabric 群集 | Microsoft Docs
description: 了解如何使用 Azure 资源管理器在 Azure 中设置安全的 Service Fabric 群集。  可以使用默认模板或自己的群集模板创建群集。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: 709b59d257dd974e81d8b4058983f6e264ba0708
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925857"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>使用 Azure 资源管理器创建 Service Fabric 群集 
> [!div class="op_single_selector"]
> * [Azure 资源管理器](service-fabric-cluster-creation-via-arm.md)
> * [Azure 门户](service-fabric-cluster-creation-via-portal.md)
>
>

[Azure Service Fabric 群集](service-fabric-deploy-anywhere.md)是一组联网的虚拟机，可在其中部署和管理微服务。  在 Azure 中运行的 Service Fabric 群集是一种 Azure 资源，通过 Azure 资源管理器进行部署。 本文介绍如何使用资源管理器在 Azure 中安全地部署 Service Fabric 群集。 可以使用默认模板或自定义模板。  如果尚无自定义模板，请[了解如何创建自定义模板](service-fabric-cluster-creation-create-template.md)。

群集安全性是在首次设置群集时配置的，以后无法更改。 在设置群集之前，请先阅读 [Service Fabric 群集安全性方案][service-fabric-cluster-security]。 在 Azure 中，Service Fabric 使用 x509 证书来保护群集及其终结点，对客户端进行身份验证以及对数据进行加密。 另外，还建议使用 Azure Active Directory 来保护对管理终结点的访问。 在创建群集之前，必须先创建 Azure AD 租户和用户。  有关详细信息，请阅读[设置 Azure AD 来对客户端进行身份验证](service-fabric-cluster-creation-setup-aad.md)。

若要创建生产群集以运行生产工作负载，我们建议你首先阅读[生产就绪情况核对清单](service-fabric-production-readiness-checklist.md)。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>必备组件 
在本文中，使用 Service Fabric RM powershell 或 Azure CLI 模块部署群集：

* [Azure PowerShell 4.1 或更高版本][azure-powershell]
* [Azure CLI 版本 2.0 及更高版本][azure-CLI]

以下为关于 Service Fabric 模块的参考文档：
* [Az.ServiceFabric](https://docs.microsoft.com/powershell/module/az.servicefabric)
* [az SF CLI 模块](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>登录 Azure

运行本文中任何命令之前，请先登录到 Azure。

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>使用系统生成的自签名证书创建新群集

使用如下命令创建一个受系统生成的自签名证书保护的群集。 此命令将设置一个主要群集证书，用于保证群集的安全性以及设置使用该证书执行管理操作时所需的管理访问权限。  自签名证书适合用于保护测试群集。  应使用证书颁发机构 (CA) 提供的证书保护生产群集。

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>使用此模块中随附的默认群集模板

通过默认模板，使用以下命令并指定最少量的参数快速创建群集。

[Azure Service Fabric 模板示例：Windows 模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG)和 [Ubuntu 模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)中提供了所用的模板

以下命令可创建 Windows 或 Linux 群集，需指定相应的操作系统。 PowerShell/CLI 命令还会在指定的 CertificateOutputFolder 中输出证书，但要确保已创建该证书文件夹。 此命令还采用 VM SKU 等其他参数。

> [!NOTE]
> 以下 PowerShell 命令仅适用于 Azure PowerShell`Az`模块。 若要检查当前版本的 Azure 资源管理器 PowerShell 版本，请运行以下 PowerShell 命令"Get-module Az"。 单击[此链接](/powershell/azure/install-Az-ps)升级 Azure 资源管理器 PowerShell 的版本。 
>
>

使用 PowerShell 部署群集：

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

使用 Azure CLI 部署群集：

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>使用自己的自定义模板

如果需要根据需要编写自定义模板，我们强烈建议从 [Azure Service Fabric 模板示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)中提供的模板之一着手。 了解如何[自定义群集模板][customize-your-cluster-template]。

如果已有一个自定义模板，请仔细检查模板中的所有三个证书相关参数以及参数文件已按如下所示命名且如下所示的值为 null：

```json
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

使用 PowerShell 部署群集：

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

使用 Azure CLI 部署群集：

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

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>使用自己的 X.509 证书创建新的群集

如果想要使用证书来保护群集，请使用以下命令创建群集。

如果这是一个同时用于其他目的的 CA 签名证书，则我们建议专门为 Key Vault 提供一个不同的资源组。 建议将密钥保管库置于其资源组中。 这样可在不丢失密钥和机密的情况下删除计算和存储资源组，包括具有 Service Fabric 群集的资源组。 **包含 Key Vault 的资源组必须与正在使用它的群集位于同一区域。**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>使用模块中附带的默认“5 Node 1”节点类型模板
[Azure 示例：Windows 模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG)和 [Ubuntu 模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)中提供了所用的模板

使用 PowerShell 部署群集：

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

使用 Azure CLI 部署群集：

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>使用自己的自定义群集模板
如果需要根据需要编写自定义模板，我们强烈建议从 [Azure Service Fabric 模板示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)中提供的模板之一着手。 了解如何[自定义群集模板][customize-your-cluster-template]。

如果已有一个自定义模板，请确保模板中的所有三个证书相关参数以及参数文件已按如下所示命名并使用 null 值。

```json
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

使用 PowerShell 部署群集：

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

使用 Azure CLI 部署群集：

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>使用指向上传到密钥保管库中的机密的指针

要使用现有密钥保管库，则必须[针对部署启用](../key-vault/key-vault-manage-with-cli2.md#bkmk_KVperCLI)该密钥保管库，使计算资源提供程序能够从中获取证书并将其安装在群集节点上。

使用 PowerShell 部署群集：

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

使用 Azure CLI 部署群集：

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

## <a name="next-steps"></a>后续步骤
此时 Azure 中已正在运行一个安全的群集。 接下来，请[连接到该群集](service-fabric-connect-to-secure-cluster.md)，了解如何[管理应用程序机密](service-fabric-application-secret-management.md)。

有关要在模板中使用的 JSON 语法和属性，请参阅 [Microsoft.ServiceFabric/clusters 模板参考](/azure/templates/microsoft.servicefabric/clusters)。

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
