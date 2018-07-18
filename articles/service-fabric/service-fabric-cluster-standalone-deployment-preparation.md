---
title: Azure Service Fabric 独立群集部署准备 | Microsoft Docs
description: 本文介绍如何准备环境和创建群集配置，以便部署用于处理生产工作负荷的群集。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/12/2017
ms.author: dekapur
ms.openlocfilehash: 89fb9d97acd5cb658b58f957d35d426ecdacc75d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205104"
---
<a id="preparemachines"></a>

# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>规划和准备 Service Fabric 独立群集部署
在创建群集之前，请执行以下步骤。

## <a name="plan-your-cluster-infrastructure"></a>规划群集基础结构
需要在所“拥有”的计算机上创建 Service Fabric 群集，以便确定群集需应对的故障类型。 例如，是否需要为这些计算机单独提供电源线或 Internet 连接？ 此外，还应考虑这些计算机的物理安全性。 计算机位于何处，谁需要访问它们？ 在做出这些决定后，可以采用逻辑方式将计算机映射到多个容错域（请参阅下一步骤）。 相比于测试群集，生产群集的基础结构规划要更复杂。

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>确定容错域和升级域的数目
[*容错域* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) 是故障的物理单元，与数据中心的物理基础结构直接相关。 容错域由共享单一故障点的硬件组件（计算机、交换机、网络等）组成。 尽管容错域与机架之间没有 1:1 映射，但是大致上，可将每个机架视为一个容错域。

当在 ClusterConfig.json 中指定 FD 时，可以选择每个 FD 的名称。 Service Fabric 支持分层的 FD，因此，可以在 FD 中反映基础结构拓扑。  例如，以下 FD 是有效的：

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

有关 FD 和 UD 的更多详细信息，请参阅 [Service Fabric 群集介绍](service-fabric-cluster-resource-manager-cluster-description.md)。

如果能够完全控制节点的维护和管理（即负责更新和更换计算机），则应该将生产环境中的群集至少跨越三个 FD，这样才能使它在生产环境中受支持。 对于在无法完全控制计算机的环境（例如 Amazon Web Services VM 实例）中运行的群集，至少应在群集中部署五个 FD。 每个 FD 可以包含一个或多个节点。 这是为了防止计算机升级和更新造成问题，根据升级和更新的时间，这些问题可能会干扰群集中应用程序和服务的运行。

## <a name="determine-the-initial-cluster-size"></a>确定初始群集大小

通常，群集中的节点数是根据业务需求确定的，例如，要在群集上运行多少个服务和容器，以及需要多少个资源来维持工作负荷。 对于生产群集，我们建议在群集中至少跨 5 个 FD 部署 5 个节点。 但是，如前所述，如果可以完全控制节点并且可以跨三个 FD，则三个节点应该也能达到目的。

运行有状态工作负荷的测试群集应有三个节点，而只运行无状态工作负荷的测试群集则只需要一个节点。 另请注意，出于开发目的，可以在一台给定的计算机上包含多个节点。 但是，在生产环境中，对于每个物理机或虚拟机，Service Fabric 只支持一个节点。

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>准备好要充当节点的计算机

下面是要添加到群集的每台计算机的建议规格：

* 至少 16 GB RAM
* 至少 40 GB 可用磁盘空间
* 4 核或更高规格的 CPU
* 所有计算机与安全网络连接
* Windows Server 2012 R2 或 Windows Server 2016
* [.NET Framework 4.5.1 或更高版本](https://www.microsoft.com/download/details.aspx?id=40773)的完整安装版
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)
* 应在所有计算机上运行 [RemoteRegistry 服务](https://technet.microsoft.com/library/cc754820)

部署和配置群集的群集管理员必须拥有每台计算机的[管理员权限](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx)。 不能在域控制器上安装 Service Fabric。

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>下载适用于 Windows Server 的 Service Fabric 独立包
[下载链接 - Service Fabric 独立包 - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690)，并将包解压缩到群集外的一台部署计算机中或解压缩到群集内的其中一台计算机中。

## <a name="modify-cluster-configuration"></a>修改群集配置
若要创建一个独立群集，则必须创建一个独立群集配置 ClusterConfig.json 文件，并在其中说明该群集的规范。 可根据以下链接中的模板创建配置文件。 <br>
[独立群集配置](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

有关此文件中相关部分的详细信息，请参阅 [Windows 独立群集的配置设置](service-fabric-cluster-manifest.md)。

从已下载的程序包中打开某个 ClusterConfig.json 文件，并修改以下设置：
| **配置设置** | **说明** |
| --- | --- |
| **NodeTypes** |节点类型可让你将群集节点划分到不同的组中。 一个群集必须至少有一个节点类型。 组中的所有节点具有以下共同特征： <br> **名称** - 即节点类型名称。 <br>**终结点端口** - 即与此节点类型关联的各种命名终结点（端口）。 可以使用任何端口号，只要它们不会与此清单中的其他部分发生冲突，并且未被计算机/VM 上运行的其他应用程序使用。 <br> **放置属性** - 即此节点类型的相应属性，可用作系统服务或你的服务的放置约束。 这些属性是用户定义的键/值对，可为指定节点提供额外的元数据。 节点属性的示例包括节点是否有硬盘或图形卡、其硬盘的轴数、内核数和其他物理属性。 <br> **容量** - 节点容量，定义特定节点提供的特定资源的名称和数量。 例如，节点可以定义名为“MemoryInMb”的指标容量，而且默认有 2048 MB 的可用内存。 这些容量在运行时使用，以确保将需要特定资源量的服务放在具有所需数量的可用资源的节点上。<br>**IsPrimary** - 如果定义了多个 NodeType，请确保只有一个设置为主节点（值为 *true*），系统服务会在该主节点上运行。 应将所有其他节点类型设置为 *false* 值 |
| **Nodes** |这些是群集内的每个节点的详细信息（节点类型、节点名称、IP 地址、节点的容错域和升级域）。 要在其上创建群集的计算机必须与其 IP 地址一起列在此处。 <br> 如果对所有节点使用相同的 IP 地址，则会创建一个可用于测试的单机群集。 不要将单机群集用于部署生产工作负荷。 |

群集配置将所有设置配置到环境后，可针对群集环境对其进行测试（步骤 7）。

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>环境设置

群集管理员配置 Service Fabric 独立群集时，需按照以下准则设置环境： <br>
1. 对于群集配置文件中列出的所有计算机，创建群集的用户应对它们具有管理员级别的安全特权。
2. 从中创建群集的计算机和每个群集节点计算机必须：
* 已卸载 Service Fabric SDK
* 已卸载 Service Fabric 运行时 
* 已启用 Windows 防火墙服务 (mpssvc)
* 已启用远程注册表服务（远程注册表）
* 已启用文件共享 (SMB)
* 已基于群集配置端口打开所需的端口
* 已打开 Windows SMB 和远程注册表服务所需的端口：135、137、138、139 和 445
* 已相互建立网络连接
3. 所有群集节点计算机均不是域控制器。
4. 如果要部署的群集是安全群集，需确保存在所需的安全先决条件，且已针对配置进行了正确配置。
5. 如果群集计算机无法访问 Internet，请在群集配置中进行以下设置：
* 禁用遥测：在“属性”下，设置 *"enableTelemetry": false*
* 禁用自动下载 Fabric 版本和禁用通知当前群集版本支持即将终止：在“属性”下，设置 *"fabricClusterAutoupgradeEnabled": false*
* 或者，如果网络 Internet 访问仅限于允许列表中的域，则需要自动升级以下域：go.microsoft.com   download.microsoft.com

6. 设置适当的 Service Fabric 防病毒排除项：

| **防病毒排除的目录** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot（从群集配置中） |
| FabricLogRoot（从群集配置中） |

| **防病毒排除的进程** |
| --- |
| Fabric.exe |
| FabricHost.exe |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>使用 TestConfiguration 脚本验证环境
TestConfiguration.ps1 脚本位于独立包中。 该脚本可用作最佳做法分析器，验证上述部分标准，并应用作健全性检查，验证是否可在给定环境中部署群集。 如果出现任何故障，请参阅[环境设置](service-fabric-cluster-standalone-deployment-preparation.md)下的列表进行故障排除。 

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

目前，此配置测试模块不会验证安全配置，因此必须单独执行验证。  

> [!NOTE]
> 我们正在不断改进，旨在使此模块更加可靠，因此如果遇到了可能由 TestConfiguration 导致的故障或丢失情况，请通过我们的[支持通道](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)告知我们。   
> 
> 

## <a name="next-steps"></a>后续步骤
* [创建在 Windows Server 上运行的独立群集](service-fabric-cluster-creation-for-windows-server.md)
