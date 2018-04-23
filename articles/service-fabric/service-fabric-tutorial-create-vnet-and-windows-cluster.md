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
ms.date: 01/22/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: b85c3030984758a0c425fb526a32fcd8eff974c9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2018
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>教程：将 Service Fabric Windows 群集部署到 Azure 虚拟网络
本教程是一个系列中的第一部分。 其中介绍了如何使用 PowerShell 和模板，将运行 Windows 的 Service Fabric 群集部署到 [Azure 虚拟网络 (VNET)](../virtual-network/virtual-networks-overview.md) 和[网络安全组](../virtual-network/virtual-networks-nsg.md)。 完成本教程后，云中会运行一个可在其中部署应用程序的群集。  若要使用 Azure CLI 创建 Linux 群集，请参阅[在 Azure 上创建安全的 Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)。

本教程介绍一个生产方案。  若要快速创建一个小型群集用于测试，请参阅[创建三节点测试群集](./scripts/service-fabric-powershell-create-test-cluster.md)。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 PowerShell 在 Azure 中创建 VNET
> * 创建密钥保管库并上传证书
> * 使用 PowerShell 在 Azure 中创建安全的 Service Fabric 群集
> * 使用 X.509 证书保护群集
> * 使用 PowerShell 连接到群集
> * 删除群集

在此系列教程中，你会学习如何：
> [!div class="checklist"]
> * 在 Azure 上创建安全群集
> * [缩小或扩大群集](service-fabric-tutorial-scale-cluster.md)
> * [升级群集的运行时](service-fabric-tutorial-upgrade-cluster.md)
> * [部署 API 管理与 Service Fabric](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 安装 [Service Fabric SDK 和 PowerShell 模块](service-fabric-get-started.md)
- 安装 [Azure PowerShell 模块 4.1 或更高版本](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

以下步骤将创建一个五节点 Service Fabric 群集。 若要计算在 Azure 中运行 Service Fabric 群集的成本，请使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)。

## <a name="key-concepts"></a>关键概念
[Service Fabric 群集](service-fabric-deploy-anywhere.md)是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。 群集可以扩展到成千上万台计算机。 属于群集一部分的计算机或 VM 称为节点。 需为每个节点分配节点名称（字符串）。 节点具有各种特征，如放置属性。

节点类型定义群集中一组虚拟机的大小、数量和属性。 每个已定义的节点类型均设置为[虚拟机规模集](/azure/virtual-machine-scale-sets/)，是一种 Azure 计算资源，可用于将一组虚拟机作为一个集进行部署和管理。 然后，每个节点类型可以独立扩展或缩减、打开不同的端口集，并可以有不同的容量指标。 节点类型用于定义一组群集节点（如“前端”或“后端”）的角色。  群集可以有多个节点类型，但主节点类型必须至少有 5 个 VM 供群集用于生产（或至少有 3 个 VM 用于测试群集）。  [Service Fabric 系统服务](service-fabric-technical-overview.md#system-services)位于主节点类型的节点上。

群集是使用群集证书保护的。 群集证书是一种 X.509 证书，用于保护节点到节点的通信，并对指向管理客户端的群集管理终结点进行验证。  群集证书还通过 HTTPS 为 HTTPS 管理 API 和 Service Fabric Explorer 提供 SSL。 自签名证书仅用于测试群集。  对于生产群集，请使用证书颁发机构 (CA) 提供的证书作为群集证书。

群集证书必须具备以下条件：

- 包含私钥。
- 专为密钥交换而创建，且证书可导出到个人信息交换 (.pfx) 文件。
- 证书的使用者名称必须与用于访问 Service Fabric 群集的域匹配。 只有满足此匹配，才能为群集的 HTTPS 管理终结点和 Service Fabric Explorer 提供 SSL。 无法从证书颁发机构 (CA) 处获取针对 cloudapp.azure.com 域的 SSL 证书。 必须获取群集的自定义域名。 从 CA 请求证书时，该证书的使用者名称必须与用于群集的自定义域名匹配。

Azure 密钥保管库用于管理 Azure 中 Service Fabric 群集的证书。  在 Azure 中部署群集时，负责创建 Service Fabric 群集的 Azure 资源提供程序将从密钥保管库提取证书，并将其安装在群集 VM 上。

本教程显示了一个在单个节点类型中包含五个节点的群集。 但是，对于任何生产群集部署，[容量规划](service-fabric-cluster-capacity.md)都是一个重要的步骤。 下面是在规划过程中必须注意的一些事项。

- 群集所需的节点数目和节点类型 
- 每个节点类型的属性（例如，大小、主节点、面向 Internet 以及 VM 数量等）
- 群集的可靠性和耐久性特征

## <a name="download-and-explore-the-template"></a>下载并浏览模板
下载以下资源管理器模板文件：
- [vnet-cluster.json][template]
- [vnet-cluster.parameters.json][parameters]

[vnet-cluster.json][template] 部署一些资源，包括以下资源。 

### <a name="service-fabric-cluster"></a>Service Fabric 群集
部署具有以下特征的 Windows 群集：
- 单节点类型 
- 主节点类型包含五个节点（可在模板参数中配置）
- OS：包含容器的 Windows Server 2016 Datacenter（可在模板参数中配置）
- 证书保护（可在模板参数中配置）
- 已启用[反向代理](service-fabric-reverseproxy.md)
- 已启用 [DNS 服务](service-fabric-dnsservice.md)
- 铜级[持久性级别](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)（可在模板参数中配置）
- 银级[可靠性级别](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)（可在模板参数中配置）
- 客户端连接终结点：19000（可在模板参数中配置）
- HTTP 网关终结点：19080（可在模板参数中配置）

### <a name="azure-load-balancer"></a>Azure 负载均衡器
已部署负载均衡器，并针对以下端口设置了探测和规则：
- 连接终结点:19000
- HTTP 网关终结点：19080 
- 应用程序端口：80
- 应用程序端口：443
- Service Fabric 反向代理：19081

如需其他任何应用程序端口，则需要调整 microsoft.network/loadBalancers 资源和 Microsoft.Network/networkSecurityGroups 资源，以允许传入流量。

### <a name="virtual-network-subnet-and-network-security-group"></a>虚拟网络、子网和网络安全组
虚拟网络、子网和网络安全组的名称已在模板参数中声明。  虚拟网络和子网的地址空间也已在模板参数中声明：
- 虚拟网络地址空间：172.16.0.0/20
- Service Fabric 子网地址空间：172.16.2.0/23

网络安全组中已启用以下入站流量规则。 可以通过更改模板变量来更改端口值。
- ClientConnectionEndpoint (TCP)：19000
- HttpGatewayEndpoint (HTTP/TCP)：19080
- SMB：445
- Internodecommunication - 1025、1026、1027
- 临时端口范围 – 49152 到 65534（至少需要 256 个端口）
- 应用程序使用的端口：80 和 443
- 应用程序端口范围 – 49152 到 65534（用于服务间的通信，但未在负载均衡器上打开）
- 阻止其他所有端口

如需其他任何应用程序端口，则需要调整 microsoft.network/loadBalancers 资源和 Microsoft.Network/networkSecurityGroups 资源，以允许传入流量。

## <a name="set-template-parameters"></a>设置模板参数
[Vnet-cluster.parameters.json][parameters] 参数文件声明用于部署群集和关联资源的多个值。 可能需要使用某些参数来修改部署：

|参数|示例值|说明|
|---|---||
|adminUserName|vmadmin| 群集 VM 的管理员用户名。 |
|adminPassword|Password#1234| 群集 VM 的管理员密码。|
|clusterName|mysfcluster123| 群集的名称。 |
|location|southcentralus| 群集的位置。 |
|certificateThumbprint|| <p>如果创建自签名证书或提供证书文件，则值应为空。</p><p>若要使用之前上传到 Key Vault 的现有证书，请填写证书指纹值。 例如“6190390162C988701DB5676EB81083EA608DCCF3”</p>。 | 
|certificateUrlValue|| <p>如果创建自签名证书或提供证书文件，则值应为空。 </p><p>若要使用之前上传到 Key Vault 的现有证书，请填写证书 URL。 例如“https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346”。</p>|
|sourceVaultValue||<p>如果创建自签名证书或提供证书文件，则值应为空。</p><p>若要使用之前上传到 Key Vault 的现有证书，请填写源保管库值。 例如“/subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT”。</p>|


<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>部署虚拟网络和群集
接下来，设置网络拓扑并部署 Service Fabric 群集。 [vnet-cluster.json][template] 资源管理器模板针对 Service Fabric 创建虚拟网络 (VNET)、子网和网络安全组 (NSG)。 该模板还会部署一个已启用证书安全性的群集。  对于生产群集，请使用证书颁发机构 (CA) 提供的证书作为群集证书。 可以使用自签名证书来保护测试群集。

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
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-cluster.json" `
-ParameterFile "$templatepath\vnet-cluster.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>使用新的自签名证书创建群集
以下脚本使用 [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) cmdlet 和模板在 Azure 中部署新群集。 该 cmdlet 还会在 Azure 中创建新的密钥保管库、向密钥保管库添加新的自签名证书，并将证书文件下载到本地。 

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
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\vnet-cluster.json" `
-ParameterFile "$templatepath\vnet-cluster.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateSubjectName $subname

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

**Service Fabric** PowerShell 模块提供许多 cmdlet 用于管理 Service Fabric 群集、应用程序和服务。  使用 [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet 连接到安全群集。 可在上一步骤的输出中找到证书指纹和连接终结点详细信息。

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
本教程系列中的其他文章将会使用本文中创建的群集。 如果不立即转到下一篇文章，可能需要删除该群集和密钥保管库，以避免产生费用。 若要删除群集及其占用的所有资源，最简单的方式是删除资源组。

使用 [Remove-AzureRMResourceGroup cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) 删除资源组和所有群集资源。  此外，还需删除包含密钥保管库的资源组。

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 使用 PowerShell 在 Azure 中创建 VNET
> * 创建密钥保管库并上传证书
> * 使用 PowerShell 在 Azure 中创建安全的 Service Fabric 群集
> * 使用 X.509 证书保护群集
> * 使用 PowerShell 连接到群集
> * 删除群集

接下来，请转到以下教程了解如何缩放群集。
> [!div class="nextstepaction"]
> [缩放群集](service-fabric-tutorial-scale-cluster.md)


[template]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.json
[parameters]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/cluster-tutorial/vnet-cluster.parameters.json
