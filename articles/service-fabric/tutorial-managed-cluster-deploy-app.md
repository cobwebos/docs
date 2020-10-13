---
title: 通过 PowerShell 将应用程序部署到 Service Fabric 托管群集（预览版）
description: 在本教程中，你将连接到 Service Fabric 托管群集并通过 PowerShell 部署应用程序。
ms.topic: tutorial
ms.date: 09/28/2020
ms.openlocfilehash: 36a91d2852bcda5f958441b48ef4721d6ccc83c4
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91410188"
---
# <a name="tutorial-deploy-an-app-to-a-service-fabric-managed-cluster-preview"></a>教程：将应用部署到 Service Fabric 托管群集（预览版）

在本系列教程中，我们将讨论以下内容：

> [!div class="checklist"]
> * [如何部署 Service Fabric 托管群集](tutorial-managed-cluster-deploy.md)
> * [如何横向扩展 Service Fabric 托管群集](tutorial-managed-cluster-scale.md)
> * [如何在 Service Fabric 托管群集中添加和删除节点](tutorial-managed-cluster-add-remove-node-type.md)
> * 如何将应用程序部署到 Service Fabric 托管群集

本系列中的这一部分介绍如何执行以下操作：

> [!div class="checklist"]
> * 连接到 Service Fabric 托管群集
> * 将应用程序上传到群集
> * 在群集中实例化应用程序
> * 从群集中删除应用程序

## <a name="prerequisites"></a>先决条件

* Service Fabric 托管群集（请参阅[部署托管群集](tutorial-managed-cluster-deploy.md)）。

## <a name="connect-to-your-cluster"></a>连接到群集

若要连接到群集，需要有群集证书指纹。 你可以在资源部署的群集属性输出中找到此值，或者通过查询现有资源的群集属性也可以找到此值。

可以使用以下命令来查询群集资源，以获取群集证书指纹。

```powershell
$serverThumbprint = (Get-AzResource -ResourceId /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ServiceFabric/managedclusters/mysfcluster).Properties.clusterCertificateThumbprint
```

有了群集证书指纹，你就可以连接到群集了。

```powershell
$connectionEndpoint = "mysfcluster.eastus2.cloudapp.azure.com:19000"
Connect-ServiceFabricCluster -ConnectionEndpoint $connectionEndpoint -KeepAliveIntervalInSec 10 `
      -X509Credential `
      -ServerCertThumbprint $serverThumbprint  `
      -FindType FindByThumbprint `
      -FindValue $clientThumbprint `
      -StoreLocation CurrentUser `
      -StoreName My

```

### <a name="upload-an-application-package"></a>上传应用程序包

在本教程中，我们将使用 [Service Fabric 投票应用程序](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/voting-sample-no-reverse-proxy)示例。 若要详细了解如何通过 PowerShell 部署 Service Fabric 应用程序，请参阅[部署和删除 Service Fabric 应用程序](service-fabric-deploy-remove-applications.md)。

> [!NOTE]
> 在 Service Fabric 托管群集预览版中，无法直接从 Visual Studio 中发布应用程序。

需要先[将应用程序打包以便进行部署](service-fabric-package-apps.md)。 在本教程中，请遵循从 Visual Studio 中打包应用程序的步骤。 请务必记下应用程序的打包路径，因为它将用于以下路径。

创建应用程序包之后，可将应用程序包上传到群集。 更新 `$path` 值，以表示应用程序包所在的路径，然后运行以下代码：

```powershell
$path = "C:\Users\<user>\Documents\service-fabric-dotnet-quickstart\Voting\pkg\Debug"
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $path -CompressPackage
Register-ServiceFabricApplicationType -ApplicationPathInImageStore Debug
```

### <a name="create-an-application"></a>创建应用程序

可以使用 New-ServiceFabricApplication cmdlet 通过已成功注册的任何应用程序类型版本来实例化应用程序。 每个应用程序的名称必须以“fabric:”方案开头，并且必须对每个应用程序实例是唯一的。 还会创建目标应用程序类型的应用程序清单中定义的任何默认服务。

```powershell
New-ServiceFabricApplication fabric:/Voting VotingType 1.0.0
```

此操作完成之后，应该会看到你的应用程序实例在 Service Fabric Explorer 中运行。

### <a name="remove-an-application"></a>删除应用程序

如果不再需要某个应用程序实例，可使用 `Remove-ServiceFabricApplication` cmdlet 按名称将其永久删除，这样也会自动删除属于该应用程序的所有服务，从而永久删除所有服务状态。

```powershell
Remove-ServiceFabricApplication fabric:/Voting
```

## <a name="next-steps"></a>后续步骤

在这一步中，我们将应用部署到了 Service Fabric 托管群集。 若要详细了解 Service Fabric 托管群集，请参阅：

> [!div class="nextstepaction"]
> [Service Fabric 托管群集常见问题](faq-managed-cluster.md)
