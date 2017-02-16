---
title: "设置 Windows RDMA 群集以运行 MPI 应用程序 | Microsoft Docs"
description: "了解如何创建包含 H16r、H16mr、A8 或 A9 大小 VM 的 Windows HPC Pack 群集，借以使用 Azure RDMA 网络运行 MPI 应用。"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: ff9fb5f0b2229a470ea3f5c736622ee1e9228c93
ms.openlocfilehash: 4120ca24bab11f21fe66d5219f06608e33b527d1


---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>使用 HPC Pack 设置一个运行 MPI 应用程序的 Windows RDMA 群集
在 Azure 中使用 [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) 和 [H 系列或计算密集型 A 系列实例](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)设置 Windows RDMA 群集，用于运行并行消息传递接口 (MPI) 应用程序。 在 HPC Pack 群集中设置支持 RDMA 且基于 Windows Server 的节点时，MPI 应用程序将在 Azure 中利用基于远程直接内存访问 (RDMA) 技术的低延迟、高吞吐量网络实现高效通信。

如果要在访问 Azure RDMA 网络的 Linux VM 上运行 MPI 工作负荷，请参阅[设置 Linux RDMA 群集以运行 MPI 应用程序](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。

## <a name="hpc-pack-cluster-deployment-options"></a>HPC Pack 群集部署选项
Microsoft HPC Pack 是一款无需额外费用的工具，可用于创建本地 HPC 群集或在 Azure 中运行 Windows/Linux HPC 应用程序。 HPC Pack 中包括适用于 Windows 消息传递接口的 Microsoft 实现 (MS-MPI) 的运行时环境。 配合使用支持 RDMA 的实例（此类实例运行受支持的 Windows Server 操作系统）时，HPC Pack 可高效地运行访问 Azure RDMA 网络的 Windows MPI 应用程序。 

本文介绍使用 Microsoft HPC Pack 设置 Windows RDMA 群集的以下两种方案，并提供详细指南链接。 

* 方案 1. 部署计算密集型辅助角色实例 (PaaS)
* 方案 2. 在计算密集型 VM (IaaS) 中部署计算节点

有关通过 Windows 使用计算密集型实例的常规先决条件，请参阅[关于 H 系列和计算密集型 A 系列 VM](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>方案 1：部署计算密集型辅助角色实例 (PaaS)
从现有的 HPC Pack 群集，添加运行在云服务 (PaaS) 中的 Azure 辅助角色实例（Azure 节点）形式的额外计算资源。 此功能（也称为从 HPC Pack“迸发到 Azure”）支持一定范围的辅助角色实例大小。 添加 Azure 节点时，请指定一个支持 RDMA 的大小。

从现有群集（通常在本地）迸发到支持 RDMA 的 Azure 实例时需注意以下事项和步骤。 可以使用类似的过程，将辅助角色实例添加到部署在 Azure VM 中的 HPC Pack 头节点。

> [!NOTE]
> 有关使用 HPC Pack 迸发到 Azure 的教程，请参阅[使用 HPC Pack 设置混合群集](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)。 请注意，以下步骤中的注意事项仅适用于支持 RDMA 的 Azure 节点。
> 
> 

![迸发到 Azure][burst]

### <a name="steps"></a>步骤
1. **部署和配置 HPC Pack 2012 R2 头节点**
   
    从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=49922)下载最新的 HPC Pack 安装包。 有关 Azure 迸发部署准备工作的要求和说明，请参阅[使用 Microsoft HPC Pack 迸发到 Azure 辅助角色实例](https://technet.microsoft.com/library/gg481749.aspx)。
2. **在 Azure 订阅中配置管理证书**
   
    配置证书以保护头节点与 Azure 之间的连接。 有关选项和过程，请参阅[为 HPC Pack 配置 Azure 管理证书的方案](http://technet.microsoft.com/library/gg481759.aspx)。 对于测试部署，HPC Pack 安装默认 Microsoft HPC Azure 管理证书，用户可以将该证书快速上载到 Azure 订阅。
3. **创建新的云服务和存储帐户**
   
    通过 Azure 经典门户，在可使用支持 RDMA 的实例的区域中创建要部署的云服务和存储帐户。
4. **创建 Azure 节点模板**
   
    在 HPC 群集管理器中使用“创建节点模板”向导。 有关步骤，请参阅“使用 Microsoft HPC Pack 部署 Azure 节点的步骤”中的[创建 Azure 节点模板](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ)。
   
    对于初始测试，我们建议在模板中配置手动可用性策略。
5. **向群集添加节点**
   
    在 HPC 群集管理器中使用“添加节点”向导。 有关详细信息，请参阅[向 Windows HPC 群集中添加 Azure 节点](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add)。
   
    指定节点大小时，请选择一个支持 RDMA 的实例大小。
   
   > [!NOTE]
   > 在每个使用计算密集型实例“迸发到 Azure”的部署中，除了指定的 Azure 辅助角色实例外，HPC Pack 至少还会将两个支持 RDMA 的实例（例如 A8）自动部署为代理节点。 代理节点使用分配给订阅的内核，并会与 Azure 辅助角色实例一起产生费用。
   > 
   > 
6. **启动（预配）节点并使它们联机以运行作业**
   
    在 HPC 群集管理器中选择节点，并使用“启动”操作。 完成预配后，在 HPC 群集管理器中选择节点并使用“联机”操作。 节点将准备运行作业。
7. **向群集提交作业**
   
   使用 HPC Pack 作业提交工具来运行群集作业。 请参阅 [Microsoft HPC Pack：作业管理](http://technet.microsoft.com/library/jj899585.aspx)。
8. **停止（取消预配）节点**
   
   完成运行作业后，使节点脱机，并在 HPC 群集管理器中使用“停止”操作。

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>方案 2：在计算密集型 VM (IaaS) 中部署计算节点
在此方案中，可以将 HPC Pack 头节点和群集计算节点部署到 Azure 虚拟网络中的 VM 上。 HPC Pack 在 Azure VM 中提供了多个[部署选项](virtual-machines-linux-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)，包括自动部署脚本和 Azure 快速入门模板。 例如，以下注意事项和步骤将指导你使用 [HPC Pack IaaS 部署脚本](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)在 Azure 中自动部署 HPC Pack 2012 R2 群集。

![Azure VM 中的群集][iaas]

### <a name="steps"></a>步骤
1. **在客户端计算机上运行 HPC Pack IaaS 部署脚本，创建群集头节点和计算节点 VM**
   
    从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=49922)下载 HPC Pack IaaS 部署脚本包。
   
    若要准备客户端计算机、创建脚本配置文件并运行脚本，请参阅[使用 HPC Pack IaaS 部署脚本创建 HPC 群集](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 
   
    若要部署支持 RDMA 的计算节点，请注意以下附加事项：
   
   * **虚拟网络**：在可使用支持 RDMA 的所需实例大小的区域中，指定一个新的虚拟网络。
   * **Windows Server 操作系统**：若要支持 RDMA 连接，请为计算节点 VM 指定 Windows Server 2012 R2 或 Windows Server 2012 操作系统。
   * **云服务**：建议在两个不同的云服务中分别部署头节点和计算节点。
   * **头节点大小**：在此方案中，请考虑至少将头节点设为 A4（特大）大小。
   * **HpcVmDrivers 扩展**：部署大小为 A8 或 A9、运行 Windows Server 操作系统的计算节点时，部署脚本会自动安装 Azure VM 代理和 HpcVmDrivers 扩展。 HpcVmDrivers 扩展将驱动程序安装在计算节点 VM 上，以便它们可以连接到 RDMA 网络。 在支持 RDMA 的 H 系列 VM 上，必须手动安装 HpcVmDrivers 扩展。 请参阅 [About H-series and compute-intensive A-series VMs](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#access-to-the-rdma-network)（关于 H 系列和计算密集型 A 系列 VM）。
   * **群集网络配置**：部署脚本将自动设置拓扑 5 中的 HPC Pack 群集（企业网络上的所有节点）。 此拓扑是 VM 中所有 HPC Pack 群集部署所必需的。 以后请不要更改群集网络拓扑。
2. **使计算节点联机以运行作业**
   
    在 HPC 群集管理器中选择节点，并使用“联机”操作。 节点将准备运行作业。
3. **向群集提交作业**
   
    连接到头节点以提交作业，或将本地计算机设置为执行此操作。 有关信息，请参阅[向 Azure 中的 HPC 群集提交作业](virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
4. **使节点脱机并停止（解除分配）节点**
   
    完成运行作业后，请在 HPC 群集管理器中使节点脱机。 然后，使用 Azure 管理工具来关闭这些节点。

## <a name="run-mpi-applications-on-the-cluster"></a>在群集上运行 MPI 应用程序
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>示例：在 HPC Pack 群集上运行 mpipingpong
若要验证支持 RDMA 的实例的 HPC Pack 部署，请在群集上运行 HPC Pack **mpipingpong** 命令。 **mpipingpong** 在配对的节点之间反复发送数据包，以计算启用了 RDMA 的应用程序网络的延迟和吞吐量度量值以及统计信息。 本示例演示了使用群集 **mpiexec** 命令运行 MPI 作业（在本例中为 **mpipingpong**）的典型模式。

本示例假定已在“迸发到 Azure”配置（[方案 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article)）中添加了 Azure 节点。 如果你在 Azure VM 群集中部署了 HPC Pack，则需要修改命令语法以指定不同的节点组，并设置其他环境变量以将网络流量定向到 RDMA 网络。

在群集上运行 mpipingpong：

1. 在头节点或正确配置的客户端计算机上，打开“命令提示符”。
2. 若要估计四节点的 Azure 迸发部署中节点对之间的延迟时间，请键入以下命令提交使用小数据包大小和多个迭代来运行 mpipingpong 的作业：
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    该命令将返回提交的作业的 ID。
   
    如果在 Azure VM 上部署了 HPC Pack 群集，请指定包含在单个云服务中部署的计算节点 VM 的节点组，并按如下所示修改 **mpiexec** 命令：
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. 完成该作业后，如果要查看输出（在此例中为作业的任务 1 的输出），请键入以下命令：
   
    ```Command
    task view <JobID>.1
    ```
   
    其中 &lt;*JobID*&gt; 为已提交作业的 ID。
   
    输出将包含类似于下面的延迟结果。
   
    ![弹性延迟][pingpong1]
4. 若要估计 Azure 迸发节点对之间的吞吐量，请键入以下命令，提交使用大数据包和几个迭代来运行 **mpipingpong** 的作业：
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    该命令将返回提交的作业的 ID。
   
    在 Azure VM 上部署的 HPC Pack 群集中，修改步骤 2 中所示的命令。
5. 完成该作业后，如果要查看输出（在此例中为作业的任务 1 的输出），请键入以下命令：
   
    ```Command
    task view <JobID>.1
    ```
   
   输出将包含类似于下面的吞吐量结果。
   
   ![弹性吞吐量][pingpong2]

### <a name="mpi-application-considerations"></a>MPI 应用程序注意事项
在 Azure 中使用 HPC Pack 运行 MPI 应用程序时请注意以下事项。 某些注意事项只适用于 Azure 节点（在“迸发到 Azure”配置中添加的辅助角色实例）的部署。

* Azure 会定期对云服务中的辅助角色实例进行重新预配，且不会事先通知（例如，出于系统维护目的，或者在实例失败时）。 如果在实例运行 MPI 作业的同时重新预配，该实例将丢失数据并回到首次部署它时的状态，进而可能导致 MPI 作业失败。 用于单个 MPI 作业的节点越多，该作业的运行时间就会越长，在某一作业正在运行时重新预配其中一个实例的可能性就越大。 如果在部署中将单个节点指定为文件服务器，则也应考虑此情况。
* 无需使用支持 RDMA 的实例即可在 Azure 中运行 MPI 作业。 你可以使用 HPC Pack 支持的任何实例大小。 但是，若要运行较大规模的 MPI 作业且这些作业易受连接节点的网络的延迟和带宽影响，建议使用支持 RDMA 的实例。 如果使用其他大小运行易受延迟和带宽影响的 MPI 作业，建议运行小型作业，其中单个任务仅在几个节点上运行。
* 部署到 Azure 实例的应用程序将受到与应用程序关联的许可条款的限制。 请向商业应用程序的供应商咨询有关在云中运行的许可或其他限制。 并非所有供应商都提供即付即用许可。
* Azure 实例需要进一步设置才能访问本地节点、共享和许可证服务器。 例如，要使 Azure 节点能够访问本地许可证服务器，你可以配置站点到站点 Azure 虚拟网络。
* 若要在 Azure 实例上运行 MPI 应用程序，必须通过运行 **hpcfwutil** 命令，向这些实例上的 Windows 防火墙注册每个 MPI 应用程序。 这样便可以在防火墙动态分配的端口上进行 MPI 通信。
  
  > [!NOTE]
  > 对于“迸发到 Azure”部署，你还可以配置一个防火墙例外命令，以便在添加到群集的所有新 Azure 节点上自动运行。 在运行 **hpcfwutil** 命令并确认应用程序正常工作后，可以将该命令添加到 Azure 节点的启动脚本。 有关详细信息，请参阅[对 Azure 节点使用启动脚本](https://technet.microsoft.com/library/jj899632.aspx)。
  > 
  > 
* HPC Pack 使用 CCP_MPI_NETMASK 群集环境变量为 MPI 通信指定可接受地址范围。 从 HPC Pack 2012 R2 开始，CCP_MPI_NETMASK 群集环境变量仅影响已加入域的群集计算节点（在本地或 Azure VM 中）之间的 MPI 通信。 该变量将被已添加到“迸发到 Azure”配置中的节点忽略。
* MPI 作业不能跨部署在不同云服务中的 Azure 实例运行（例如，不能在使用不同节点模板的“迸发到 Azure”部署中或部署在多个云服务中的 Azure VM 计算节点上运行）。 如果你有使用不同节点模板启动的多个 Azure 节点部署，则 MPI 作业必须仅在一组 Azure 节点上运行。
* 在你向群集添加 Azure 节点并且使它们处于联机状态时，HPC 作业计划程序服务将会立即尝试启动这些节点上的作业。 如果你只有一部分的工作负荷可以在 Azure 上运行，请确保更新或创建作业模板以定义可在 Azure 上运行的作业类型。 例如，若要确保使用某一作业模板提交的作业仅在 Azure 节点上运行，你可以向该作业模板中添加“节点组”属性并且选择 AzureNodes 作为所需值。 若要为你的 Azure 节点创建自定义组，可以使用 Add-HpcGroup HPC PowerShell cmdlet。

## <a name="next-steps"></a>后续步骤
* 使用 HPC Pack 的替代方法是使用 Azure Batch 服务进行开发，以便在 Azure 中的计算节点池上运行 MPI 应用程序。 请参阅[在 Azure Batch 中使用多实例任务运行消息传递接口 (MPI) 应用程序](../batch/batch-mpi.md)。
* 如果要运行访问 Azure RDMA 网络的 Linux MPI 应用程序，请参阅[设置 Linux RDMA 群集以运行 MPI 应用程序](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)。

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/burst.png
[iaas]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/iaas.png
[pingpong1]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong2.png



<!--HONumber=Jan17_HO1-->


