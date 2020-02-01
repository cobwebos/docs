---
title: 创建独立 Azure Service Fabric 群集
description: 在运行 Windows Server 的任何计算机（物理或虚拟）上创建 Azure Service Fabric 群集，无论是在本地还是在任何云中。
author: dkkapur
ms.topic: conceptual
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: 461d6021a201ca1fa5722bb44c427baca2a7728e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903373"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>创建在 Windows Server 上运行的独立群集
可以使用 Azure Service Fabric 在运行 Windows Server 的任何虚拟机或计算机上创建 Service Fabric 的群集。 这意味着，你可以在包含一组互连 Windows Server 计算机的任何环境中部署和运行 Service Fabric 应用程序，不管是在本地还是与任何云提供商联系。 Service Fabric 提供了一个安装程序包，用于创建称为独立 Windows Server 包的 Service Fabric 群集。 Azure 上的传统 Service Fabric 群集作为托管服务提供，而独立的 Service Fabric 群集是自助服务。 有关差异的详细信息，请参阅[比较 Azure 与独立 Service Fabric 群集](./service-fabric-deploy-anywhere.md)。

本文将指导你完成创建 Service Fabric 独立群集的步骤。

> [!NOTE]
> 此独立 Windows Server 包免费提供，可用于生产部署。 此包可能包含 "预览" 中新的 Service Fabric 功能。 向下滚动到 "[预览此包中包含的功能](#previewfeatures_anchor)"。 预览功能列表部分。 你现在可以[下载 EULA 的副本](https://go.microsoft.com/fwlink/?LinkID=733084)。
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>获取对 Windows Server 包的 Service Fabric 的支持
* 向社区询问有关[Azure Service Fabric 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?)中的 Windows Server Service Fabric 独立包的信息。
* 为[Service Fabric 提供专业支持](https://support.microsoft.com/oas/default.aspx?prid=16146)的票证。  在[此处](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)详细了解 Microsoft 的专业支持。
* 你还可以作为[Microsoft 顶级支持](https://support.microsoft.com/en-us/premier)的一部分获取此包的支持。
* 有关更多详细信息，请参阅[Azure Service Fabric 支持选项](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)。
* 若要出于支持目的收集日志，请运行[Service Fabric 独立日志收集器](service-fabric-cluster-standalone-package-contents.md)。

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>下载 Windows Server 包的 Service Fabric
若要创建群集，请使用在此处找到的适用于 Windows Server 包的 Service Fabric （Windows Server 2012 R2 和更高版本）： <br>
[下载链接-Service Fabric 独立包-Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

在[此处](service-fabric-cluster-standalone-package-contents.md)查找有关包内容的详细信息。

创建群集时，会自动下载 Service Fabric 运行时包。 如果从不连接到 internet 的计算机进行部署，请从此处下载带外的运行时包： <br>
[下载链接-Service Fabric 运行时-Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

在以下位置查找独立群集配置示例： <br>
[独立群集配置示例](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>创建群集
安装包安装了多个示例群集配置文件。 *Clusterconfig.x509.multimachine.json*是最简单的群集配置：在一台计算机上运行的不安全三节点群集。  其他配置文件描述了用 x.509 证书或 Windows 安全性保护的单个或多个计算机群集。  不需要为本教程修改任何默认的配置设置，只需查看配置文件，即可熟悉设置。  **节点**部分描述了群集中的三个节点：名称、IP 地址、[节点类型、容错域和升级域](service-fabric-cluster-manifest.md#nodes-on-the-cluster)。  **Properties**节定义群集的[安全性、可靠性级别、诊断集合和节点类型](service-fabric-cluster-manifest.md#cluster-properties)。

本文中创建的群集不安全。  任何人都可以进行匿名连接并执行管理操作，因此应始终使用 x.509 证书或 Windows 安全性保护生产群集。  安全设置仅在群集创建时配置，不能在创建群集后启用安全性。 更新配置文件启用[证书安全性](service-fabric-windows-cluster-x509-security.md)或[Windows 安全性](service-fabric-windows-cluster-windows-security.md)。 阅读[保护群集](service-fabric-cluster-security.md)，详细了解 Service Fabric 群集安全性。

### <a name="step-1-create-the-cluster"></a>步骤1：创建群集

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>方案 A：创建不受保护的本地开发群集
Service Fabric 可以通过使用[示例](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)中包含的*DevCluster*文件部署到一台计算机开发群集。

将独立包解压缩到计算机，将示例配置文件复制到本地计算机，然后通过管理员 PowerShell 会话从独立包文件夹中运行*createservicefabriccluster.ps1*脚本。

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

有关疑难解答的详细信息，请参阅[计划和准备群集部署](service-fabric-cluster-standalone-deployment-preparation.md)中的环境设置部分。

如果已完成运行开发方案，则可以通过参考 "[删除群集](#removecluster_anchor)" 一节中的步骤，从计算机中删除 Service Fabric 的群集。 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>方案 B：创建多计算机群集
完成规划和准备[群集部署](service-fabric-cluster-standalone-deployment-preparation.md)中详细介绍的规划和准备步骤后，即可使用群集配置文件创建生产群集。

部署和配置群集的群集管理员必须在计算机上具有管理员权限。 不能将 Service Fabric 安装在域控制器上。

1. 独立包中的*TestConfiguration*脚本用作最佳做法分析器，用于验证是否可以在给定环境中部署群集。 [部署准备](service-fabric-cluster-standalone-deployment-preparation.md)列出了先决条件和环境要求。 运行脚本，验证是否可以创建开发群集：  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    应会看到类似于下面的输出。 如果底部字段 "通过" 返回为 "True"，则已通过了健全性检查，且群集的外观可根据输入配置进行部署。

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. 创建群集：运行*createservicefabriccluster.ps1*脚本，以便在配置中的每台计算机上部署 Service Fabric 群集。 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> 部署跟踪将写入运行 Createservicefabriccluster.ps1 PowerShell 脚本的 VM/计算机。 可在 DeploymentTraces 的子文件夹中找到这些项，该目录位于运行脚本的目录中。 若要查看 Service Fabric 是否已正确部署到计算机，请在 FabricDataRoot 目录中找到已安装的文件，如群集配置文件 FabricSettings 部分（默认为 c:\programdata\sf）中所述。 同样，可以在任务管理器中运行 FabricHost 和构造 .exe 进程。
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>方案 C：创建脱机（internet 断开）群集
创建群集时自动下载 Service Fabric 运行时包。 将群集部署到未连接到 internet 的计算机时，需要单独下载 Service Fabric 运行时包，并在创建群集时提供其路径。
运行时包可从另一台连接到 internet 的计算机（[下载链接-Service Fabric 运行时-Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)）单独下载。 将运行时包复制到部署脱机群集的位置，并通过运行包含 `-FabricRuntimePackagePath` 参数的 `CreateServiceFabricCluster.ps1` 来创建群集，如以下示例中所示： 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.json*和 *.\MicrosoftAzureServiceFabric.cab*分别是群集配置和运行时 .cab 文件的路径。

### <a name="step-2-connect-to-the-cluster"></a>步骤2：连接到群集
连接到群集以验证群集是否正在运行且可用。 ServiceFabric PowerShell 模块与运行时一起安装。  你可以使用 Service Fabric 运行时从一个群集节点或远程计算机连接到群集。  [Connect-servicefabriccluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) cmdlet 可建立到群集的连接。

若要连接到不安全的群集，请运行以下 PowerShell 命令：

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

例如:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000
```

有关连接到群集的其他示例，请参阅[连接到安全群集](service-fabric-connect-to-secure-cluster.md)。 连接到群集后，使用[get-servicefabricnode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) cmdlet 显示群集中节点的列表和每个节点的状态信息。 对于每个节点， **HealthState**应为 *"正常"* 。

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>步骤3：使用 Service Fabric 资源管理器可视化群集
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)是一种用于可视化群集和管理应用程序的好工具。  Service Fabric Explorer 是在群集中运行的一项服务，通过导航到[http://localhost:19080/Explorer](http://localhost:19080/Explorer)，你可以使用浏览器访问该服务。

群集仪表板提供了群集的概述，包括应用程序和节点运行状况的摘要。 节点视图显示群集的物理布局。 对于给定的节点，可以检查哪些应用程序已在该节点上部署代码。

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>添加和删除节点
随着业务需求的变化，你可以在独立的 Service Fabric 群集中添加或删除节点。 有关详细步骤，请参阅[在 Service Fabric 独立群集中添加或删除节点](service-fabric-cluster-windows-server-add-remove-nodes.md)。

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>删除群集
若要删除群集，请运行包文件夹中的*Removeservicefabriccluster.ps1* PowerShell 脚本，并传入 JSON 配置文件的路径。 您可以选择指定删除的日志的位置。

此脚本可以在对群集配置文件中列为节点的所有计算机具有管理员访问权限的任何计算机上运行。 运行此脚本的计算机不一定是群集的一部分。

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>收集的遥测数据以及如何选择退出
默认情况下，该产品会根据 Service Fabric 使用量收集遥测数据，以改善产品。 作为安装程序一部分运行的最佳做法分析器会检查连接以[https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)。 如果无法访问，则安装将失败，除非您选择不启用遥测。

1. 遥测管道每天尝试将以下数据上载到[https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)一次。 这是一项尽力的上载，对群集功能没有影响。 仅从运行故障转移管理器主节点的节点发送遥测数据。 没有其他节点发送遥测数据。
2. 遥测数据由以下内容组成：

* 服务数量
* Servicetype 数
* 应用程序数量
* Applicationupgrade 数
* FailoverUnits 数
* InBuildFailoverUnits 数
* Unhealthyfailoverunit 数
* 副本数
* Inbuildreplica 数
* Standbyreplica 数
* Offlinereplica 数
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* 节点数
* IsContextComplete： True/False
* ClusterId：这是为每个群集随机生成的 GUID
* ServiceFabricVersion
* 从其上传遥测数据的虚拟机或计算机的 IP 地址

若要禁用遥测，请将以下内容添加到群集配置中的*属性*： *enableTelemetry： false*。

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>此包中包含的预览功能
无。


> [!NOTE]
> 从[适用于 Windows Server 的独立群集的新 GA 版本（版本 5.3.204.）](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)开始，可以手动或自动将群集升级到将来的版本。 有关详细信息，请参阅[升级独立 Service Fabric 群集版本](service-fabric-cluster-upgrade-windows-server.md)文档。
> 
> 

## <a name="next-steps"></a>后续步骤
* [使用 PowerShell 部署和删除应用程序](service-fabric-deploy-remove-applications.md)
* [独立 Windows 群集的配置设置](service-fabric-cluster-manifest.md)
* [向独立的 Service Fabric 群集添加节点或从中删除节点](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [升级独立 Service Fabric 群集版本](service-fabric-cluster-upgrade-windows-server.md)
* [使用运行 Windows 的 Azure Vm 创建独立的 Service Fabric 群集](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [使用 Windows 安全性保护 Windows 上的独立群集](service-fabric-windows-cluster-windows-security.md)
* [使用 X509 证书保护 Windows 上的独立群集](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
