---
title: Azure Batch 中的节点和池
description: 从开发的角度来了解计算节点和池及其在 Azure Batch 工作流中的运用。
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 5707d834a7d99e147a81ee2b39952863a63ed695
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144925"
---
# <a name="nodes-and-pools-in-azure-batch"></a>Azure Batch 中的节点和池

在 Azure Batch 工作流中，计算节点（或节点）是用于处理一部分应用程序工作负荷的虚拟机 。 池是运行应用程序的节点集合。 本文将详细介绍节点和池，以及创建节点和池并在 Azure Batch 工作流中使用时的注意事项。

## <a name="nodes"></a>Nodes

节点是专门用于处理一部分应用程序工作负荷的 Azure 虚拟机 (VM) 或云服务 VM。 节点大小确定了 CPU 核心数目、内存容量，以及分配给节点的本地文件系统大小。

可以使用 [Azure 虚拟机市场](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)提供的 Azure 云服务映像或自己准备的自定义映像创建 Windows 或 Linux 节点池。

节点可以运行节点操作系统环境支持的任何可执行文件或脚本。 可执行文件或脚本包括 \*.exe、\*.cmd、\*.bat 和 PowerShell 脚本（适用于 Windows），以及二进制文件、shell 和 Python 脚本（适用于 Linux）。

Batch 中的所有计算节点还包括：

- 任务可引用的标准[文件夹结构](files-and-directories.md)和关联的[环境变量](jobs-and-tasks.md)。
- **防火墙** 设置。
- [远程访问](error-handling.md#connect-to-compute-nodes) Windows（远程桌面协议 (RDP)）和 Linux（安全外壳 (SSH)）节点。

默认情况下，节点可以彼此通信，但它们无法与不属于同一池的虚拟机进行通信。 若要允许节点与其他虚拟机或本地网络进行安全通信，可以[在 Azure 虚拟网络的子网中](batch-virtual-network.md)预配该池 (VNet) 。 当你这样做时，可以通过公共 IP 地址访问节点。 这些公共 IP 地址是通过 Batch 创建的，并且可能会在池的生存期内更改。 你还可以[创建一个包含你控制的静态公共 IP 地址的池](create-pool-public-ip.md)，这样可确保它们不会意外更改。

## <a name="pools"></a>池

池是运行应用程序的节点集合。

Azure Batch 池构建在核心 Azure 计算平台的顶层。 它们提供大规模的分配、应用程序安装、数据分发和运行状况监视，以及在池内灵活调整（[缩放](#automatic-scaling-policy)）计算节点数目等功能。

添加到池中的每个节点都分配有唯一的名称和 IP 地址。 从池中删除某个节点时，会丢失对操作系统或文件所做的任何更改，并且节点的名称和 IP 地址将被释放供将来使用。 当某个节点退出池时，它的生存期即告结束。

池只能由创建它的 Batch 帐户使用。 Batch 帐户可以创建多个池，以满足将运行的应用程序的资源要求。

可以手动创建池；或者在你指定要完成的工作时，由 Batch 服务自动创建池。 在创建池时，可以指定以下属性：

- [节点操作系统和版本](#operating-system-and-version)
- [节点类型和目标节点数](#node-type-and-target)
- [节点大小](#node-size)
- [自动缩放策略](#automatic-scaling-policy)
- [任务计划策略](#task-scheduling-policy)
- [通信状态](#communication-status)
- [启动任务](#start-tasks)
- [应用程序包](#application-packages)
- [虚拟网络 (VNet) 和防火墙配置](#virtual-network-vnet-and-firewall-configuration)
- [生存期](#pool-and-compute-node-lifetime)

> [!IMPORTANT]
> Batch 帐户具有默认配额，用于限制 Batch 帐户中的核心数。 核心数对应于计算节点数。 可以在 [Azure Batch 服务的配额和限制](batch-quota-limit.md)中找到默认配额以及如何[提高配额](batch-quota-limit.md#increase-a-quota)的说明。 如果池不能实现其目标节点数，则问题可能出在核心配额上。

## <a name="operating-system-and-version"></a>操作系统和版本

在创建 Batch 池时，可指定 Azure 虚拟机配置和想要在池中每个计算节点上运行的操作系统类型。

## <a name="configurations"></a>配置

Batch 中提供了两种类型的池配置。

### <a name="virtual-machine-configuration"></a>虚拟机配置

虚拟机配置指定池由 Azure 虚拟机组成。 可以从 Linux 或 Windows 映像创建这些 VM。

基于虚拟机配置创建池时，不仅要指定节点大小和用于创建它们的映像源，还必须指定要安装在节点上的“虚拟机映像引用”和批处理“节点代理 SKU”。 有关指定这些池属性的详细信息，请参阅 [Provision Linux compute nodes in Azure Batch pools](batch-linux-nodes.md)（在 Azure Batch 池中预配 Linux 计算节点）。 可选选择性地将一个或多个空数据磁盘附加到从市场映像创建的池 VM，也可将数据磁盘包括在用于创建 VM 的自定义映像中。 如果包括数据磁盘，需要在 VM 中装载并格式化这些磁盘，然后才能使用。

### <a name="cloud-services-configuration"></a>云服务配置

云服务配置指定池由 Azure 云服务节点组成。 云服务只提供 Windows 计算节点。

[Azure Guest OS releases and SDK compatibility matrix](../cloud-services/cloud-services-guestos-update-matrix.md)（Azure 来宾 OS 版本和 SDK 兼容性对照表）中列出了适用于云服务配置池的操作系统。 创建包含云服务节点的池时，需要指定节点大小及其 OS 系列（用于确定哪些版本的 .NET 随 OS 一起安装）。 将云服务部署到 Azure 的速度比部署运行 Windows 的虚拟机更快。 如果需要 Windows 计算节点池，可能会发现云服务具有部署时间上的性能优势。

与云服务中的辅助角色一样，可以指定 *OS 版本*（有关辅助角色的详细信息，请参阅[云服务概述](../cloud-services/cloud-services-choose-me.md)）。 对于 OS 版本，建议指定 `Latest (*)`，使节点可自动升级，而无需采取措施来适应新的版本。 选择特定 OS 版本的主要用例是在允许更新版本之前执行向后兼容测试，以确保保持应用程序兼容性。 验证后，便可以更新池的 OS 版本并安装新的 OS 映像。 所有正在运行的任务将会中断并重新排队。

### <a name="node-agent-skus"></a>节点代理 SKU

创建池时，需要选择适当的 **nodeAgentSkuId**，具体取决于 VHD 基本映像的 OS。 可通过调用[列出支持的节点代理 SKU](/rest/api/batchservice/list-supported-node-agent-skus) 操作获得可用节点代理 SKU ID 到其 OS 映像引用的映射。

### <a name="custom-images-for-virtual-machine-pools"></a>虚拟机池的自定义映像

若要了解如何使用自定义映像创建池，请参阅[使用共享映像库创建自定义池](batch-sig-images.md)。

或者，可以使用[托管映像](batch-custom-images.md)资源创建自定义虚拟机池。 有关从 Azure VM 准备自定义 Linux 映像的信息，请参阅[如何创建虚拟机或 VHD 的映像](../virtual-machines/linux/capture-image.md)。 若要了解如何通过 Azure VM 准备自定义 Windows 映像，请参阅[在 Azure 中创建通用 VM 的托管映像](../virtual-machines/windows/capture-image-resource.md)。

### <a name="container-support-in-virtual-machine-pools"></a>虚拟机池中的容器支持

使用 Batch API 创建虚拟机配置池时，可以将池设置为在 Docker 容器中运行任务。 目前，必须使用支持 Docker 容器的映像创建池。 将 Windows Server 2016 Datacenter 与 Azure 市场中的容器映像配合使用，或者提供自定义 VM 映像（其中包含 Docker Community Edition 或 Enterprise Edition 以及任何必需的驱动程序）。 池设置必须包括[容器配置](/rest/api/batchservice/pool/add)，该配置在创建池时将容器映像复制到 VM。 然后，在池中运行的任务即可引用容器映像和容器运行选项。

有关详细信息，请参阅[在 Azure Batch 上运行 Docker 容器应用程序](batch-docker-container-workloads.md)。

## <a name="node-type-and-target"></a>节点类型和目标

创建池时，可以指定所需的节点类型和每种类型的目标节点数。 有两种类型的节点：

- 专用节点。 专用计算节点将为工作负荷保留。 它们比低优先级节点开销高，但可确保永远不会被抢占。
- 低优先级节点。 低优先级节点利用 Azure 中的多余容量运行 Batch 工作负荷。 低优先级节点每小时的成本比专用节点低，可支持需要大量计算能力的工作负荷。 有关详细信息，请参阅[在 Batch 中使用低优先级 VM](batch-low-pri-vms.md)。

当 Azure 的多余容量不足时，低优先级节点可能会被抢占。 如果某个节点在运行任务时被抢占，这些任务将重新排队并在计算节点重新变为可用后，重新运行。 对于作业完成时间很灵活且工作分布在多个节点上的工作负荷来说，低优先级节点是一个很好选择。 在决定为自己的方案使用低优先级节点之前，请确保会因其他资源优先使用而导致丢失的工作是最少的，且这些工作易于重新创建。

在同一池中可同时有低优先级计算节点和专用计算节点。 每种类型的节点都有其自己的目标设置，你可以为其指定所需的节点数。

计算节点数之所以称为*目标*，是因为在某些情况下，池可能无法达到所需的节点数。 例如，如果池先达到了 Batch 帐户的[核心配额](batch-quota-limit.md)，则该池可能达不到目标。 或者，如果已将限制最大节点数的自动缩放公式应用于池，则该池也可能达不到目标。

有关低优先级节点和专用节点的定价信息，请参阅 [Batch 定价](https://azure.microsoft.com/pricing/details/batch/)。

## <a name="node-size"></a>节点大小

创建 Azure Batch 池时，可以在 Azure 提供的几乎所有 VM 系列和大小中进行选择。 Azure 提供一系列适用于不同工作负荷的 VM 大小，包括专用 [HPC](../virtual-machines/sizes-hpc.md) 或[启用了 GPU](../virtual-machines/sizes-gpu.md) 的 VM 大小。 

有关详细信息，请参阅[在 Azure Batch 池中选择适用于计算节点的 VM 大小](batch-pool-vm-sizes.md)。

## <a name="automatic-scaling-policy"></a>自动缩放策略

对于动态工作负荷，可以将自动缩放策略应用于池。 Batch 服务将定期评估公式，并根据计算方案的当前工作负载和资源使用情况动态调整池中的节点数目。 这样，便可做到只使用所需资源并可释放不需要的资源，因而能够降低运行应用程序的整体成本。

可通过编写 [自动缩放公式](batch-automatic-scaling.md#automatic-scaling-formulas) 并将该公式与池相关联，来启用自动缩放。 Batch 服务使用该公式来确定池中下一个缩放间隔（可配置的间隔）的目标节点数目。 可以在创建池时指定池的自动缩放设置，或稍后在池上启用缩放。 还可以更新已启用缩放的池上的缩放设置。

例如，也许某个作业需要提交大量要执行的任务。 你可以将缩放公式分配到池，以根据当前的排队任务数和作业中任务的完成率来调整池中的节点数目。 Batch 服务将定期评估公式，并根据工作负荷和其他公式设置来调整池的大小。 该服务在有大量排队的任务时按需添加节点，在没有排队的任务或正在运行的任务时删除节点。

缩放公式可以基于以下度量值：

- **时间度量值** 基于指定的时数内每隔五分钟收集的统计信息。
- **资源度量值** 基于 CPU 使用率、带宽使用率、内存使用率和节点的数目。
- **任务指标**基于任务状态，例如“活动”（已排队）、“正在运行”或“已完成”。  

如果自动缩放会减少池中的计算节点数，则必须考虑如何处理在执行减少操作时运行的任务。 为了满足这一点，Batch 提供可包含在公式中的[节点解除分配选项](/rest/api/batchservice/pool/removenodes#computenodedeallocationoption)。 例如，可以指定运行中的任务立即停止，然后重新排入队列，以便在另一个节点上运行，或允许先完成再从池中删除节点。 请注意，在所有任务都已完成，或者所有任务保留期都已过期之前，将节点解除选项设置为 `taskcompletion` 或 `retaineddata` 会阻止池调整大小操作。

有关自动缩放应用程序的详细信息，请参阅 [自动缩放 Azure Batch 池中的计算节点](batch-automatic-scaling.md)。

> [!TIP]
> 若要获得最大的计算资源使用率，请将节点的目标数目设置成在作业结束时降为零，但允许正在运行的任务完成。

## <a name="task-scheduling-policy"></a>任务计划策略

[每个节点的最大任务数](batch-parallel-node-tasks.md) 配置选项确定了可以在池中每个计算节点上并行运行的最大任务数。

默认配置指定每次在节点上运行一个任务，但在某些情况下，在一个节点上同时执行两个或多个任务可能更有利。 请参阅 [concurrent node tasks](batch-parallel-node-tasks.md)（并发节点任务）一文中的[示例方案](batch-parallel-node-tasks.md#example-scenario)，了解如何通过在每个节点上运行多个任务来受益。

还可以指定一个填充类型，用于确定 Batch 是要将任务平均分散到池中的所有节点，还是在将最大数目的任务分配给一个节点后，再将任务分配给另一个节点。

## <a name="communication-status"></a>通信状态

在大多数情况下，任务将独立运行，并不需要彼此通信。 但是，某些应用程序中的任务必须能够通信，例如 [MPI 方案](batch-mpi.md)。

可将池配置为允许节点间通信，以便池中的节点可在运行时进行通信。 启用节点间通信时，云服务配置池中的节点可以在超过 1100 个端口上彼此通信，并且虚拟机配置池不会限制任何端口的流量。

启用节点间通信也会影响群集内的节点位置，并且由于部署限制，可能限制池中的最大节点数。 如果应用程序不需要节点之间的通信，Batch 服务可以将许多不同的群集和数据中心的大量节点分配给池，以发挥更强大的并行处理能力。

## <a name="start-tasks"></a>启动任务

如果需要，可以添加一个[启动任务](jobs-and-tasks.md#start-task)该任务将在每个节点加入池以及节点每次重新启动或重置映像时在该节点上运行。 启动任务特别适合用于准备计算节点，以便执行任务，例如，在计算节点上安装运行任务的应用程序。

## <a name="application-packages"></a>应用程序包

可以指定要部署到池中计算节点的应用程序包。 应用程序包提供任务运行的应用程序的简化部署和版本控制。 为池指定的应用程序包安装在加入该池的每个节点上，每次节点重新启动或重置映像时，将安装这些包。

若要详细了解如何使用应用程序包将应用程序部署到 Batch 节点，请参阅[使用 Batch 应用程序包将应用程序部署到计算节点](batch-application-packages.md)。

## <a name="virtual-network-vnet-and-firewall-configuration"></a>虚拟网络 (VNet) 和防火墙配置

在 Batch 中预配计算节点池时，可以将池与 Azure [虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 的子网相关联。 若要使用 Azure VNet，Batch 客户端 API 必须使用 Azure Active Directory (AD) 身份验证。 有关 Azure AD 的 Azure Batch 支持，请参阅[使用 Active Directory 对 Batch 服务解决方案进行身份验证](batch-aad-auth.md)。

### <a name="vnet-requirements"></a>VNet 要求

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

若要详细了解如何在 VNet 中设置 Batch 池，请参阅[通过虚拟网络创建虚拟机池](batch-virtual-network.md)。

> [!TIP]
> 若要确保用于访问节点的公共 IP 地址不会更改，可以[使用指定的公共 ip 地址来创建池](create-pool-public-ip.md)。

## <a name="pool-and-compute-node-lifetime"></a>池和计算节点生存期

在设计 Azure Batch 解决方案时，必须指定如何及何时创建池，以及这些池中的计算节点可用性要保持多久。

在极端情况下，可以针对提交的每个作业创建一个池，并在其任务执行完成时立即删除该池。 这样，只有在需要时才分配节点，节点空闲时会立即关闭，因此可以最大程度地提高利用率。 这意味着作业必须等待分配节点，但务必注意，在任务已单独分配并且启动任务已完成时，会立即计划待执行的任务。 批处理*不会*在等到池中的所有节点都可用后才将任务分配到节点。 这可确保最大程度地利用所有可用节点。

在另一种极端情况下，如果最高优先级是让作业立即启动，则你可以预先创建池，并使其节点在提交作业之前可用。 在此情况下，任务可以立即启动，但节点可能会保持空闲状态以等待分配任务。

通常会使用一种组合方法来处理可变但持续存在的负载。 可以有一个池来容纳提交的多个作业，并且可以根据作业负载扩展或缩减节点数目。 可以根据当前负载被动执行此操作，或者在负载可预测时主动执行此操作。 有关详细信息，请参阅[自动缩放策略](#automatic-scaling-policy)。

## <a name="security-with-certificates"></a>证书的安全性

在加密或解密任务的敏感信息（例如 [Azure 存储帐户](accounts.md#azure-storage-accounts)的密钥）时，通常需要使用证书。 为此，可以在节点上安装证书。 加密的机密通过命令行参数或内嵌在某个任务资源中来传递给任务，已安装的证书可用于解密机密。

可以使用[添加证书](/rest/api/batchservice/certificate/add)操作 (Batch REST) 或 [CertificateOperations.CreateCertificate](/dotnet/api/microsoft.azure.batch.certificateoperations) 方法 (Batch .NET) 将证书添加到 Batch 帐户。 然后，可以将该证书与新池或现有池相关联。

将证书与池关联后，Batch 服务将在池中的每个节点上安装该证书。 在启动节点之后、启动任何任务（包括[启动任务](jobs-and-tasks.md#start-task)和[作业管理器任务](jobs-and-tasks.md#job-manager-task)）之前，Batch 服务将安装相应的证书。

如果将证书添加到现有池，必须重新启动其计算节点，证书才会应用到节点。

## <a name="next-steps"></a>后续步骤

- 了解[作业和任务](jobs-and-tasks.md)。
