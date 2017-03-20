---
title: "升级 Windows Server 上的独立 Azure Service Fabric 群集 | Microsoft 文档"
description: "升级运行独立 Service Fabric 群集的 Azure Service Fabric 代码和/或配置，其中包括设置群集更新模式。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: df15775f8e93c1dfad82c69ee4caa7838a39a545
ms.lasthandoff: 03/07/2017


---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>升级 Windows Server 上的独立 Azure Service Fabric 群集
> [!div class="op_single_selector"]
> * [Azure 群集](service-fabric-cluster-upgrade.md)
> * [独立群集](service-fabric-cluster-upgrade-windows-server.md)
>
>

对于任何现代系统，设计可升级性都是实现产品长期成功的关键。 Azure Service Fabric 群集是你拥有的资源。 本文介绍如何确保群集始终运行受支持的 Service Fabric 代码和配置版本。

## <a name="control-the-fabric-version-that-runs-on-your-cluster"></a>控制在群集上运行的 Fabric 版本
可以将群集设置为 Microsoft 一发布新版本就下载 Service Fabric 更新。 另一选项是为群集选择支持的 Fabric 版本。

若要控制 Fabric 版本，请将“fabricClusterAutoupgradeEnabled”群集配置设置为 true 或 false。

> [!NOTE]
> 确保群集始终运行支持的 Service Fabric 版本。 新版 Service Fabric 的公开发布标志着自该日期起至少 60 天以后才会结束对旧版本的支持。 新版本[在 Service Fabric 团队博客](https://blogs.msdn.microsoft.com/azureservicefabric/)上公布。
>
>

仅当使用的是生产形式的节点配置（每个 Service Fabric 节点在独立的物理机或虚拟机上分配）时，才可以将群集升级到最新版本。 如果使用的是开发群集（单个物理机或虚拟机上有多个 Service Fabric 节点），则必须先解除该群集，然后使用新版本重新创建该群集。

可以使用下面的两个工作流，将群集升级到最新版本或受支持的 Service Fabric 版本：


*   可以通过连接自动下载最新版本的群集
*   无法通过连接自动下载最新 Service Fabric 版本的群集

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>升级可以进行网络连接下载最新的代码和配置的群集
如果群集节点可以通过 Internet 连接到 [Microsoft 下载中心](http://download.microsoft.com)，则使用以下步骤将群集升级至支持的版本。

对于可以连接到 [Microsoft 下载中心](http://download.microsoft.com)的群集，我们会定期检查新的 Service Fabric 版本是否已发布。

推出新的 Service Fabric 版本时，相应的包将下载到群集本地，并为升级完成预配。 此外，为了通知客户已推出新版本，系统会明确发出类似于下面的群集运行状况警告：

“当前的群集版本 [版本号] 支持在[日期]结束。”

群集运行最新版本后，系统会删除警告。

#### <a name="cluster-upgrade-workflow"></a>群集升级工作流
看到群及运行状况警告后，需要执行以下操作：

1. 从对已被列为群集中节点的所有计算机具有管理员访问权限的任何计算机中连接到该群集。 运行以下脚本的计算机不一定是群集的一部分。

    ```powershell

    ###### Connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. 获取可以升级到的 Service Fabric 版本的列表。

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    会显示如下所示的输出：

    ![获取 Service Fabric 版本][getfabversions]

3. 请使用 [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx) Windows PowerShell 命令启动群集升级，升级到某个已发布的版本。

    ```powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Cluster upgrade example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   若要监视升级进度，可以使用 Service Fabric Explorer，也可以运行以下 Windows PowerShell 命令。

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

如果不符合现行的群集运行状况策略，则回滚升级。 此时可以指定自定义运行状况策略。有关 Start-ServiceFabricClusterUpgrade 命令的详细信息， 请参阅 [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx)。

修复造成回退的问题后，需重新启动升级，所执行的步骤与之前的步骤相同。

### <a name="upgrade-the-clusters-with-no-connectivity-to-download-the-latest-code-and-configuration"></a>升级无法通过网络连接下载最新代码和配置的群集
如果群集节点无法通过 Internet 连接到 [Microsoft 下载中心](http://download.microsoft.com)，请使用此部分的步骤将群集升级至支持的版本。

> [!NOTE]
> 如果正在运行的群集不能连接到 Internet，则必须关注 [Service Fabric 团队博客](https://blogs.msdn.microsoft.com/azureservicefabric/)，查看新版本的通知。 系统*不会*显示任何群集运行状况警告来提醒用户存在版本问题。  
>
>

1. 修改群集配置以将以下属性设置为 False。

        "fabricClusterAutoupgradeEnabled": false,

2.      启动配置升级。 有关详细信息，请参阅 [Start-ServiceFabricClusterConfigurationUpgrade](https://msdn.microsoft.com/en-us/library/mt788302.aspx)。 开始配置升级之前，请更新 JavaScript 对象表示法 (JSON) 文件中的 clusterConfigurationVersion 值。

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>群集升级工作流
1. 下载[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)文档中描述的最新版程序包。
2. 从对已被列为群集中节点的所有计算机具有管理员访问权限的任何计算机中连接到该群集。 运行此脚本的计算机不一定是群集的一部分。

    ```powershell

    ###### Connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```
3. 将下载的程序包复制到群集映像存储中。

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Code example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```

4. 注册复制的程序包。

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Code example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
5. 启动群集升级，升级到某个已发布的版本。

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Code example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   若要监视升级进度，可以使用 Service Fabric Explorer，也可以运行以下 Windows PowerShell 命令。

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    如果不符合现行的群集运行状况策略，则回滚升级。 此时可以指定自定义运行状况策略。有关 start-serviceFabricClusterUpgrade 命令的详细信息， 请参阅 [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx)。

修复造成回退的问题后，需重新启动升级，所执行的步骤与之前的步骤相同。


## <a name="upgrade-the-cluster-configuration"></a>升级群集配置
若要升级群集配置，请运行 Start-ServiceFabricClusterConfigurationUpgrade 命令。 配置升级由升级域处理。

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```


## <a name="next-steps"></a>后续步骤
* 了解如何自定义 [Service Fabric 群集结构设置](service-fabric-cluster-fabric-settings.md)的部分内容。
* 了解如何[扩展和缩减群集](service-fabric-cluster-scale-up-down.md)。
* 了解[应用程序升级](service-fabric-application-upgrade.md)。

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

