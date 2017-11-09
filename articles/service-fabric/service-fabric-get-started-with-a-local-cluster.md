---
title: "以本地方式部署和升级 Azure 微服务 | Microsoft 文档"
description: "了解如何设置本地 Service Fabric 群集，在其中部署现有的应用程序，并升级该应用程序。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 60a1f6a5-5478-46c0-80a8-18fe62da17a8
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/03/2017
ms.author: ryanwi;mikhegn
ms.openlocfilehash: 9d547039491f6b0c0d308c64d11e9c54b4b4e3d5
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2017
---
# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>在本地群集上部署和升级应用程序入门
Azure Service Fabric SDK 包含完整的本地开发环境，可让你快速地在本地群集上开始部署和管理应用程序。 在本文中，会从 Windows PowerShell 创建本地群集、将现有应用程序部署到该群集，然后将该应用程序升级为新版本。

> [!NOTE]
> 本文假设已[设置开发环境](service-fabric-get-started.md)。
> 
> 

## <a name="create-a-local-cluster"></a>创建本地群集
Service Fabric 群集代表一组可在其中部署应用程序的硬件资源。 通常，群集由任意数量的计算机（从 5 台到数千台）组成。 不过，Service Fabric SDK 包含可在一台计算机上运行的群集配置。

请务必知道 Service Fabric 本地群集不是模拟器或仿真器。 它运行多台计算机群集上使用的相同平台代码。 唯一的差别在于它在一台计算机上运行通常会分散于五台计算机的平台进程。

SDK 提供两种方式来设置本地群集：Windows PowerShell 脚本和本地群集管理器系统托盘应用。 在本教程中，我们使用 PowerShell 脚本。

> [!NOTE]
> 如果已通过从 Visual Studio 部署应用程序创建了本地群集，则可以跳过本部分。
> 
> 

1. 以管理员身份启动新的 PowerShell 窗口。
2. 从 SDK 文件夹运行群集设置脚本：
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```
   
    群集设置需要一段时间。 完成设置后，会看到类似于下面的输出：
   
    ![群集设置输出][cluster-setup-success]
   
    现已准备好将应用程序部署到群集。

## <a name="deploy-an-application"></a>部署应用程序
Service Fabric SDK 包含一组丰富的框架以及用于创建应用程序的开发人员工具。 如果有兴趣学习如何在 Visual Studio 中创建应用程序，请参阅[在 Visual Studio 中创建第一个 Service Fabric 应用程序](service-fabric-create-your-first-application-in-visual-studio.md)。

本教程会使用现有示例应用程序（其名称为 WordCount），以便可专注于平台的管理方面：部署、监视和升级。

1. 以管理员身份启动新的 PowerShell 窗口。
2. 导入 Service Fabric SDK PowerShell 模块。
   
    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```
3. 创建一个目录，用于存储要下载和部署的应用程序，例如 C:\ServiceFabric。
   
    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```
4. [下载 WordCount 应用程序](http://aka.ms/servicefabric-wordcountapp) 到创建的位置。  注意：Microsoft Edge 浏览器以 *.zip* 扩展名保存文件。  将文件扩展名更改为 *.sfpkg*。
5. 连接到本地群集：
   
    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```
6. 使用 SDK 的部署命令来创建新的应用程序，并提供应用程序包的名称和路径。
   
    ```powershell  
   Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```
   
    如果一切正常，应会看到下面的输出：
   
    ![将应用程序部署到本地群集][deploy-app-to-local-cluster]
7. 若要查看应用程序的操作情况，请启动浏览器并导航到 [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html)。 应会看到：
   
    ![已部署的应用程序 UI][deployed-app-ui]
   
    WordCount 应用程序非常简单。 它包含的客户端 JavaScript 代码可生成五个随机字符的“单词”，并将这些单词通过 ASP.NET Web API 中继到应用程序。 有状态服务将跟踪计算的字数。 这些单词根据其第一个字符进行分区。 可以在[经典入门示例](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/WordCount)中查找 WordCount 应用程序的源代码。
   
    我们部署的应用程序包含四个分区。 因此，以 A 到 G 开头的单词将存储在第一个分区，以 N 到 H 开头的单词将存储在第二个分区，依此类推。

## <a name="view-application-details-and-status"></a>查看应用程序详细信息和状态
我们现已部署应用程序，让我们在 PowerShell 中查看一些应用程序详细信息。

1. 查询群集上所有已部署的应用程序：
   
    ```powershell
    Get-ServiceFabricApplication
    ```
   
    假设你只部署了 WordCount 应用，将看到类似于下面的内容：
   
    ![在 PowerShell 中查询所有已部署的应用程序][ps-getsfapp]
2. 通过查询 WordCount 应用程序中包含的服务集转到下一个级别。
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![在 PowerShell 中列出应用程序的服务][ps-getsfsvc]
   
    该应用程序由两个服务组成：Web 前端服务和可管理单词的有状态服务。
3. 最后，查看 WordCountService 的分区列表：
   
    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```
   
    ![在 PowerShell 中查看服务分区][ps-getsfpartitions]
   
    使用的命令集（例如所有 Service Fabric PowerShell 命令）可用于任何可以连接的群集（本地或远程）。
   
    若要以更直观的方式来与群集交互，可以在浏览器中导航到 [http://localhost:19080/Explorer](http://localhost:19080/Explorer) ，以使用基于 Web 的 Service Fabric Explorer 工具。
   
    ![在 Service Fabric 资源管理器中查看应用程序详细信息][sfx-service-overview]
   
   > [!NOTE]
   > 要了解有关 Service Fabric Explorer 的详细信息，请参阅 [使用 Service Fabric Explorer 可视化群集](service-fabric-visualizing-your-cluster.md)。
   > 
   > 

## <a name="upgrade-an-application"></a>升级应用程序
Service Fabric 在应用程序推出于群集时监视其运行状况，从而提供无需停机的升级。 执行 WordCount 应用程序升级。

新版应用程序现在只计算以元音开头的单词。 推出升级后，我们会看到应用程序的行为出现两项变化。 首先，计数增长的速率应该变慢，因为计算的单词比较少。 其次，由于第一个分区有两个元音（A 和 E），而其他每个分区只包含一个元音，因此第一个分区的计数最终会超出其他分区。

1. [下载 WordCount v2 包](http://aka.ms/servicefabric-wordcountappv2)并将其保存到已下载的 v1 包的同一位置。
2. 返回到 PowerShell 窗口并使用 SDK 的升级命令在群集中注册新版本。 然后开始升级 fabric:/WordCount 应用程序。
   
    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```
   
    开始升级时，应会在 PowerShell 中看到如下所示的输出。
   
    ![在 PowerShell 中查看升级进度][ps-appupgradeprogress]
3. 当升级正在进行时，可能发现从 Service Fabric 资源管理器监视其状态会更加轻松。 启动浏览器窗口并导航到 [http://localhost:19080/Explorer](http://localhost:19080/Explorer)。 展开左侧树中的“应用程序”，并选择“WordCount”，最后选择“fabric:/WordCount”。 在“基本信息”选项卡中，随着群集升级域的不断升级，可以看到升级状态。
   
    ![在 Service Fabric 资源管理器中查看升级进度][sfx-upgradeprogress]
   
    随着每个域不断升级，系统将执行运行状况检查，以确保应用程序行为正常。
4. 如果对 fabric:/WordCount 应用程序中的服务集重新运行以前的查询，则会发现虽然 WordCountService 的版本已更改，但 WordCountWebService 的版本保持不变：
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![升级后查询应用程序服务][ps-getsfsvc-postupgrade]
   
    此示例重点介绍了 Service Fabric 如何管理应用程序升级。 它只处理了已更改的服务集（或这些服务中的代码/配置包），使升级过程变得更快速且更可靠。
5. 最后，请返回到浏览器来观察新应用程序版本的行为。 与预期一样，计数进度比较缓慢，第一个分区中的卷数最终稍多一些。
   
    ![在浏览器中查看应用程序的新版本][deployed-app-ui-v2]

## <a name="cleaning-up"></a>清理
结束之前，请务必记住该本地群集是真实的。 应用程序继续在后台运行，直到将其删除。  根据应用的性质，正在运行的应用可能会占用计算机上的大量资源。 可通过几个选项对应用程序和群集进行管理：

1. 若要删除单个应用程序及其所有数据，请运行下面的命令：
   
    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```
   
    也可从 Service Fabric Explorer 的“操作”菜单或左侧应用程序列表视图的上下文菜单中删除应用程序。
   
    ![在 Service Fabric Explorer 中删除应用程序][sfe-delete-application]
2. 从群集中删除应用程序后，可以注销 WordCount 应用程序类型版本 1.0.0 和 2.0.0。 删除操作将从群集的映像存储中删除该应用程序包，包括其代码和配置。
   
    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```
   
    或者，在 Service Fabric Explorer 中选择该应用程序对应的“取消预配类型”  。
3. 若要关闭群集，但保留应用程序数据和跟踪，请在系统托盘应用中单击“停止本地群集”  。
4. 要完全删除群集，请在系统托盘应用中单击“删除本地群集”  。 此选项会导致下次在 Visual Studio 中按 F5 时部署较慢。 仅当在一段时间内不想使用本地群集时，或者当需要回收资源时，才删除本地群集。

## <a name="one-node-and-five-node-cluster-mode"></a>单节点或 5 节点群集模式
开发应用程序时，通常会发现自己快速迭代编写代码、调试、更改代码、调试等。 为有助于优化此过程，本地群集可采用两种模式运行：单节点或 5 节点模式。 这两种群集模式各有优点。 5 节点群集模式使你能够使用真正的群集。 可以使用多个实例和服务副本测试故障转移方案。 单节点群集模式经过优化，能够快速执行服务部署和注册，从而帮助使用 Service Fabric 运行时快速验证代码。

单节点群集模式和 5 节点群集模式都不是仿真器或模拟器。 本地开发群集运行多台计算机群集中使用的同一平台代码。

> [!WARNING]
> 更改群集模式时，会从系统中删除当前群集，并创建一个新的群集。 更改群集模式后会删除相应群集中存储的数据。
> 
> 

若要更改为单节点群集模式，请在 Service Fabric 本地群集管理器中选择“切换群集模式”。

![切换群集模式][switch-cluster-mode]

或者，使用 PowerShell 更改群集模式：

1. 以管理员身份启动新的 PowerShell 窗口。
2. 从 SDK 文件夹运行群集设置脚本：
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```
   
    群集设置需要一段时间。 完成设置后，会看到类似于下面的输出：
   
    ![群集设置输出][cluster-setup-success-1-node]

## <a name="next-steps"></a>后续步骤
* 现在，已部署并升级某些预先生成的应用程序，接下来可以 [尝试在 Visual Studio 中生成自己的应用程序](service-fabric-create-your-first-application-in-visual-studio.md)。
* 也可以对 [Azure 群集](service-fabric-cluster-creation-via-portal.md) 执行本文中所述的对本地群集执行的所有操作。
* 本文中执行的升级非常简单。 若要深入了解 Service Fabric 升级的功能和灵活性，请参阅 [升级文档](service-fabric-application-upgrade.md) 。

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
