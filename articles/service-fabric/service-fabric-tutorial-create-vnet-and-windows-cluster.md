---
title: 在 Azure 中创建 Windows Service Fabric 群集 | Microsoft Docs
description: 本教程介绍如何使用 PowerShell 将 Windows Service Fabric 群集部署到 Azure 虚拟网络和网络安全组。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/19/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 590e1e5853ccf4a525477f194c78f1fd8ce679ed
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453063"
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>教程：将 Service Fabric Windows 群集部署到 Azure 虚拟网络

本教程是一个系列中的第一部分。 其中介绍了如何使用 PowerShell 和模板，将运行 Windows 的 Service Fabric 群集部署到 [Azure 虚拟网络 (VNET)](../virtual-network/virtual-networks-overview.md) 和[网络安全组](../virtual-network/virtual-networks-nsg.md)。 完成本教程后，云中会运行一个可在其中部署应用程序的群集。  若要使用 Azure CLI 创建 Linux 群集，请参阅[在 Azure 上创建安全的 Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)。

本教程介绍一个生产方案。  若要快速创建一个较小群集用于测试，请参阅[创建测试群集](./scripts/service-fabric-powershell-create-secure-cluster-cert.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 PowerShell 在 Azure 中创建 VNET
> * 创建 Key Vault 并上传证书
> * 设置 Azure Active Directory 身份验证
> * 在 Azure PowerShell 中创建安全的 Service Fabric 群集
> * 使用 X.509 证书保护群集
> * 使用 PowerShell 连接到群集
> * 删除群集

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 在 Azure 上创建安全群集
> * [缩小或扩大群集](service-fabric-tutorial-scale-cluster.md)
> * [升级群集的运行时](service-fabric-tutorial-upgrade-cluster.md)
> * [删除群集](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前：

* 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* 安装 [Service Fabric SDK 和 PowerShell 模块](service-fabric-get-started.md)
* 安装 [Azure PowerShell 模块 4.1 或更高版本](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)
* 查看 [Azure 群集](service-fabric-azure-clusters-overview.md)的关键概念

以下步骤将创建一个七节点 Service Fabric 群集。 若要计算在 Azure 中运行 Service Fabric 群集的成本，请使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)。

## <a name="download-and-explore-the-template"></a>下载并浏览模板

下载以下资源管理器模板文件：

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

此模板将包含七个虚拟机和三个节点类型的安全群集部署到虚拟网络和网络安全组中。  其他示例模板可以在 [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates) 上找到。  [azuredeploy.json][template] 部署一些资源，包括以下资源。

### <a name="service-fabric-cluster"></a>Service Fabric 群集

在 **Microsoft.ServiceFabric/clusters** 资源中，配置了具有以下特征的 Windows 群集：

* 三个节点类型
* 主节点类型包含五个节点（可在模板参数中配置），另外两个节点类型各包含一个节点
* OS：包含容器的 Windows Server 2016 Datacenter（可在模板参数中配置）
* 证书保护（可在模板参数中配置）
* 已启用[反向代理](service-fabric-reverseproxy.md)
* 已启用 [DNS 服务](service-fabric-dnsservice.md)
* 铜级[持久性级别](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)（可在模板参数中配置）
* 银级[可靠性级别](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)（可在模板参数中配置）
* 客户端连接终结点：19000（可在模板参数中配置）
* HTTP 网关终结点：19080（可在模板参数中配置）

### <a name="azure-load-balancer"></a>Azure 负载均衡器

在 **Microsoft.Network/loadBalancers** 资源中，配置了负载均衡器，并为以下端口设置了探测和规则：

* 客户端连接终结点：19000
* HTTP 网关终结点：19080
* 应用程序端口：80
* 应用程序端口：443
* Service Fabric 反向代理：19081

如需其他任何应用程序端口，则需要调整 **Microsoft.Network/loadBalancers** 资源和 **Microsoft.Network/networkSecurityGroups** 资源，以允许传入流量。

### <a name="virtual-network-subnet-and-network-security-group"></a>虚拟网络、子网和网络安全组

虚拟网络、子网和网络安全组的名称已在模板参数中声明。  虚拟网络和子网的地址空间也在模板参数中声明，并在 **Microsoft.Network/virtualNetworks** 资源中配置：

* 虚拟网络地址空间：172.16.0.0/20
* Service Fabric 子网地址空间：172.16.2.0/23

在 **Microsoft.Network/networkSecurityGroups** 资源中启用以下入站流量规则。 可以通过更改模板变量来更改端口值。

* ClientConnectionEndpoint (TCP)：19000
* HttpGatewayEndpoint (HTTP/TCP)：19080
* SMB：445
* Internodecommunication - 1025、1026、1027
* 临时端口范围 – 49152 到 65534（至少需要 256 个端口）
* 应用程序使用的端口：80 和 443
* 应用程序端口范围 – 49152 到 65534（用于服务间的通信，但未在负载均衡器上打开）
* 阻止其他所有端口

如需其他任何应用程序端口，则需要调整 **Microsoft.Network/loadBalancers** 资源和 **Microsoft.Network/networkSecurityGroups** 资源，以允许传入流量。

### <a name="windows-defender"></a>Windows Defender
默认情况下，[Windows Defender 防病毒](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016)安装在 Windows Server 2016 上并在其上运行。 用户界面默认安装在某些 SKU 上，但不是必需的。  对于在模板中声明的每个节点类型/VM 规模集，将会使用 [Azure VM 防病毒扩展](/azure/virtual-machines/extensions/iaas-antimalware-windows)排除 Service Fabric 目录和进程：

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>设置模板参数

[azuredeploy.parameters.json][parameters] 参数文件声明用于部署群集和关联资源的多个值。 可能需要使用某些参数来修改部署：

|参数|示例值|说明|
|---|---||
|adminUserName|vmadmin| 群集 VM 的管理员用户名。[VM 的用户名要求](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm) |
|adminPassword|Password#1234| 群集 VM 的管理员密码。 [VM 的密码要求](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm)|
|clusterName|mysfcluster123| 群集的名称。 仅可包含字母和数字。 长度可介于 3 到 23 个字符之间。|
|位置|southcentralus| 群集的位置。 |
|certificateThumbprint|| <p>如果创建自签名证书或提供证书文件，则值应为空。</p><p>若要使用之前上传到密钥保管库的现有证书，请填写证书 SHA1 指纹值。 例如“6190390162C988701DB5676EB81083EA608DCCF3”</p>。 |
|certificateUrlValue|| <p>如果创建自签名证书或提供证书文件，则值应为空。 </p><p>若要使用之前上传到 Key Vault 的现有证书，请填写证书 URL。 例如“https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346”。</p>|
|sourceVaultValue||<p>如果创建自签名证书或提供证书文件，则值应为空。</p><p>若要使用之前上传到 Key Vault 的现有证书，请填写源保管库值。 例如“/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”。</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>设置 Azure Active Directory 客户端身份验证
如果将 Service Fabric 群集部署在某个公共网络中，而该网络托管在 Azure 上，则对于客户端到节点型相互身份验证，建议如下：
* 对客户端标识使用 Azure Active Directory
* 对服务器标识使用证书，并对 http 通信进行 SSL 加密

必须在[创建群集](#createvaultandcert)之前设置 Azure AD，以便针对 Service Fabric 群集对客户端进行身份验证。  通过 Azure AD，组织（称为租户）可管理用户对应用程序的访问。 

Service Fabric 群集提供其管理功能的各种入口点，包括基于 Web 的 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) 和 [Visual Studio](service-fabric-manage-application-in-visual-studio.md)。 因此，需要创建两个 Azure AD 应用程序来控制对群集的访问：一个 Web 应用程序和一个本机应用程序。  创建应用程序后，将用户分配到只读和管理员角色。

> [!NOTE]
> 在创建群集之前，请完成以下步骤。 因为脚本需要群集名称和终结点，这些值应是规划的值，而不是已创建的值。

本文假设已创建了一个租户。 如果未创建，请先阅读[如何获取 Azure Active Directory 租户](../active-directory/develop/quickstart-create-new-tenant.md)。

为了简化涉及到配置 Azure AD 与 Service Fabric 群集的一些步骤，我们创建了一组 Windows PowerShell 脚本。 [将脚本下载](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool)到计算机。

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>创建 Azure AD 应用程序并为用户分配角色
创建两个 Azure AD 应用程序来控制对群集的访问：一个 Web 应用程序和一个本机应用程序。 创建用于表示群集的应用程序后，请将用户分配到 [Service Fabric 支持的角色](service-fabric-cluster-security-roles.md)：只读和管理员。

运行 `SetupApplications.ps1` 并提供租户 ID、群集名称和 Web 应用程序回复 URL 作为参数。  另请指定用户的用户名和密码。  例如：

```PowerShell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> 对于国家/地区云（例如，Azure 政府、Azure 中国、Azure 德国），还应指定 `-Location` 参数。

可在 [Azure 门户](https://portal.azure.com)中找到 *TenantId* 或目录 ID。 选择“Azure Active Directory”->“属性”并复制“目录 ID”值。

将 *ClusterName* 用作脚本创建的 Azure AD 应用程序的前缀。 它不需要完全匹配实际的群集名称。 旨在更加轻松地将 Azure AD 项目映射到其配合使用的 Service Fabric 群集。

*WebApplicationReplyUrl* 是 Azure AD 在完成登录过程之后返回给用户的默认终结点。 将此终结点设置为群集的 Service Fabric Explorer 的终结点，默认值为：

https://&lt;cluster_domain&gt;:19080/Explorer

系统会提示登录到具有 Azure AD 租户管理权限的帐户。 完成此操作后，脚本会创建 Web 和本机应用程序来代表 Service Fabric 群集。 在 [Azure 门户](https://portal.azure.com)中查看租户的应用程序时，应会看到两个新条目：

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

创建群集时该脚本显示 Azure 资源管理器模板所需的 JSON，因此最好不要关闭 PowerShell 窗口。

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>添加 Azure AD 配置以使用 Azure AD 访问客户端
在 [azuredeploy.json][template] 的 **Microsoft.ServiceFabric/clusters** 节中配置 Azure AD。  为租户 ID、群集应用程序 ID 和客户端应用程序 ID 添加参数。  

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
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

在 [azuredeploy.parameters.json][parameters] 参数文件中添加参数值。  例如：

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>部署虚拟网络和群集

接下来，设置网络拓扑并部署 Service Fabric 群集。 [azuredeploy.json][template] 资源管理器模板针对 Service Fabric 创建虚拟网络 (VNET)、子网和网络安全组 (NSG)。 该模板还会部署一个已启用证书安全性的群集。  对于生产群集，请使用证书颁发机构 (CA) 提供的证书作为群集证书。 可以使用自签名证书来保护测试群集。

本文中的模板部署一个群集，该群集使用证书指纹来标识群集证书。  两个证书不能有相同的指纹，否则会增加证书管理的难度。 将已部署的群集从使用证书指纹切换为使用证书公用名称会使证书管理更加简单。  若要了解如何更新群集，以便使用证书公用名称进行证书管理，请阅读[将群集更改为使用证书公用名称进行管理](service-fabric-cluster-change-cert-thumbprint-to-cn.md)。

### <a name="create-a-cluster-using-an-existing-certificate"></a>使用现有证书创建群集

以下脚本使用 [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) cmdlet 和模板在 Azure 中部署新群集。 该 cmdlet 还会在 Azure 中创建新的 Key Vault，并上传证书。

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # must match the clusterName parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>使用新的自签名证书创建群集

以下脚本使用 [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) cmdlet 和模板在 Azure 中部署新群集。 该 cmdlet 还会在 Azure 中创建新的 Key Vault、向 Key Vault 添加新的自签名证书，并将证书文件下载到本地。

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>连接到安全群集

使用连同 Service Fabric SDK 一起安装的 Service Fabric PowerShell 模块连接到群集。  首先，将证书安装到计算机上当前用户的“个人(我的)”存储中。  运行以下 PowerShell 命令：

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

现在可以连接到安全群集了。

**Service Fabric** PowerShell 模块提供许多 cmdlet 用于管理 Service Fabric 群集、应用程序和服务。  使用 [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet 连接到安全群集。 可在上一步骤的输出中找到证书 SHA1 指纹和连接终结点详细信息。

如果以前设置过 AAD 客户端身份验证，请运行以下命令： 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

如果没有设置过 AAD 客户端身份验证，请运行以下命令：
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

使用 [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet 检查是否已建立连接并且群集正常。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>清理资源

本教程系列中的其他文章将使用刚才创建的群集。 如果不立即转到下一篇文章，可能需要[删除该群集](service-fabric-cluster-delete.md)，以避免产生费用。

## <a name="next-steps"></a>后续步骤

本教程介绍了以下操作：

> [!div class="checklist"]
> * 使用 PowerShell 在 Azure 中创建 VNET
> * 创建 Key Vault 并上传证书
> * 设置 Azure Active Directory 身份验证
> * 使用 PowerShell 在 Azure 中创建安全的 Service Fabric 群集
> * 使用 X.509 证书保护群集
> * 使用 PowerShell 连接到群集
> * 删除群集

接下来，请转到以下教程了解如何缩放群集。
> [!div class="nextstepaction"]
> [缩放群集](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
