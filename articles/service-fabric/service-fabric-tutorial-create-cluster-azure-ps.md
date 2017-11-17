---
title: "在 Azure 中创建 Service Fabric 群集 | Microsoft Docs"
description: "了解如何使用 PowerShell 在 Azure 中创建 Windows 或 Linux Service Fabric 群集"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/03/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 217b9f2f0dfed5b095e1bac1c8146abf4753fadc
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2017
---
# <a name="create-a-secure-cluster-in-azure-by-using-powershell"></a>使用 PowerShell 在 Azure 中创建安全群集
本文是教程系列中的第一篇文章，介绍如何使用 Azure Service Fabric 群集和容器将 .NET 应用程序移到云中。 以下步骤说明如何创建在 Azure 中运行的 Service Fabric 群集（Windows 或 Linux）。 完成本教程后，云中会运行一个可在其中部署应用程序的安全群集。

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 安装 [Service Fabric SDK](service-fabric-get-started.md)。
- 安装 [Azure PowerShell 模块 4.1 或更高版本](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。 （如果需要，请[安装 Azure PowerShell](/powershell/azure/overview) 或[更新到较新版本](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0#update-azps)）。


## <a name="create-a-service-fabric-cluster"></a>创建 Service Fabric 群集

此脚本创建单节点预览版 Service Fabric 群集。 该群集由自签名证书保护。 该脚本随群集一起创建证书，然后将证书置于 Key Vault 中。 无法将单节点群集扩展超出一个虚拟机，无法将预览版群集升级到较新版本。

若要计算在 Azure 中运行 Service Fabric 群集的成本，请使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)。
有关如何创建 Service Fabric 群集的详细信息，请参阅[使用 Azure 资源管理器创建 Service Fabric 群集](service-fabric-cluster-creation-via-arm.md)。

## <a name="log-in-to-azure"></a>登录 Azure
打开 PowerShell 控制台，登录到 Azure，选择要在其中部署群集的订阅：

   ```PowerShell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```

## <a name="cluster-parameters"></a>群集参数

   此脚本采用以下参数和概念。 根据你的需求自定义参数。

   | 参数       | 说明 | 建议的值 |
   | --------------- | ----------- | --------------- |
   | 位置 | 要在其中部署群集的 Azure 区域。 | 例如 *westeurope*、*eastasia* 或 *eastus* |
   | 名称     | 要创建的群集的名称。 名称必须为 4-23 个字符，并且只能包含小写字母、数字和连字符。 | 例如 *bobs-sfpreviewcluster* |
   | ResourceGroupName   | 要在其中创建群集的资源组的名称。 | 例如，*myresourcegroup* |
   | VmSku  | 要用于节点的虚拟机 SKU。 | 任何有效的虚拟机 SKU |
   | 操作系统  | 要用于节点的虚拟机 OS。 | 任何有效的虚拟机 OS |
   | KeyVaultName | 要与群集关联的新 Key Vault 的名称。 | 例如 *mykeyvault* |
   | ClusterSize | 群集中的虚拟机数量（可以是 *1* 或 *3-99* 之间的数字）。| 对于预览群集，仅指定一个虚拟机 |
   | CertificateSubjectName | 要创建的证书的使用者名称。 | 采用格式 *<name>*.*<location>*.cloudapp.azure.com |

### <a name="default-parameter-values"></a>默认参数值
**虚拟机**：可选设置。 如果没有指定它们，则管理员用户名默认为 *vmadmin*，并且 PowerShell 在创建群集之前会提示输入虚拟机密码。

**端口**：默认为端口 80 和 8081。 可以按照 [Service Fabric 群集中的端口](https://docs.microsoft.com/en-us/azure/service-fabric/create-load-balancer-rule)对应的指南指定其他端口。

**诊断**：默认已启用。

**DNS 服务**：默认未启用。

**反向代理**：默认未启用。

## <a name="create-the-cluster-with-your-parameters"></a>使用参数创建群集

确定符合需求的参数后，请运行以下命令来生成一个安全的 Service Fabric 群集及其证书。

可以修改此脚本来包含其他参数。 有关用于群集创建的参数的详细信息，请参阅 [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster.md) cmdlet。

>[!NOTE]
>运行此命令之前，必须创建在其中存储群集证书的文件夹。

```PowerShell

# Set the certificate variables. This creates and encrypts a password that Service Fabric will use.
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force

# You must create the folder where you want to store the certificate on your machine before you start this step.
$certfolder="c:\mycertificates\"

# Set the variables for common values. Change the values to fit your needs.
$clusterloc="WestUS"
$clustername = "mysfcluster"
$groupname="mysfclustergroup"       
$vmsku = "Standard_D2_v2"
$vaultname = "mykeyvault"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Set the number of cluster nodes. The possible values are 1 and 3-99.
$clustersize=1

# Create the Service Fabric cluster and its self-signed certificate. The OS you specify here lets you use this cluster with any applications that are also using containers.
New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
-ClusterSize $clustersize -CertificateSubjectName $subname `
-CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
-OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
```

创建过程可能需要几分钟。 配置完成后，该参数会输出在 Azure 中创建的群集的相关信息。 此外，它还会将群集证书复制到为此参数所指定路径上的 -CertificateOutputFolder 目录。

请记下群集的 **ManagementEndpoint** URL，它可能类似于以下 URL： https://mycluster.westeurope.cloudapp.azure.com:19080 。

## <a name="import-the-certificate"></a>导入证书

成功创建群集后，请运行以下命令以确保可以使用自签名证书：

```PowerShell

# To connect to the cluster, install the certificate into the Personal (My) store of the current user on your computer.
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
-FilePath C:\mycertificates\mysfclustergroup20170531104310.pfx `
-Password $certpwd
```

该命令在你计算机的当前用户上安装证书。 需要此证书才能访问 Service Fabric Explorer 并查看群集的运行状况。


## <a name="view-your-cluster-optional"></a>查看群集（可选）

获取群集和导入的证书后，可以连接到群集并查看其运行状况。 有多种连接方式，可通过 Service Fabric Explorer 或 PowerShell 进行连接。

### <a name="service-fabric-explorer"></a>Service Fabric Explorer
可以通过 Service Fabric Explorer 查看群集的运行状况。 为此，请浏览到群集的 **ManagementEndpoint** URL，然后选择已保存到计算机的证书。

>[!NOTE]
>打开 Service Fabric Explorer 时会看到证书错误，因为使用的是自签名证书。 在Microsoft Edge 中，必须单击“详细信息”，然后单击“转到网页”链接。 在 Chrome 中，必须单击“高级”，然后单击“继续”链接。

### <a name="powershell"></a>PowerShell

Service Fabric PowerShell 模块提供许多 cmdlet 用于管理 Service Fabric 群集、应用程序和服务。 使用 [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet 连接到安全群集。 在上一步骤的输出中可以找到证书指纹和连接终结点详细信息。

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
-KeepAliveIntervalInSec 10 `
-X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
-FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
-StoreLocation CurrentUser -StoreName My
```

也可以使用 [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) cmdlet 检查是否已建立连接并且群集正常运行。

```PowerShell
Get-ServiceFabricClusterHealth
```

## <a name="next-steps"></a>后续步骤
本教程已介绍如何使用 PowerShell 在 Azure 中创建安全的 Service Fabric 群集。

接下来，请转到以下教程了解如何部署现有应用程序。
> [!div class="nextstepaction"]
> [使用 Docker Compose 部署现有的 .NET 应用程序](service-fabric-host-app-in-a-container.md)

 
