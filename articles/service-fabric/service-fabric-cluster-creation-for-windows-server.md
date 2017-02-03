---
title: "创建和管理独立 Azure Service Fabric 群集 | Microsoft 文档"
description: "在运行 Windows Server 的任何本地或任意云计算机上创建和管理 Azure Service Fabric 群集（物理或虚拟）。"
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 31349169-de19-4be6-8742-ca20ac41eb9e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2016
ms.author: ryanwi;chackdan
translationtype: Human Translation
ms.sourcegitcommit: f9e0835a9b84c09eb42708606472a4ede34b1c0a
ms.openlocfilehash: 89bd97f6746503344f016cf8a473fb641beb9dde


---
# <a name="create-and-manage-a-cluster-running-on-windows-server"></a>创建和管理在 Windows Server 上运行的群集
你可以使用 Azure Service Fabric 在运行 Windows Server 的任何虚拟机或计算机上创建 Service Fabric 群集。 这意味着可以在包含一组相互连接的 Windows Server 计算机的任何环境（无论是本地环境还是任何云提供商所提供的环境）中部署和运行 Service Fabric 应用程序。 Service Fabric 提供了一个安装程序包，用于创建名为“Windows Server 独立包”的 Service Fabric 群集。

本文将引导你使用 Service Fabric 的独立包在本地完成创建群集的步骤，不过，你也可以针对其他任何环境（例如其他云提供商）轻松地进行调整。

> [!NOTE]
> 可购买此独立 Windows Server 包并将其用于生产部署。 此包可能包含“预览”形式的新 Service Fabric 功能。 向下滚动到“此包中包括的预览功能。”部分， 预览功能列表部分。 现在，可以[下载 EULA 的副本](http://go.microsoft.com/fwlink/?LinkID=733084)。
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-standalone-package"></a>获取 Service Fabric 独立包的支持
* 在 [Azure Service Fabric 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?)中询问社区关于 Windows Server 的 Service Fabric 独立包的信息。
* 打开 [Service Fabric 专业支持](http://support.microsoft.com/oas/default.aspx?prid=16146)票证。  了解有关 [Microsoft 专业支持](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)的详细信息。
* 还可通过 [Microsoft 顶级支持](https://support.microsoft.com/en-us/premier)获取对此包的支持，作为顶级支持的一部分 

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-standalone-package"></a>下载 Service Fabric 独立包
[下载适用于 Windows Server 2012 R2 及更高版本的 Service Fabric 独立包](http://go.microsoft.com/fwlink/?LinkId=730690)，其名为 Microsoft.Azure.ServiceFabric.WindowsServer.&lt;版本&gt;.zip。

在下载包中，你将看到以下文件：

| **文件名** | **简短说明** |
| --- | --- |
| MicrosoftAzureServiceFabric.cab |.cab 文件，其中包含部署到群集中每台计算机的二进制文件。 |
| ClusterConfig.Unsecure.DevCluster.json |群集配置示例文件，其中包含非安全型三节点式单计算机（或虚拟机）开发群集的设置，这些设置包括群集中每个节点的信息。 |
| ClusterConfig.Unsecure.MultiMachine.json |群集配置示例文件，其中包含非安全型多计算机（或虚拟机）群集的设置，这些设置包括群集中每个计算机的信息。 |
| ClusterConfig.Windows.DevCluster.json |群集配置示例文件，其中包含安全型三节点式单计算机（或虚拟机）开发群集的所有设置，这些设置包括群集中每个节点的信息。 此群集使用 [Windows 标识](https://msdn.microsoft.com/library/ff649396.aspx)进行保护。 |
| ClusterConfig.Windows.MultiMachine.json |群集配置示例文件，其中包含使用 Windows 安全性措施的安全型多计算机（或虚拟机）群集的所有设置，这些设置包括安全群集中每个计算机的信息。 此群集使用 [Windows 标识](https://msdn.microsoft.com/library/ff649396.aspx)进行保护。 |
| ClusterConfig.x509.DevCluster.json |群集配置示例文件，其中包含安全型三节点式单计算机（或虚拟机）开发群集的所有设置，这些设置包括群集中每个节点的信息。 此群集使用 x509 证书进行保护。 |
| ClusterConfig.x509.MultiMachine.json |群集配置示例文件，其中包含安全型多计算机（或虚拟机）群集的所有设置，这些设置包括安全群集中每个节点的信息。 此群集使用 x509 证书进行保护。 |
| EULA_ENU.txt |有关使用 Microsoft Azure Service Fabric Windows Server 独立包的许可条款。 现在，可以[下载 EULA 的副本](http://go.microsoft.com/fwlink/?LinkID=733084)。 |
| Readme.txt |发行说明和基本安装说明的链接。 这是本文中说明的子集。 |
| CreateServiceFabricCluster.ps1 |PowerShell 脚本，用于通过 ClusterConfig.json 中的设置创建群集。 |
| RemoveServiceFabricCluster.ps1 |PowerShell 脚本，用于通过 ClusterConfig.json 中的设置删除群集。 |
| ThirdPartyNotice.rtf |包中的第三方软件的通知。 |
| AddNode.ps1 |PowerShell 脚本，用于将节点添加到现有的部署群集。 |
| RemoveNode.ps1 |PowerShell 脚本，用于将节点从现有的部署群集中删除。 |
| CleanFabric.ps1 |PowerShell 脚本，用于从当前计算机中清除独立 Service Fabric 安装。 应使用以前的 MSI 安装的自身关联卸载程序来删除以前的安装。 |
| TestConfiguration.ps1 |PowerShell 脚本，用于分析 Cluster.json 中指定的基础结构。 |

## <a name="plan-and-prepare-your-cluster-deployment"></a>规划和准备群集部署
在创建群集之前，请执行以下步骤。

### <a name="step-1-plan-your-cluster-infrastructure"></a>步骤 1：规划群集基础结构
您需要在所拥有的计算机上创建 Service Fabric 群集，以便确定群集需应对的故障类型。 例如，是否需要为这些计算机单独提供电源线或 Internet 连接？ 此外，还应考虑这些计算机的物理安全性。 计算机位于何处，谁需要访问它们？ 在你做出这些决定后，可以采用逻辑方式将计算机映射到多个容错域（请参阅步骤 4）。 相比于测试群集，生产群集的基础结构规划要更复杂。

<a id="preparemachines"></a>

### <a name="step-2-prepare-the-machines-to-meet-the-prerequisites"></a>步骤 2：准备符合先决条件的计算机
要添加到群集的每台计算机的先决条件：

* 建议至少提供 16 GB RAM。
* 建议至少提供 40 GB 的可用磁盘空间。
* 建议提供 4 核或更高的 CPU。
* 所有计算机与安全网络连接。
* Windows Server 2012 R2 或 Windows Server 2016。 
* [.NET Framework 4.5.1 或更高版本](https://www.microsoft.com/download/details.aspx?id=40773)的完整安装版。
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)。
* 应在所有计算机上运行 [RemoteRegistry 服务](https://technet.microsoft.com/library/cc754820)。

部署和配置群集的群集管理员必须拥有每台计算机的[管理员权限](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx)。 不能在域控制器上安装 Service Fabric。

### <a name="step-3-determine-the-initial-cluster-size"></a>步骤 3：确定初始群集大小
独立 Service Fabric 群集中的每个节点都应部署 Service Fabric 运行时，并且都是该群集的成员。 在典型的生产部署中，每个 OS 实例（物理或虚拟）都有一个节点。 群集大小由你的业务需求确定。 但是，群集大小必须至少为三个节点（计算机或虚拟机）。
出于开发目的，可以在一台指定的计算机上创建多个节点。 在生产环境中，对于每个物理机或虚拟机，Service Fabric 只支持一个节点。

### <a name="step-4-determine-the-number-of-fault-domains-and-upgrade-domains"></a>步骤 4：确定容错域和升级域的数目
*容错域* (FD) 是故障的物理单元，与数据中心的物理基础结构直接相关。 容错域由共享单一故障点的硬件组件（计算机、交换机、网络等）组成。 尽管容错域与机架之间没有 1:1 映射，但是大致上，可将每个机架视为一个容错域。 在考虑群集中的节点时，强烈建议至少在三个容错域之间分布节点。

当在 ClusterConfig.json 中指定 FD 时，可以选择每个 FD 的名称。 Service Fabric 支持分层的 FD，因此，你可以在 FD 中反映基础结构拓扑。  例如，以下 FD 是有效的：

* "faultDomain": "fd:/Room1/Rack1/Machine1"
* "faultDomain": "fd:/FD1"
* "faultDomain": "fd:/Room1/Rack1/PDU1/M1"

*升级域* (UD) 是节点的逻辑单元。 在 Service Fabric 协调式升级（应用程序升级或群集升级）期间，将关闭 UD 中的所有节点以执行升级，而其他 UD 中的节点仍可用来为请求提供服务。 对计算机进行的固件升级将不遵循 UD，因此每次必须在一台计算机上执行此操作。

领会这些概念的最简单方法是将 FD 视为非计划内故障的单元，将 UD 视为计划维护的单元。

当在 ClusterConfig.json 中指定 UD 时，可以选择每个 UD 的名称。 例如，以下名称是有效的：

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blue"

有关升级域和容错域的更多详细信息，请参阅 [Service Fabric 群集介绍](service-fabric-cluster-resource-manager-cluster-description.md)。

### <a name="step-5-download-the-service-fabric-standalone-package-for-windows-server"></a>步骤 5：下载适用于 Windows Server 的 Service Fabric 独立包
下载[适用于 Windows Server 的 Service Fabric 独立包](http://go.microsoft.com/fwlink/?LinkId=730690)，并将包解压缩到群集外的一台部署计算机中或解压缩到群集内的其中一台计算机中。 你可以重命名已解压缩的文件夹 `Microsoft.Azure.ServiceFabric.WindowsServer`。

<a id="createcluster"></a>

## <a name="create-your-cluster"></a>创建群集
完成规划和准备步骤之后，可以开始创建群集。

### <a name="step-1-modify-cluster-configuration"></a>步骤 1：修改群集配置
ClusterConfig.json 对群集进行了描述。 有关此文件中相关部分的详细信息，请参阅 [Windows 独立群集的配置设置](service-fabric-cluster-manifest.md)。
从已下载的程序包中打开某个 ClusterConfig.json 文件，然后修改以下设置：

<!--Loc Comment: Please, check that line 129 the clause has been modified to "that you use as placement constraints" instead of using "you are used as placement constraints"-->

| **配置设置** | **说明** |
| --- | --- |
| **NodeTypes** |节点类型可让你将群集节点划分到不同的组中。 一个群集必须至少有一个节点类型。 组中的所有节点具有以下共同特征： <br> **名称** - 即节点类型名称。 <br>**终结点端口** - 即与此节点类型关联的各种命名终结点（端口）。 可以使用任何端口号，只要它们不会与此清单中的其他部分发生冲突，并且未被计算机/VM 上运行的其他应用程序使用。 <br> **放置属性** - 即此节点类型的相应属性，可用作系统服务或你拥有的服务的放置约束。 这些属性是用户定义的键/值对，可为指定节点提供额外的元数据。 节点属性的示例包括节点是否有硬盘或图形卡、其硬盘的轴数、内核数和其他物理属性。 <br> **容量** - 节点容量，定义特定节点提供的特定资源的名称和数量。 例如，节点可以定义名为“MemoryInMb”的指标容量，而且默认有 2048 MB 的可用内存。 这些容量在运行时使用，以确保将需要特定资源量的服务放在具有所需数量的可用资源的节点上。<br>**IsPrimary** - 如果定义了多个 NodeType，请确保只有一个设置为主节点（值为 *true*），系统服务将在该主节点上运行。 应将所有其他节点类型设置为 *false* 值 |
| **Nodes** |这些是群集内的每个节点的详细信息（节点类型、节点名称、IP 地址、节点的容错域和升级域）。 要在其上创建群集的计算机必须与其 IP 地址一起列在此处。 <br> 如果对所有节点使用相同的 IP 地址，则会创建一个可用于测试的单机群集。 不要将单机群集用于部署生产工作负荷。 |

### <a name="step-2-run-the-testconfiguration-script"></a>步骤 2：运行 TestConfiguration 脚本
TestConfiguration 脚本按照 cluster.json 中的定义测试你的基础结构，确保分配了所需的权限、计算机彼此连接到对方以及定义了其他属性，以便部署成功完成。 本质上而言，它是一个小型最佳实践分析工具。 随着时间推移，我们会继续将更多验证添加到此工具以使其更强大。

可以在对群集配置文件中列为节点的所有计算机具有管理员访问权限的任何计算机上运行此脚本。 运行此脚本的计算机不一定是群集的一部分。

```powershell

PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
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

### <a name="step-3-run-the-create-cluster-script"></a>步骤 3：运行创建群集脚本
在 JSON 文档中修改群集配置并在其中添加所有节点信息之后，运行包文件夹中用于创建群集的 *CreateServiceFabricCluster.ps1* PowerShell 脚本，并传入 JSON 配置文件的路径。 完成此操作时，接受 EULA。

可以在对群集配置文件中列为节点的所有计算机具有管理员访问权限的任何计算机上运行此脚本。 运行此脚本的计算机不一定是群集的一部分。

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

> [!NOTE]
> 部署日志可在 VM/计算机（已在其上运行 CreateServiceFabricCluster PowerShell）上本地使用。 可以在名为“DeploymentTraces”的子文件夹中找到这些日志，该子文件夹位于运行过 Powershell 命令的文件夹下。 若要查看 Service Fabric 是否已正确部署到某个计算机，可在 C:\ProgramData 目录中查找已安装文件，并可在任务管理器中查看正在运行的 FabricHost.exe 和 Fabric.exe 进程。
> 
> 

### <a name="step-4-connect-to-the-cluster"></a>步骤 4：连接到群集
若要连接到安全群集，请参阅 [Service Fabric 连接到安全群集](service-fabric-connect-to-secure-cluster.md)。

若要连接到不安全的群集，请运行以下 PowerShell 命令：

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### <a name="step-5-bring-up-service-fabric-explorer"></a>步骤 5：打开 Service Fabric Explorer
现在可以通过 Service Fabric Explorer 连接到群集，既可以直接从装有 http://localhost:19080/Explorer/index.html 的某台计算机进行连接，也可以通过 http://<*IPAddressofaMachine*>:19080/Explorer/index.html 进行远程连接。

## <a name="add-and-remove-nodes"></a>添加和删除节点
当业务需要改变时，您可以向独立 Service Fabric 群集添加或删除节点。 参阅[向 Service Fabric 独立群集添加或删节点](service-fabric-cluster-windows-server-add-remove-nodes.md)以了解详细步骤。

## <a name="remove-a-cluster"></a>删除群集
若要删除群集，请运行包文件夹中的 *RemoveServiceFabricCluster.ps1* Powershell 脚本，并传入 JSON 配置文件的路径。 你可以选择指定删除的日志的位置。

可以在对群集配置文件中列为节点的所有计算机具有管理员访问权限的任何计算机上运行此脚本。 运行此脚本的计算机不一定是群集的一部分。

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```


<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>收集遥测数据以及如何选择不使用它
默认情况下，产品收集 Service Fabric 使用情况的遥测数据来改进产品。 作为安装程序一部分运行的最佳实践分析工具检查与 [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) 的连接。 如果不可访问，安装将失败，除非你选择不使用遥测。

1. 遥测管道尝试每日一次将以下数据上载到 [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1)。 这是尽力而为的上载，不会影响群集功能。 仅从运行故障转移管理器的主节点发送遥测数据。 其他节点不发送遥测数据。
2. 遥测数据由以下项组成：

* 服务数量
* ServiceType 数量
* 应用程序数量
* ApplicationUpgrade 数量
* FailoverUnit 数量
* InBuildFailoverUnit 数量
* UnhealthyFailoverUnit 数量
* 副本数量
* InBuildReplica 数量
* StandByReplica 数量
* OfflineReplica 数量
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* 节点数量
* IsContextComplete：True/False
* ClusterId：这是为每个群集随机生成的 GUID
* ServiceFabricVersion
* 从其上载遥测数据的虚拟机或计算机的 IP 地址

若要禁用遥测数据，请将以下内容添加到群集配置中的*属性*：*enableTelemetry: false*。

<a id="previewfeatures"></a>

## <a name="preview-features-included-in-this-package"></a>此包中包括的预览功能
无。


> [!NOTE]
> 从[适用于 Windows Server 的独立群集的新 GA 版本（版本 5.3.204.x）](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/)开始，可以将群集手动或自动升级到未来版本。 请参阅[升级独立 Service Fabric 群集版本](service-fabric-cluster-upgrade-windows-server.md)文档以获取详细信息。
> 
> 

## <a name="next-steps"></a>后续步骤
* [独立 Windows 群集的配置设置](service-fabric-cluster-manifest.md)
* [在独立 Service Fabric 群集中添加或删除节点](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [升级独立 Service Fabric 群集版本](service-fabric-cluster-upgrade-windows-server.md)
* [使用运行 Windows 的 Azure VM 创建独立 Service Fabric 群集](service-fabric-cluster-creation-with-windows-azure-vms.md)
* [使用 Windows 安全性保护 Windows 上的独立群集](service-fabric-windows-cluster-windows-security.md)
* [使用 X509 证书保护 Windows 上的独立群集](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png



<!--HONumber=Dec16_HO2-->


