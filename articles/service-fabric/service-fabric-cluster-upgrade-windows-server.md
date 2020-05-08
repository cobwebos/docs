---
title: 升级独立群集的版本
description: 升级运行独立 Service Fabric 群集的 Azure Service Fabric 代码。
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 5921fc9038e53f34e23f6fd97111c71b29699dc5
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793136"
---
# <a name="upgrade-the-service-fabric-version-that-runs-on-your-cluster"></a>升级群集上运行的 Service Fabric 版本 

对于任何新式系统而言，升级能力是实现产品长期成功的关键所在。 Azure Service Fabric 群集是你拥有的资源。 本文介绍如何升级独立群集上运行的 Service Fabric 版本。

> [!NOTE]
> 确保群集始终运行受支持的 Service Fabric 版本。 当 Microsoft 宣布推出新版 Service Fabric 时，即标志着自宣布日期起至少 60 天后，将结束对旧版的支持。 新版本将[在 Service Fabric 团队博客](https://blogs.msdn.microsoft.com/azureservicefabric/)中宣布。 从该时间开始，便可以选择使用新版本。
>
>

仅当使用的是生产形式的节点配置（每个 Service Fabric 节点在独立的物理机或虚拟机上分配）时，才可以将群集升级到最新版本。 如果使用的是开发群集（单个物理机或虚拟机上有多个 Service Fabric 节点），必须使用新版本重新创建该群集。

可以使用两个不同的工作流将群集升级至最新版本或受支持的 Service Fabric 版本。 其中一个工作流适用于已建立网络连接，可自动下载最新版本的群集。 另一个工作流适用于未建立网络连接，无法下载最新 Service Fabric 版本的群集。

## <a name="enable-auto-upgrade-of-the-service-fabric-version-of-your-cluster"></a>支持自动升级群集的 Service Fabric 版本
要将群集设置为在 Microsoft 发布新版本时下载 Service Fabric 的更新，可将 `fabricClusterAutoupgradeEnabled` 群集配置设置为 true**。 若要手动选择希望群集一直使用的某个受支持的 Service Fabric 版本，可将 `fabricClusterAutoupgradeEnabled` 群集配置设置为 false  。

## <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>升级已建立网络连接，可下载最新代码和配置的群集
如果群集节点可以通过 internet 连接到[Microsoft 下载中心](https://download.microsoft.com)，请使用以下步骤将群集升级到受支持的版本。

对于可以连接到 [Microsoft 下载中心](https://download.microsoft.com)的群集，Microsoft 会定期检查新的 Service Fabric 版本是否已发布。

推出新的 Service Fabric 版本时，相应的包将下载到群集本地，并为升级完成预配。 此外，为了通知客户已推出新版本，系统会明确显示类似于下面的群集运行状况警告：

“当前的群集版本 [版本号] 支持在 [日期] 结束。”

群集运行最新版本后，警告将会消失。

看到群集运行状况警告时，请更新群集：

1. 从对群集配置文件中列为节点的所有计算机拥有管理员访问权限的任何计算机连接到该群集。 运行此脚本的计算机不一定是群集的一部分。

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

2. 获取可以升级到的 Service Fabric 版本的列表。

    ```powershell
    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    应会看到类似于下面的输出：

    ![获取 Service Fabric 版本][getfabversions]
3. 使用 [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) Windows PowerShell 命令，开始将群集升级到可用的版本。

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
   若要监视升级进度，可以使用 Service Fabric Explorer，也可以运行以下 PowerShell 命令：

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    如果不符合现行的群集运行状况策略，则回滚升级。 若要为 Start-ServiceFabricClusterUpgrade 命令指定自定义运行状况策略，请参阅 [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) 的文档。

    解决造成回滚的问题后，请遵循前面所述的相同步骤再次启动升级。

## <a name="upgrade-clusters-that-have-no-connectivity-to-download-the-latest-code-and-configuration"></a>升级 *未建立网络连接*，无法下载最新代码和配置的群集
如果群集节点未与 [Microsoft 下载中心](https://download.microsoft.com)建立 Internet 连接，请使用以下步骤将群集升级到支持的版本。

> [!NOTE]
> 如果运行的群集未连接到 Internet，则必须关注 [Service Fabric 团队博客](https://blogs.msdn.microsoft.com/azureservicefabric/)来了解新版本的信息。 系统不会显示任何群集运行状况警告来提醒有新版本可用。  
>
>

### <a name="auto-provisioning-vs-manual-provisioning"></a>自动预配与手动预配
若要为最新代码版本启用自动下载和注册，请设置 Service Fabric 更新服务。 有关说明，请参阅[独立包](service-fabric-cluster-standalone-package-contents.md)中的 Tools\ServiceFabricUpdateService.zip\Readme_InstructionsAndHowTos.txt  。

对于手动步骤，请根据以下说明操作。

在启动配置升级之前，请修改群集配置，将以下属性设置为 *false*。

```json
"fabricClusterAutoupgradeEnabled": false,
```

有关用法详细信息，请参阅 [Start-ServiceFabricClusterConfigurationUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) PowerShell 命令。 在启动配置升级之前，请务必在 JSON 中更新“clusterConfigurationVersion”。

```powershell
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
```

### <a name="cluster-upgrade-workflow"></a>群集升级工作流

1. 从群集中的一个节点运行 [Get-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclusterupgrade) 并记下 *TargetCodeVersion*。

2. 从连接到 Internet 的计算机运行以下命令，以列出与当前版本兼容的所有升级版本，并从关联的下载链接下载相应的程序包：

    ```powershell
    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. 从对群集配置文件中列为节点的所有计算机拥有管理员访问权限的任何计算机连接到该群集。 运行此脚本的计算机不一定是群集的一部分。

    ```powershell
    ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"
    ```
4. 将下载的包复制到群集映像存储区中。

5. 注册复制的程序包。

    ```powershell
    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
    ```
6. 开始将群集升级到可用版本。

    ```powershell
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    ```
    可以在 Service Fabric Explorer 中或通过运行以下 PowerShell 命令来监视升级进度：

    ```powershell
    Get-ServiceFabricClusterUpgrade
    ```

    如果不符合现行的群集运行状况策略，则回滚升级。 若要为 Start-ServiceFabricClusterUpgrade 命令指定自定义运行状况策略，请参阅 [Start-ServiceFabricClusterUpgrade](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricclusterupgrade) 的文档。

    解决造成回滚的问题后，请遵循前面所述的相同步骤再次启动升级。

## <a name="next-steps"></a>后续步骤
* [升级独立群集的配置](service-fabric-cluster-config-upgrade-windows-server.md)
* 自定义部分 [Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)。
* [扩展和缩减群集](service-fabric-cluster-scale-in-out.md)。

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
