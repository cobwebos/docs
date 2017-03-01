---
title: "升级 Windows Server 上的独立 Service Fabric 群集 | Microsoft Docs"
description: "升级运行独立 Service Fabric 群集的 Service Fabric 代码和/或配置，其中包括设置群集更新模式"
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
ms.sourcegitcommit: 3d7e28c1cd221d704cf9cfec66da535e079fb472
ms.openlocfilehash: 30044abc0d7d42b11ddd210dfb9ea3eadb94dda6
ms.lasthandoff: 02/27/2017


---
# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>升级 Windows Server 上的独立 Service Fabric 群集
> [!div class="op_single_selector"]
> * [Azure 群集](service-fabric-cluster-upgrade.md)
> * [独立群集](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

对于任何现代系统，设计可升级性都是实现产品长期成功的关键。 Service Fabric 群集是你拥有的资源。 本文介绍了如何可以确保群集始终运行支持的 Service Fabric 代码和配置版本。

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>控制在群集上运行的结构版本
当 Microsoft 发布新版本时可以将群集设置为下载 Service Fabric 更新，或选择挑选想要群集安装的支持的结构版本。 

可以通过将“fabricClusterAutoupgradeEnabled”群集配置设置为 True 或 False 实现此目的。

> [!NOTE]
> 确保始终让群集运行支持的 Service Fabric 版本。 当我们公布新版本的 Service Fabric 发布后，则标志着自该日期起至少 60 天以后结束对旧版本的支持。 新版本[在 Service Fabric 团队博客](https://blogs.msdn.microsoft.com/azureservicefabric/)上公布。 之后新版本则可供选择。 
> 
> 

仅当使用的是生产形式的节点配置（每个 Service Fabric 节点在独立的物理机或虚拟机上分配）时，才可以将群集升级到最新版本。 如果使用的是开发群集（单个物理机或虚拟机上有多个 Service Fabric 节点），则必须先解除该群集，然后使用新版本重新创建该群集。

有两种不同的工作流可用于将群集升级至最新的或支持的 Service Fabric 版本。 一种适用于可以进行网络连接自动下载最新版本的群集，第二种适用于不能进行网络连接下载最新的 Service Fabric 版本的群集。

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>升级可以进行网络连接下载最新的代码和配置的群集
如果群集节点可以通过 Internet 连接到 [http://download.microsoft.com](http://download.microsoft.com)，则使用以下步骤将群集升级至支持的版本 

对于可以连接到 [http://download.microsoft.com](http://download.microsoft.com) 的群集，我们会定期检查新的 Service Fabric 版本的可用性。

当新的 Service Fabric 版本可用时，会将程序包本地下载到群集并对其进行设置以进行升级。 此外，系统还会显示类似以下的显式群集运行状况警告来告知客户新版本的发布：

“当前群集版本 [版本#] 支持结束 [日期]。”群集运行最新版本后，警告会立即消失。

#### <a name="cluster-upgrade-workflow"></a>群集升级工作流。
看到群及运行状况警告后，需要执行以下操作：

1. 从对已被列为群集中节点的所有计算机具有管理员访问权限的任何计算机中连接到该群集。 运行此脚本的计算机不一定是群集的一部分
   
    ```powershell
   
    ###### connect to the secure cluster using certs
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
2. 获取你可以升级到的 Service Fabric 版本列表
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```
   
    你应会看到类似于此的输出：
   
    ![获取结构版本][getfabversions]
3. 使用 [Start-ServiceFabricClusterUpgrade PowerShell cmd](https://msdn.microsoft.com/library/mt125872.aspx) 启动到可用版本之一的群集升级
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   可以在 Service Fabric Explorer 中或通过运行以下 Power Shell 命令来监视升级进度
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    如果不符合现行的群集运行状况策略，则回滚升级。 此时，可以指定自定义运行状况策略。有关 Start-ServiceFabricClusterUpgrade 命令的详细信息，请参阅[此文档](https://msdn.microsoft.com/library/mt125872.aspx)。 

修复造成回滚的问题后，需要按照与之前完全相同的步骤重新启动升级。

### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>升级<U>不能</u>进行网络连接下载最新代码和配置的群集
如果群集节点**不能**通过 Internet 连接到 [http://download.microsoft.com](http://download.microsoft.com)，则使用以下步骤将群集升级至支持的版本 

> [!NOTE]
> 如果正在运行的群集不能连接到 Internet，则必须监测 Service Fabric 团队博客来获知新版本的发布。 系统**不会**显示任何群集运行状况警告来提醒你新版本的发布。  
> 
> 

1. 修改群集配置以将以下属性设置为 False。
   
        "fabricClusterAutoupgradeEnabled": false,

然后启动配置升级。 请参阅 [Start-ServiceFabricClusterConfigurationUpgrade PS cmd ](https://msdn.microsoft.com/en-us/library/mt788302.aspx) 了解使用的详细信息。 在启动配置升级前请确保在 JSON 中更新“clusterConfigurationVersion”。

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File> 

```

#### <a name="cluster-upgrade-workflow"></a>群集升级工作流。
1. 在[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)文档中下载最新版本的程序包
2. 从对已被列为群集中节点的所有计算机具有管理员访问权限的任何计算机中连接到该群集。 运行此脚本的计算机不一定是群集的一部分 
   
    ```powershell
   
    ###### connect to the cluster
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
   
   ###### Get the list of available service fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"
   
   ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```

4. 注册复制的程序包 
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 
   
    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
   
     ```
5. 启动到可用版本之一的群集升级。 
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   可以在 Service Fabric Explorer 中或通过运行以下 Power Shell 命令来监视升级进度
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    如果不符合现行的群集运行状况策略，则回滚升级。 此时，可以指定自定义运行状况策略。有关 start-serviceFabricClusterUpgrade 命令的详细信息，请参阅[此文档](https://msdn.microsoft.com/library/mt125872.aspx)。 

修复造成回滚的问题后，需要按照与之前完全相同的步骤重新启动升级。


## <a name="cluster-configuration-upgrade"></a>群集配置升级
若要执行群集配置升级，请运行 Start-ServiceFabricClusterConfigurationUpgrade。 配置升级由升级域处理。

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File> 

```


## <a name="next-steps"></a>后续步骤
* 了解如何自定义 [Service Fabric 群集结构设置](service-fabric-cluster-fabric-settings.md)的部分内容
* 了解如何[扩展和缩减群集](service-fabric-cluster-scale-up-down.md)
* 了解[应用程序升级](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

