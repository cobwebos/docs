---
title: "在 Azure 中创建 Service Fabric 群集 | Microsoft Docs"
description: "了解如何使用 PowerShell 在 Azure 中创建 Windows 或 Linux Service Fabric 群集。"
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
ms.date: 07/13/2017
ms.author: ryanwi
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 80c5a2a43302e1cc8ec3b4298eb393a1861252d3
ms.contentlocale: zh-cn
ms.lasthandoff: 08/16/2017

---

# <a name="create-a-secure-cluster-on-azure-using-powershell"></a>使用 PowerShell 在 Azure 中创建安全群集
本教程介绍如何创建一个在 Azure 中运行的 Service Fabric 群集（Windows 或 Linux）。 完成本教程后，云中会运行一个可在其中部署应用程序的群集。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 PowerShell 在 Azure 中创建安全的 Service Fabric 群集
> * 使用 X.509 证书保护群集
> * 使用 PowerShell 连接到群集
> * 删除群集

## <a name="prerequisites"></a>先决条件
在开始学习本教程之前：
- 如果没有 Azure 订阅，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- 安装 [Service Fabric SDK 和 PowerShell 模块](service-fabric-get-started.md)
- 安装 [Azure PowerShell 模块 4.1 或更高版本](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

以下过程创建单节点（单虚拟机）预览版 Service Fabric 群集。 该群集由自签名证书保护，该证书随群集一起创建，然后置于密钥保管库中。 单节点群集无法扩展超出一个虚拟机，并且预览版群集无法升级到较新版本。

若要计算在 Azure 中运行 Service Fabric 群集的成本，请使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/)。
有关创建 Service Fabric 群集的详细信息，请参阅[使用 Azure Resource Manager 创建 Service Fabric 群集](service-fabric-cluster-creation-via-arm.md)。

## <a name="create-the-cluster-using-azure-powershell"></a>使用 Azure PowerShell 创建群集
1. 从[用于 Service Fabric 的 Azure Resource Manager 模板](https://aka.ms/securepreviewonelineclustertemplate) GitHub 存储库下载 Azure Resource Manager 模板和参数文件的本地副本：  *azuredeploy.json* 是定义 Service Fabric 群集的 Azure Resource Manager 模板。 *azuredeploy.parameters.json* 是用于自定义群集部署的参数文件。

2. 在 *azuredeploy.parameters.json* 参数文件中自定义以下参数：

   | 参数       | 说明 | 建议的值 |
   | --------------- | ----------- | --------------- |
   | clusterLocation | 要在其中部署群集的 Azure 区域。 | 例如 westeurope、eastasia、eastus |
   | clusterName     | 要创建的群集的名称。 | 例如 bobs-sfpreviewcluster |
   | adminUserName   | 群集虚拟机上的本地管理员帐户。 | 任何有效的 Windows Server 用户名 |
   | adminPassword   | 群集虚拟机上的本地管理员帐户的密码。 | 任何有效的 Windows Server 密码 |
   | clusterCodeVersion | 要运行的 Service Fabric 版本。 （255.255.X.255 是预览版）。 | **255.255.5718.255** |
   | vmInstanceCount | 群集中的虚拟机数量（可以是 1 或 3-99 之间的数字）。 | **1** | 对于预览群集，仅指定一个虚拟机 |

3. 打开 PowerShell 控制台，登录到 Azure，选择要在其中部署群集的订阅：

   ```powershell
   Login-AzureRmAccount
   Select-AzureRmSubscription -SubscriptionId <subscription-id>
   ```
4. 为 Service Fabric 使用的证书创建并加密密码。

   ```powershell
   $pwd = "<your password>" | ConvertTo-SecureString -AsPlainText -Force
   ```
5. 运行以下命令创建群集及其证书：

   ```powershell
      New-AzureRmServiceFabricCluster `
          -TemplateFile C:\Users\me\Desktop\azuredeploy.json `
          -ParameterFile C:\Users\me\Desktop\azuredeploy.parameters.json `
          -CertificateOutputFolder C:\Users\me\Desktop\ `
          -CertificatePassword $pwd `
          -CertificateSubjectName "mycluster.westeurope.cloudapp.azure.com" `
          -ResourceGroupName myclusterRG
   ```

   >[!NOTE]
   >`-CertificateSubjectName` 参数应与参数文件中指定的 clusterName 参数以及连接到所选择的 Azure 区域的域保持一致，例如 `clustername.eastus.cloudapp.azure.com`。

配置完成后，该参数会输出在 Azure 中创建的群集的相关信息。 此外，它还会将群集证书复制到为此参数所指定路径上的 -CertificateOutputFolder 目录。 需要此证书才能访问 Service Fabric Explorer 并查看群集的运行状况。

请记下群集的 URL，它可能类似于以下 URL：https://mycluster.westeurope.cloudapp.azure.com:19080

## <a name="modify-the-certificate--access-service-fabric-explorer"></a>修改证书并访问 Service Fabric Explorer ##

1. 双击证书打开证书导入向导。

2. 使用默认设置，但务必勾选“将此密钥标记为可导出” 复选框（在“私钥保护”步骤中）。 本教程中稍后将 Azure 容器注册表配置为 Service Fabric 群集身份验证时，Visual Studio 需要导出该证书。

3. 现在可以在浏览器中打开 Service Fabric Explorer。 为此，请使用 Web 浏览器导航到群集的 ManagementEndpoint URL，然后选择已保存到计算机的证书。

>[!NOTE]
>打开 Service Fabric Explorer 时会看到证书错误，因为使用的是自签名证书。 在Microsoft Edge 中，必须单击“详细信息”，然后单击“转到网页”链接。 在 Chrome 中，必须单击“高级”，然后单击“继续”链接。

>[!NOTE]
>如果群集创建失败，始终可以重新运行命令，这样可以更新已部署的资源。 如果在失败部署的过程中创建了证书，则将创建新的证书。 若要排查群集创建问题，请参阅[使用 Azure Resource Manager 创建 Service Fabric 群集](service-fabric-cluster-creation-via-arm.md)。

## <a name="connect-to-the-secure-cluster"></a>连接到安全群集
使用连同 Service Fabric SDK 一起安装的 Service Fabric PowerShell 模块连接到群集。  首先，将证书安装到计算机上当前用户的“个人(我的)”存储中。  运行以下 PowerShell 命令：

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

现在可以连接到安全群集了。

**Service Fabric** PowerShell 模块提供许多 cmdlet 用于管理 Service Fabric 群集、应用程序和服务。  使用 [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) cmdlet 连接到安全群集。 在上一步骤的输出中可以找到证书指纹和连接终结点详细信息。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
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

群集由群集资源本身以及其他 Azure 资源组成。 若要删除群集及其占用的所有资源，最简单的方式是删除资源组。

登录到 Azure，选择要删除的群集的订阅 ID。  可通过登录到 [Azure 门户](http://portal.azure.com)查找订阅 ID。 使用 [Remove-AzureRMResourceGroup cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) 删除资源组和所有群集资源。

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="next-steps"></a>后续步骤
本教程介绍了如何：

> [!div class="checklist"]
> * 在 Azure 中创建 Service Fabric 群集
> * 使用 X.509 证书保护群集
> * 使用 PowerShell 连接到群集
> * 删除群集

接下来，转到以下教程了解如何部署现有应用程序。
> [!div class="nextstepaction"]
> [使用 Docker Compose 部署现有的 .NET 应用程序](service-fabric-host-app-in-a-container.md)

