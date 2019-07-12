---
title: Azure 迁移服务器迁移的基于代理的迁移体系结构
description: 概述使用 Azure 迁移服务器迁移基于代理的 VMware VM 迁移。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: raynew
ms.openlocfilehash: 21c779587842c976ba93d7fa592a91ee714bc55c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811149"
---
# <a name="agent-based-migration-architecture"></a>基于代理的迁移体系结构

本文概述了体系结构和用于使用 Azure 迁移 Server 迁移工具代理基于复制的过程。

[Azure Migrate](migrate-services-overview.md)提供中心，以跟踪发现、 评估和本地应用和工作负荷和 AWS/GCP VM 实例，迁移到 Azure 的迁移。 中心提供了评估和迁移，以及第三方独立软件供应商 (ISV) 产品/服务的 Azure 迁移工具。

## <a name="agent-based-replication"></a>基于代理的复制

Azure 迁移 Server 复制中使用工具迁移基于代理的复制的本地 VMware Vm 和物理服务器到 Azure。 此外可以用于迁移其他本地虚拟化服务器，以及为私有云和公有云虚拟机，包括 AWS 实例和 GCP Vm。

对于 VMware 迁移，Azure 迁移 Server 迁移工具提供了几个选项：

- 在本文中所述，使用基于代理的复制的迁移。
- 无代理复制，若要将 Vm 迁移而无需在其上安装任何内容。

详细了解如何[vmware 选择迁移方法](server-migrate-overview.md)。

## <a name="server-migration-and-azure-site-recovery"></a>服务器迁移和 Azure Site Recovery

Azure 迁移服务器迁移是一个工具，迁移在本地和公有云工作负荷转移到 Azure。 它非常适合迁移。 Site Recovery 是灾难恢复工具。 Azure 服务器迁移和 Site Recovery 共享数据复制所用的一些常见技术组件，但满足不同目的。

## <a name="architectural-components"></a>体系结构组件

![体系结构](./media/agent-based-replication-architecture/architecture.png)

表总结了使用基于代理的迁移的组件。

组件  | **详细信息** | **安装**
--- | --- | ---
**复制设备** | 复制设备 （配置服务器） 是在本地计算机，它就像在本地环境和 Azure 迁移 Server 迁移工具之间的桥梁。 设备发现本地 VM 清单，以便 Azure 服务器迁移可以协调复制和迁移。 设备有两个组件：<br/><br/> **配置服务器**：连接到 Azure 迁移服务器迁移并协调复制。<br/> **进程服务器**：处理数据的复制。 它接收 VM 数据、 压缩和加密，并将发送到 Azure 订阅。 存在，服务器迁移到托管磁盘写入数据。 | 默认情况下与复制设备上配置服务器一起安装进程服务器。
**移动服务** | 移动服务是你想要复制和迁移每台计算机上安装的代理。 它从计算机中将复制数据发送到进程服务器中。 有多个不同的移动服务代理。 | 移动服务的安装文件位于复制设备上。 下载并安装你需要根据操作系统和版本要复制的计算机的代理。

### <a name="mobility-service-installation"></a>移动服务安装

可以使用下述方法部署移动服务：

- **推送安装**：当你为计算机启用保护时，进程服务器被安装移动服务。 
- **手动安装**：可以通过 UI 或命令提示符下的每台计算机上手动安装移动服务。

移动服务与复制设备进行通信，并复制计算机。 如果必须复制设备、 进程服务器或正在复制的计算机上运行的防病毒软件，则应从扫描中排除以下文件夹：


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent （Windows 在计算机上安装了移动服务）

## <a name="replication-process"></a>复制过程

1. 如果启用了 VM 的复制，开始初始复制到 Azure。
2. 在初始复制，移动服务从计算机磁盘读取数据并将其发送到进程服务器。
3. 使用此数据在 Azure 订阅中磁盘的副本的种子。 
4. 完成初始复制后，开始将增量更改复制到 Azure。 复制是块级别和几乎持续不断。
4. 移动服务截获写入 VM 磁盘内存，通过将与存储子系统的操作系统集成。 此方法可避免对用于增量复制的复制计算机上的磁盘 I/O 操作。 
5. 机跟踪的更改发送到 HTTPS 9443 端口上的进程服务器入站。 可以修改此端口。 进程服务器压缩和加密，并将其发送到 Azure。 

## <a name="ports"></a>端口

**设备** | **Connection**
--- | --- 
VM | 在 Vm 上运行的移动服务进行通信通过 HTTPS 443 端口上的本地复制设备的复制管理入站。<br/><br/> 虚拟机复制数据发送到 HTTPS 9443 端口上的进程服务器 （正在运行复制设备上默认情况下） 入站。 可以修改此端口。
复制设备 | 复制设备来协调通过 HTTPS 443 出站端口与 Azure 的复制。
进程服务器 | 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。


## <a name="performance-and-scaling"></a>性能和缩放

默认情况下，在部署单个复制设备的运行配置服务器和进程服务器。 如果要仅复制几台计算机，此部署就足够了。 但是，如果是复制并迁移数百台计算机，一个进程服务器可能无法处理所有的复制流量。 在这种情况下，可以部署附加的横向扩展进程服务器。

### <a name="site-recovery-deployment-planner-for-vmware"></a>VMware 的 site Recovery 部署规划器

如果要复制 VMware Vm，则可以使用[Site Recovery Deployment Planner](../site-recovery/site-recovery-deployment-planner.md)对于 VMware，以帮助确定性能要求，包括每日数据更改速率和所需的进程服务器。

### <a name="replication-appliance-capacity"></a>复制设备容量

此表中的值可以用于确定是否需要额外的进程服务器部署中。

- 如果每日更改率 （变动率） 超过 2 TB，部署额外的进程服务器。
- 如果要复制超过 200 台计算机，部署的其他复制设备。

CPU  | **内存** | **数据缓存的可用空间** | **变动率** | **复制限制**
--- | --- | --- | --- | ---
8 个 vCPU（2 个插槽 * 4 个核心 \@ 2.5 GHz） | 16 GB | 300 GB | 500 GB 或更少 | < 100 台计算机 
12 个 vCPU（2 个插槽 * 6 个核心 \@ 2.5 GHz） | 18 GB | 600 GB | 501 GB 到 1 TB | 100 台计算机。
16 个 vCPU（2 个插槽 * 8 个核心 \@ 2.5 GHz） | 32 G1 |  1 TB | 1 TB 到 2 TB | 151 200 台计算机。

### <a name="scale-out-process-server-sizing"></a>横向扩展进程服务器的大小

如果你需要部署横向扩展进程服务器，此表可以帮助您找出服务器大小调整。

**进程服务器** | **数据缓存的可用空间** | **变动率** | **复制限制**
--- | --- | --- | --- 
4 个 vCPU（2 个插槽 * 2 个核心 \@ 2.5 GHz），8 GB 内存 | 300 GB | 250 GB 或更少 | 最多 85 计算机 
8 个 vCPU（2 个插槽 * 4 个核心 \@ 2.5 GHz），12 GB 内存 | 600 GB | 251 GB 到 1 TB    | 86 台计算机。
12 个 Vcpu (2 个套接字 * 6 个核心\@2.5 g h z)，24 GB 内存 | 1 TB | 1-2 TB | 151 225 机。

## <a name="control-upload-throughput"></a>控件上传吞吐量

您可以限制用于将数据上传到 Azure，每个 HYPER-V 主机上的带宽量。 小心。 如果您将会产生负面影响的值过低，复制和延迟迁移。


1. 登录到 HYPER-V 主机或群集节点。
2. 运行**C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**，若要打开 Windows Azure Backup MMC 管理单元中。
3. 在该管理单元中，选择“更改属性”。 
4. 在中**限制**，选择**为备份操作启用 internet 带宽使用限制**。 设置工作和非工作小时数限制。 有效范围为 512 Kbps 到 1,023 Mbps。
I

### <a name="influence-upload-efficiency"></a>影响上传效率

如果你具有空闲的带宽，对于复制，并想要增加上传，则可以增加为上传任务，按如下所示分配的线程数：

1. 使用注册表编辑器中打开注册表。
2. 导航到项 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. 增加使用以便为每个复制的 VM 上传数据的线程数的值。 默认值为 4，最大值为 32。 

## <a name="next-steps"></a>后续步骤

基于代理的尝试[VMware VM 迁移](tutorial-migrate-vmware-agent.md)使用 Azure 迁移服务器迁移。
