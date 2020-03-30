---
title: Azure 迁移服务器迁移中基于代理的迁移
description: 提供 Azure 迁移中基于代理的 VMware VM 迁移的概述。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: raynew
ms.openlocfilehash: d345d707cbf58f48466c3bd830d93250d13397c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425845"
---
# <a name="agent-based-migration-architecture"></a>基于代理的迁移体系结构

本文概述了使用[Azure 迁移：服务器迁移](migrate-services-overview.md#azure-migrate-server-assessment-tool)工具基于代理复制 VMware VM 的体系结构和过程。

使用 Azure 迁移：服务器迁移，可以使用几个选项复制 VMware VM：

- 使用基于代理的复制迁移 VM，如本文所述。
- 使用无代理复制迁移 VMware VM。 这将迁移 VM，而无需在 VM 上安装任何内容。

详细了解如何为 VMware VM[选择和比较](server-migrate-overview.md)迁移方法。 


## <a name="agent-based-migration"></a>基于代理的迁移

基于代理的迁移用于将本地 VMware VM 和物理服务器迁移到 Azure。 它还可用于迁移其他本地虚拟化服务器，以及私有和公共云 VM，包括 AWS 实例和 GCP VM。 Azure 迁移中基于代理的迁移使用[Azure 站点恢复](../site-recovery/site-recovery-overview.md)服务中的一些后端功能。


## <a name="architectural-components"></a>体系结构组件

该图说明了基于代理的迁移所涉及的组件。

![体系结构](./media/agent-based-replication-architecture/architecture.png)

该表总结了用于基于代理的迁移的组件。

**组件** | **详细信息** | **安装**
--- | --- | ---
**复制设备** | 复制设备（配置服务器/进程服务器）是一台本地计算机，充当本地环境和服务器迁移之间的桥梁。 设备发现本地计算机清单，以便服务器迁移可以协调复制和迁移。 产品有两个组件：<br/><br/> **配置服务器**：连接到服务器迁移并协调复制。<br/> **进程服务器**：处理数据复制。 进程服务器接收计算机数据、压缩和加密计算机数据并将其发送到 Azure。 在 Azure 中，服务器迁移将数据写入托管磁盘。 | 默认情况下，进程服务器与复制设备上的配置服务器一起安装。
**移动服务** | 移动服务是安装在要复制和迁移的每台计算机上的代理。 它将复制数据从计算机发送到进程服务器。 | 不同版本的移动服务的安装文件位于复制设备上。 您可以根据要复制的计算机的操作系统和版本下载并安装所需的代理。

## <a name="mobility-service-installation"></a>移动服务安装

可以使用下述方法部署移动服务：

- **推送安装**：当您启用计算机保护时，进程服务器将安装移动服务。 
- **手动安装**：您可以通过 UI 或命令提示符在每台计算机上手动安装移动服务。

移动服务与复制设备和复制的计算机进行通信。 如果在复制设备、进程服务器或要复制的计算机上运行防病毒软件，则应从扫描中排除以下文件夹：


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86)\Microsoft Azure Site Recovery
- C：\程序数据\ASR\代理（在安装了移动服务的 Windows 计算机上）

## <a name="replication-process"></a>复制过程

1. 为计算机启用复制时，将开始初始复制到 Azure。
2. 在初始复制期间，移动服务从计算机磁盘读取数据，并将其发送到进程服务器。
3. 此数据用于在 Azure 订阅中设置磁盘副本的种子。 
4. 完成初始复制后，开始将增量更改复制到 Azure。 复制是块级和几乎连续的。
4. 移动服务通过与操作系统的存储子系统集成来拦截写入磁盘内存。 此方法避免复制计算机上的磁盘 I/O 操作，以便进行增量复制。 
5. 计算机的跟踪更改将发送到入站端口 HTTPS 9443 上的进程服务器。 可以修改此端口。 进程服务器压缩并加密它，并将其发送到 Azure。 

## <a name="ports"></a>端口

**设备** | **连接**
--- | --- 
**复制机器** | 在 VM 上运行的移动服务与端口 HTTPS 443 入站上的本地复制设备进行通信，以便进行复制管理。<br/><br/> 计算机将复制数据发送到端口 HTTPS 9443 入站上的进程服务器。 可以修改此端口。
**复制设备** | 复制设备通过端口 HTTPS 443 出站协调使用 Azure 进行复制。
**进程服务器** | 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。


## <a name="performance-and-scaling"></a>性能和缩放

默认情况下，部署同时运行配置服务器和进程服务器的单个复制设备。 如果只复制几台计算机，则此部署就足够了。 但是，如果要复制和迁移数百台计算机，则单个进程服务器可能无法处理所有复制流量。 在这种情况下，您可以部署其他横向扩展进程服务器。

### <a name="plan-vmware-deployment"></a>规划 VMware 部署

如果要复制 VMware VM，可以使用[VMware 的站点恢复部署规划器](../site-recovery/site-recovery-deployment-planner.md)来帮助确定性能要求，包括每日数据更改率以及所需的进程服务器。

### <a name="replication-appliance-capacity"></a>复制设备容量

使用此表中的值确定部署中是否需要其他进程服务器。

- 如果每日更改率（流失率）超过 2 TB，请部署其他进程服务器。
- 如果要复制 200 多台计算机，请部署其他复制设备。

CPU**** | **内存** | **自由空间数据缓存** | **变动率** | **复制限制**
--- | --- | --- | --- | ---
8 个 vCPU（2 个插槽 * 4 个核心 \@ 2.5 GHz） | 16 GB | 300 GB | 500 GB 或更少 | < 100 台计算机 
12 个 vCPU（2 个插槽 * 6 个核心 \@ 2.5 GHz） | 18 GB | 600 GB | 501 GB 到 1 TB | 100-150台机器。
16 个 vCPU（2 个插槽 * 8 个核心 \@ 2.5 GHz） | 32 G1 |  1 TB | 1 TB 到 2 TB | 151-200台机器。

### <a name="sizing-scale-out-process-servers"></a>调整横向扩展进程服务器的规模

如果需要部署横向扩展进程服务器，请使用此表确定服务器大小调整。

**进程服务器** | **数据缓存的可用空间** | **变动率** | **复制限制**
--- | --- | --- | --- 
4 个 vCPU（2 个插槽 * 2 个核心 \@ 2.5 GHz），8 GB 内存 | 300 GB | 250 GB 或更少 | 多达 85 台机器 
8 个 vCPU（2 个插槽 * 4 个核心 \@ 2.5 GHz），12 GB 内存 | 600 GB | 251 GB 到 1 TB    | 86-150台机器。
12 个 vCPU（2 个插槽\@+ 6 个内核 2.5 GHz），24 GB 内存 | 1 TB | 1-2 TB | 151-225台机器。

## <a name="throttle-upload-bandwidth"></a>限制上传带宽。

复制到 Azure 的 VMware 流量会经过特定的进程服务器。 您可以通过限制作为进程服务器运行的计算机上带宽来限制上载吞吐量。 您可以使用此注册表项影响带宽：

- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM 注册表值指定用于磁盘数据传输（初始或增量复制）的线程数。 使用较大的值会增加复制所用的网络带宽。 默认值为 4。 最大值为 32。 监视流量以优化值。
- 此外，您可以限制进程服务器计算机上的带宽，如下所示：

    1. 在进程服务器计算机上，打开 Azure 备份 MMC 管理单元。 桌面或文件夹 C：程序文件\Microsoft Azure 恢复服务代理\bin 上有一个快捷方式。 
    2. 在该管理单元中，选择“更改属性”。****
    3. 在 **"限制"中**，选择**启用备份操作的互联网带宽使用限制**。 设置工作和非工作小时数限制。 有效范围为 512 Kbps 到 1,023 Mbps。


## <a name="next-steps"></a>后续步骤

尝试[VMware](tutorial-migrate-vmware-agent.md)或[物理服务器](tutorial-migrate-physical-virtual-machines.md)[基于代理的迁移](tutorial-migrate-vmware-agent.md)。
