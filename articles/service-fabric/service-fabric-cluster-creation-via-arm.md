---
title: "根据模板创建 Azure Service Fabric 群集 | Microsoft Docs"
description: "本文介绍如何通过使用 Azure 资源管理器、Azure Key Vault 和 Azure Active Directory (Azure AD) 进行客户端身份验证，在 Azure 中设置一个安全的 Service Fabric 群集。"
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: chackdan
ms.openlocfilehash: 6675603bf741b1a668ba387c8304d2e2b7ab4e12
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>使用 Azure 资源管理器创建 Service Fabric 群集 
> [!div class="op_single_selector"]
> * [Azure 资源管理器](service-fabric-cluster-creation-via-arm.md)
> * [Azure portal](service-fabric-cluster-creation-via-portal.md)
>
>

本指南逐步介绍如何使用 Azure 资源管理器在 Azure 中设置安全的 Azure Service Fabric 群集。 本文篇幅较长。 然而，除非已完全熟悉内容，否则请确保仔细阅读每个步骤。

该指南包含下列步骤：

* 在部署 Service Fabric 群集之前需要了解的重要概念。
* 使用 Service Fabric 资源管理器模块在 Azure 中创建群集。
* 设置 Azure Active Directory (Azure AD)，以验证针对群集执行管理操作的用户的身份。
* 为群集编写并部署自定义的 Azure 资源管理器模板。

## <a name="key-concepts-to-be-aware-of"></a>要了解的重要概念
在 Azure 中，Service Fabric 强制要求使用 x509 证书来保护群集及其终结点。 证书在 Service Fabric 中用于提供身份验证和加密，为群集及其应用程序提供全方位的保护。 要访问客户端/针对群集执行管理操作（包括部署、升级和删除应用程序、服务及其包含的数据），可以使用证书或 Azure Active Directory 凭据。 强烈建议使用 Azure Active Directory，因为它是防止在客户端上共享证书的唯一方法。  有关如何在 Service Fabric 中使用证书的详细信息，请参阅 [Service Fabric 群集安全方案][service-fabric-cluster-security]。

Service Fabric 使用 X.509 证书保护群集，提供应用程序安全功能。 可以使用 [Key Vault][key-vault-get-started] 管理 Azure 中 Service Fabric 群集的证书。 


### <a name="cluster-and-server-certificate-required"></a>群集和服务器证书（必需）
必须使用这些证书（一个主要证书，以及一个可选的辅助证书）来保护群集，并防止未经授权的访问。 此证书通过两种方式保护群集：

* **群集身份验证：**在群集联合的情况下对节点间的通信进行身份验证。 只有可以使用此证书自我证明身份的节点才能加入群集。
* **服务器身份验证：**在管理客户端上对群集管理终结点进行身份验证，使管理客户端知道它正在与真正的群集而不是“中间人”通信。 此证书还通过 HTTPS 为 HTTPS 管理 API 和 Service Fabric Explorer 提供 SSL。

为满足这些用途，该证书必须符合以下要求：

* 证书必须包含私钥。 这些证书通常使用扩展名 .pfx 或 .pem  
* 必须为密钥交换创建证书，并且该证书可导出到个人信息交换 (.pfx) 文件。
* **证书的使用者名称必须与用于访问 Service Fabric 群集的域匹配**。 只有满足此匹配，才能为群集的 HTTPS 管理终结点和 Service Fabric Explorer 提供 SSL。 无法从证书颁发机构 (CA) 处获取针对 *.cloudapp.azure.com 域的 SSL 证书。 必须获取群集的自定义域名。 从 CA 请求证书时，该证书的使用者名称必须与用于群集的自定义域名匹配。

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>为客户端身份验证设置 Azure Active Directory（可选但建议的操作）

通过 Azure AD，组织（称为租户）可管理用户对应用程序的访问。 应用程序分为采用基于 Web 的登录 UI 的应用程序和采用本地客户端体验的应用程序。 本文假设已创建了一个租户。 如果未创建，请先阅读[如何获取 Azure Active Directory 租户][active-directory-howto-tenant]。

Service Fabric 群集提供其管理功能的各种入口点，包括基于 Web 的 [Service Fabric Explorer][service-fabric-visualizing-your-cluster] 和 [Visual Studio][service-fabric-manage-application-in-visual-studio]。 因此，需要创建两个 Azure AD 应用程序来控制对群集的访问：一个 Web 应用程序和一个本机应用程序。

本文档稍后将更详细地介绍如何设置这种配置。

### <a name="application-certificates-optional"></a>应用程序证书（可选）
可以出于应用程序安全目的，在群集上安装任意数量的附加证书。 在创建群集之前，请考虑需要在节点上安装证书的应用程序安全方案，例如：

* 加密和解密应用程序配置值。
* 在复制期间跨节点加密数据。

无论是 Linux 或 Windows 群集，创建安全群集的概念是相同的。 

### <a name="client-authentication-certificates-optional"></a>客户端身份验证证书（可选）
可以指定任意数量的其他证书用于管理操作或用户客户端操作。 默认情况下，群集证书具有管理客户端的特权。 不应将这些附加的客户端证书安装到群集中，只需指定在群集配置中允许这些证书；不过，在连接到群集和执行任何管理操作的客户端计算机上需要安装这些证书。


## <a name="prerequisites"></a>先决条件 
无论是 Linux 或 Windows 群集，创建安全群集的概念是相同的。 本指南将介绍如何使用 Azure PowerShell 或 Azure CLI 创建新群集。 先决条件为 

-  [Azure PowerShell 4.1 和更高版本][azure-powershell]或 [Azure CLI 2.0 和更高版本][azure-CLI]。
-  可以在[AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) 和 [az SF CLI 模块](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)中找到有关 Service Fabric 模块的详细信息


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>使用 Service Fabric 资源管理器模块部署群集

在本文档中，我们将使用 Service Fabric 资源管理器 PowerShell 和 CLI 模块来部署群集。在多种方案中都可以使用 PowerShell 或 CLI 模块命令。 让我们详细了解每种部署方法。 请选取最符合需要的方案。 

- 创建新群集 - 使用系统生成的自签名证书
    - 使用默认的群集模板
    - 使用现有的模板
- 创建新群集 - 使用已拥有的证书
    - 使用默认的群集模板
    - 使用现有的模板

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>创建新群集 - 使用系统生成的自签名证书

如果希望系统生成自签名证书并使用它来保护群集，请使用以下命令创建群集。 此命令将设置一个主要群集证书，用于保证群集的安全性以及设置使用该证书执行管理操作时所需的管理访问权限。

### <a name="login-in-to-azure"></a>登录到 Azure。

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```
#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module-to-set-up-the-cluster"></a>使用模块中随附的默认“5 Node 1 nodetype”模板设置群集

使用以下命令并指定最少量的参数快速创建群集

[Azure Service Fabric 模板示例：Windows 模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG)和 [Ubuntu 模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)中提供了所用的模板

以下命令用于创建 Windows 和 Linux 群集，你只需相应地指定 OS。 PowerShell/CLI 命令还会在指定的 CertificateOutputFolder 中输出证书。 命令还采用类似于 VM SKU 的其他参数。

```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password!4321" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

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

#### <a name="use-the-custom-template-that-you-already-have"></a>使用现有的自定义模板 

如果需要根据需要编写自定义模板，我们强烈建议从 [Azure Service Fabric 模板示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)中提供的模板之一着手。 请遵照下面[自定义群集模板][customize-your-cluster-template]部分中的指导和说明。

如果已有一个自定义模板，请确保已按如下所示将模板中的所有三个证书相关参数以及参数文件命名并使用 null 值。

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


```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

下面是可实现相同目的的等效 CLI 命令。 将声明语句中的值更改为适当的值。 CLI 支持上述 PowerShell 命令所支持的其他所有参数。

```CLI

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


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>创建新群集 - 使用从 CA 购买的或者现有的证书。

如果想要使用证书来保护群集，请使用以下命令创建群集。

如果这是一个同时用于其他目的的 CA 签名证书，则我们建议专门为 Key Vault 提供一个不同的资源组。 建议将密钥保管库置于其资源组中。 这样可在不丢失密钥和机密的情况下删除计算和存储资源组，包括具有 Service Fabric 群集的资源组。 **包含 Key Vault 的资源组必须与正在使用它的群集位于同一区域。**


#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module"></a>使用模块中随附的默认“5 Node 1 nodetype”模板
[Azure 示例：Windows 模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG)和 [Ubuntu 模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)中提供了所用的模板

```Powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

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

#### <a name="use-the-custom-template-that-you-have"></a>使用现有的自定义模板 
如果需要根据需要编写自定义模板，我们强烈建议从 [Azure Service Fabric 模板示例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)中提供的模板之一着手。 请遵照下面[自定义群集模板][customize-your-cluster-template]部分中的指导和说明。

如果已有一个自定义模板，请确保已按如下所示将模板中的所有三个证书相关参数以及参数文件命名并使用 null 值。

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


```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword #certPassword

```

下面是可实现相同目的的等效 CLI 命令。 将声明语句中的值更改为适当的值。

```CLI

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

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-keyvault"></a>使用指向已上传到 Key Vault 的机密的指针

要使用现有密钥保管库，则_必须针对部署启用_该密钥保管库，使计算资源提供程序能够从中获取证书并将其安装在群集节点上：

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment


$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroup -SecretIdentifier $secretID -TemplateFile $templateFile -ParameterFile $templateParmfile 

```
下面是可实现相同目的的等效 CLI 命令。 将声明语句中的值更改为适当的值。

```cli

declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifieraz $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 

```

<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>为客户端身份验证设置 Azure Active Directory

通过 Azure AD，组织（称为租户）可管理用户对应用程序的访问。 应用程序分为采用基于 Web 的登录 UI 的应用程序和采用本地客户端体验的应用程序。 本文假设已创建了一个租户。 如果未创建，请先阅读[如何获取 Azure Active Directory 租户][active-directory-howto-tenant]。

Service Fabric 群集提供其管理功能的各种入口点，包括基于 Web 的 [Service Fabric Explorer][service-fabric-visualizing-your-cluster] 和 [Visual Studio][service-fabric-manage-application-in-visual-studio]。 因此，需要创建两个 Azure AD 应用程序来控制对群集的访问：一个 Web 应用程序和一个本机应用程序。

为了简化涉及到配置 Azure AD 与 Service Fabric 群集的一些步骤，我们创建了一组 Windows PowerShell 脚本。

> [!NOTE]
> 在创建群集之前，请完成以下步骤。 因为脚本需要群集名称和终结点，这些值应是规划的值，而不是已创建的值。

1. [将脚本下载][sf-aad-ps-script-download]到计算机。
2. 右键单击 zip 文件，选择“属性”，“解除阻止”复选框，并单击“应用”。
3. 解压缩 zip 文件。
4. 运行 `SetupApplications.ps1` 并提供 TenantId、ClusterName 和 WebApplicationReplyUrl 作为参数。 例如：

```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'

```

执行 PowerShell 命令 `Get-AzureSubscription`，可找到租户 ID。 执行此命令，为每个订阅显示 TenantId。

将 ClusterName 用作脚本创建的 Azure AD 应用程序的前缀。 它不需要完全匹配实际的群集名称。 旨在更加轻松地将 Azure AD 项目映射到其配合使用的 Service Fabric 群集。

WebApplicationReplyUrl 是 Azure AD 在完成登录过程之后返回给用户的默认终结点。 将此终结点设置为群集的 Service Fabric Explorer 的终结点，默认值为：

https://&lt;cluster_domain&gt;:19080/Explorer

系统会提示登录到具有 Azure AD 租户管理权限的帐户。 完成此操作后，脚本会创建 Web 和本机应用程序来代表 Service Fabric 群集。 在 [Azure 门户][azure-portal]中查看租户的应用程序时，应会看到两个新条目：

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

在下一部分创建群集时该脚本显示 Azure 资源管理器模板所需的 JSON，因此最好不要关闭 PowerShell 窗口。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>创建 Service Fabric 群集 Resource Manager 模板
本部分面向想要编写自定义 Service Fabric 群集资源管理器模板的用户。 创建模板后，仍可以回过头来使用 PowerShell 或 CLI 模块部署该模板。 

[GitHub 上的 Azure 示例](https://github.com/Azure-Samples/service-fabric-cluster-templates)中提供了示例资源管理器模板。 这些模板可用作群集模板的起点。

### <a name="create-the-resource-manager-template"></a>创建 Resource Manager 模板
本指南使用 [5 节点安全群集][service-fabric-secure-cluster-5-node-1-nodetype]示例模板和模板参数。 将 `azuredeploy.json` 和 `azuredeploy.parameters.json` 下载到计算机，在偏好的文本编辑器中打开这两个文件。

### <a name="add-certificates"></a>添加证书
通过引用包含证书密钥的密钥保管库将证书添加到群集 Resource Manager 模板。 在资源管理器模板参数文件 (azuredeploy.parameters.json) 中添加这些 Key Vault 参数和值。 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>将所有证书都添加到虚拟机规模集 osProfile
必须在规模集资源 (Microsoft.Compute/virtualMachineScaleSets) 的 osProfile 节中配置在群集中安装的每个证书。 该操作会指示资源提供程序在 VM 上安装证书。 此安装包括群集证书和打算用于应用程序的任何应用程序安全证书：

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>配置 Service Fabric 群集证书
必须在 Service Fabric 群集资源 (Microsoft.ServiceFabric/clusters) 和 Service Fabric 扩展为虚拟机规模集资源中的虚拟机规模集配置群集身份验证证书。 通过此安排，Service Fabric 资源提供程序便可以将该证书配置为用于群集身份验证及管理终结点的服务器身份验证。

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>将证书信息添加到虚拟机规模集资源：
```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>将证书信息添加到 Service Fabric 群集资源：
```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>添加 Azure AD 配置以使用 Azure AD 访问客户端

通过引用包含证书密钥的 Key Vault，将 Azure AD 配置添加到群集资源管理器模板。 在资源管理器模板参数文件 (azuredeploy.parameters.json) 中添加这些 Azure AD 参数和值。

```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a name="populate-the-parameter-file-with-the-values"></a>在参数文件中填充值。
最后，使用 Key Vault 和 Azure AD PowerShell 命令的输出值填充参数文件：

如果你打算使用 Azure Service Fabric 资源管理器 PowerShell 模块，则不需要填充群集证书信息；如果希望系统生成自签名证书来保证群集的安全性，则只需将参数保留为 null。 

> [!NOTE]
> 要让资源管理器模块拾取并填充这些空参数值，参数名称必须与以下名称匹配
>

```json
        "clusterCertificateThumbprint": {
            "value": ""
        },
        "clusterCertificateUrlValue": {
            "value": ""
        },
        "sourceVaultvalue": {
            "value": ""
        },
```

如果使用的是应用程序证书或已上传到 Key Vault 的现有群集，则需要获取并填充此信息 

资源管理器模块无法自动生成 Azure AD 配置。 因此，如果你打算使用 Azure AD 访问客户端，则需要填充配置值。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

### <a name="test-your-template"></a>测试模板  
运行以下 PowerShell 命令，使用参数文件测试资源管理器模板：

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

如果遇到问题并收到含义模糊的消息，请使用“-Debug”作为选项。

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

下图演示密钥保管库和 Azure AD 配置在 Resource Manager 模板中的作用。

![Resource Manager 依赖关系图][cluster-security-arm-dependency-map]

## <a name="create-the-cluster-using-azure-resource-template"></a>使用 Azure 资源模板创建群集 

现在，可以使用本文档前面所述的步骤部署群集；如果已在参数文件中填充了值，则现在可以直接使用 [Azure 资源模板部署][resource-group-template-deploy]创建群集。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

如果遇到问题并收到含义模糊的消息，请使用“-Debug”作为选项。


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>将用户分配到角色
创建用于表示群集的应用程序后，请将用户分配到 Service Fabric 支持的角色：只读和管理员。可使用 [Azure 门户][azure-portal]来分配这些角色。

1. 在 Azure 门户中，选择右上角的租户。

    ![“选择租户”按钮][select-tenant-button]
2. 在左侧选项卡中选择“Azure Active Directory”，然后选择“企业应用程序”。
3. 选择“所有应用程序”，然后找到并选择名称为 `myTestCluster_Cluster` 的 Web 应用程序。
4. 单击“用户和组”选项卡。

    ![“用户和组”选项卡][users-and-groups-tab]
5. 单击新页面上的“添加用户”，选择要分配的用户和角色，然后单击页面底部的“选择”按钮。

    ![“将用户分配到角色”页面][assign-users-to-roles-page]
6. 单击页面底部的“分配”按钮。

    ![添加分配确认][assign-users-to-roles-confirm]

> [!NOTE]
> 有关 Service Fabric 中角色的详细信息，请参阅[适用于 Service Fabric 客户端的基于角色的访问控制](service-fabric-cluster-security-roles.md)。
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>有关排查 Azure Active Directory 设置问题的帮助
Azure AD 的设置和使用可能有一定难度，可以参考下面的一些指导来调试问题。

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer 提示选择证书
#### <a name="problem"></a>问题
成功登录到 Service Fabric Explorer 中的 Azure AD 后，浏览器返回到主页，但会出现提示用户选择证书的消息。

![SFX 证书对话框][sfx-select-certificate-dialog]

#### <a name="reason"></a>原因
未在 Azure AD 群集应用程序中为用户分配角色。 因此，Service Fabric 群集的 Azure AD 身份验证失败。 Service Fabric Explorer 将故障回复到证书身份验证。

#### <a name="solution"></a>解决方案
遵循有关设置 Azure AD 的说明操作，并为用户分配角色。 此外，我们建议打开“访问应用需要的用户分配”，如 `SetupApplications.ps1` 所示。

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>使用 PowerShell 连接失败并出现错误：“指定的凭据无效”
#### <a name="problem"></a>问题
使用 PowerShell 以“AzureActiveDirectory”安全模式连接到群集时，成功登录到 Azure AD 后，连接失败并显示错误：“指定的凭据无效”。

#### <a name="solution"></a>解决方案
解决方案同上。

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>登录时，Service Fabric Explorer 返回失败信息：“AADSTS50011”
#### <a name="problem"></a>问题
用户尝试登录到 Service Fabric Explorer 中的 Azure AD 时，页面返回故障：“AADSTS50011：回复地址 &lt;url&gt; 与针对应用程序 &lt;guid&gt; 配置的回复地址不匹配”。

![SFX 回复地址不匹配][sfx-reply-address-not-match]

#### <a name="reason"></a>原因
代表 Service Fabric Explorer 的群集 (web) 应用程序尝试针对 Azure AD 进行身份验证，在执行请求的过程中提供了重定向返回 URL。 但是，该 URL 并未列在 Azure AD 应用程序的“回复 URL”列表中。

#### <a name="solution"></a>解决方案
在 AAD 页面中选择“应用注册”，选择你的群集应用程序，然后选择“回复 URL”按钮。 在“回复 URL”页面上，将 Service Fabric Explorer 的 URL 添加到列表中或替换列表中的某一项。 完成后，保存所做的更改。

![Web 应用程序回复 URL][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>使用 Azure AD 身份验证通过 PowerShell 连接群集
若要连接 Service Fabric 群集，请使用以下 PowerShell 命令示例：

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

若要了解有关 Connect-servicefabriccluster cmdlet 的信息，请参阅 [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx)。

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>是否可将同一个 Azure AD 租户用于多个群集？
是的。 请记得将 Service Fabric Explorer 的 URL 添加到群集 (Web) 应用程序。 否则 Service Fabric Explorer 无法正常工作。

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>为何启用 Azure AD 时仍然需要服务器证书？
FabricClient 和 FabricGateway 执行相互身份验证。 使用 Azure AD 身份验证时，Azure AD 集成可将客户端标识提供给服务器，服务器证书用于验证服务器标识。 有关 Service Fabric 证书的详细信息，请参阅 [X.509 证书和 Service Fabric][x509-certificates-and-service-fabric]。

## <a name="next-steps"></a>后续步骤
此时，已创建一个使用 Azure Active Directory 进行管理身份验证的安全群集。 接下来，请[连接到该群集](service-fabric-connect-to-secure-cluster.md)，了解如何[管理应用程序机密](service-fabric-application-secret-management.md)。


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[select-tenant-button]: ./media/service-fabric-cluster-creation-via-arm/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-via-arm/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

