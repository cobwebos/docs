---
title: "规划容量和缩放为 VMware 复制到与 Azure Site Recovery 的 Azure |Microsoft 文档"
description: "使用计划容量和缩放这篇文章时复制到与 Azure Site Recovery 的 Azure 的 VMware 虚拟机"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/24/2017
ms.author: rayne
ms.openlocfilehash: 8b580ac239bfb6d7b633fb03d4cfb91b168b0610
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="plan-capacity-and-scaling-for-vmware-replication-with-azure-site-recovery"></a>规划容量和缩放为与 Azure Site Recovery 的 VMware 复制

使用本文章以了解规划容量和缩放，将在本地 VMware 虚拟机和物理服务器复制到具有 Azure 时[Azure Site Recovery](site-recovery-overview.md)。

## <a name="how-do-i-start-capacity-planning"></a>如何启动容量规划？

通过运行收集有关您的复制环境的信息[Azure 站点恢复部署规划](https://aka.ms/asr-deployment-planner-doc)VMware 复制。 [了解](site-recovery-deployment-planner.md)有关此工具。 将收集有关兼容和不兼容的 Vm，每个虚拟机，磁盘的信息和数据改动每个磁盘。 该工具还介绍了网络带宽需求和所需的成功进行复制和测试故障转移的 Azure 基础结构。

## <a name="capacity-considerations"></a>容量注意事项

**组件** | **详细信息** |
--- | --- | ---
**复制** | **最大每日的更改率：**受保护的计算机只能使用一个进程服务器，并且单个进程服务器可以处理每天更改速率最多为 2 TB。 因此 2 TB 是最大每日数据更改受保护的计算机支持的速率。<br/><br/> **最大吞吐量：**已复制的计算机可以属于 Azure 中的一个存储帐户。 标准存储帐户可以处理最多 20,000 每秒的请求，并且我们建议你保持每秒 (IOPS) 的输入/输出操作的数目跨源计算机为 20,000。 例如，如果你有具有 5 个磁盘，源计算机，并且每个磁盘生成源计算机上的 120 IOPS （8k 大小），则它将在每个磁盘 500 的 IOPS 限制 Azure 中。 （所需的存储帐户数等于总源计算机 IOPS，除以 20000。）
**配置服务器** | 配置服务器应该能够处理所有工作负荷中受保护计算机上运行的每日的更改速率容量，并且需要足够的带宽来持续将数据复制到 Azure 存储空间。<br/><br/> 作为最佳做法是，你想要保护的计算机中找到的相同网络和 LAN 网段上的配置服务器。 位于不同的网络，但你想要保护的计算机应具有对它的层 3 网络可见性。<br/><br/> 下列部分中表中总结了配置服务器的大小建议。
**进程服务器** | 默认情况下，配置服务器上安装了第一个进程服务器。 你可以部署附加的进程服务器以扩展你的环境。 <br/><br/> 进程服务器接收来自受保护计算机的复制数据和与缓存、 压缩和加密数据进行优化。 然后它将数据发送到 Azure。 进程服务器计算机都应有足够的资源来执行这些任务。<br/><br/> 处理服务器使用基于磁盘的缓存。 使用 600 GB 或更多的单独的缓存磁盘来处理存储在发生网络瓶颈或中断时的数据更改。

## <a name="size-recommendations-for-the-configuration-server"></a>配置服务器的大小建议

**CPU** | **内存** | **缓存磁盘大小** | **数据更改率** | **受保护的计算机**
--- | --- | --- | --- | ---
8 Vcpu (2 套接字 * @ 2.5 千兆赫 [GHz] 的 4 核) | 16 GB | 300 GB | 500 GB 或更少 | 少于 100 个机复制。
12 Vcpu (2 套接字 * @ 2.5 GHz 6 个核) | 18 GB | 600 GB | 500 GB 到 1 TB | 100 150 机之间复制。
16 Vcpu (2 套接字 * 8 核 @ 2.5 GHz) | 32 GB | 1 TB | 到 2 TB 的 1 TB | 150-200 机之间复制。
部署另一个进程服务器 | | | > 2 TB | 如果要复制 200 多个计算机，或者如果的每日数据更改率超过 2 TB，则部署其他进程服务器。

其中：

* 每个源计算机配置为使用 3 个磁盘的每个 100 GB。
* 我们与 RAID 10 中使用的 10k RPM，8 SAS 驱动器的基准测试存储缓存磁盘制度量单位。

## <a name="size-recommendations-for-the-process-server"></a>进程服务器的大小建议

如果你需要保护超过 200 个机，或每日的更改率大于 2 TB，你可以添加进程服务器来处理复制负载。 若要向外扩展，您可以：

* 增加的配置服务器的数量。 例如，你可以保护最多具有两个配置服务器的 400 计算机。
* 添加更多的进程服务器，并使用它们来处理流量，而不是 （或除了） 配置服务器。

下表描述了在其中方案：

* 你不打算使用的进程服务器的配置服务器。
* 你已设置的其他进程服务器。
* 你已配置要使用其他进程服务器的受保护的虚拟机。
* 每个受保护的源计算机配置为使用每个 100 GB 的三个磁盘。

**配置服务器** | **其他进程服务器** | **缓存磁盘大小** | **数据更改率** | **受保护的计算机**
--- | --- | --- | --- | ---
8 Vcpu (2 套接字 * @ 2.5 GHz 4 核)，16 GB 内存 | 4 Vcpu (2 套接字 * @ 2.5 GHz 双核)，8 GB 内存 | 300 GB | 250 GB 或更少 | 将 85 或更少的计算机的复制。
8 Vcpu (2 套接字 * @ 2.5 GHz 4 核)，16 GB 内存 | 8 Vcpu (2 套接字 * @ 2.5 GHz 4 核)，12 GB 内存 | 600 GB | 250 GB 到 1 TB | 85 150 机之间复制。
12 Vcpu (2 套接字 * 6 核 @ 2.5 g h z)，18 GB 内存 | 12 Vcpu (2 套接字 * 6 核 @ 2.5 GHz) 24 GB 内存 | 1 TB | 到 2 TB 的 1 TB | 150 225 机之间复制。

在其中缩放你的服务器的方式取决于你的向上缩放或向外扩展模型的首选项。  通过部署几个高端配置和进程服务器，来向上扩展，或通过部署的资源较少的更多服务器向外扩展。 例如，如果你需要保护 220 机，你无法执行以下任一操作：

* 设置 12 vCPU，18 GB 内存，配置服务器和 12 vCPU，24 GB 的内存的其他进程服务器。 配置要使用仅在其他进程服务器的受保护的计算机。
* 设置两个配置服务器 （2 x 8 vCPU、 16 GB RAM） 和两个额外的进程服务器 （1x8 vCPU 和 4 vCPU x 1 到句柄 135 + 85 [220] 机）。 配置要使用的其他进程服务器的受保护的计算机。


## <a name="control-network-bandwidth"></a>控制网络带宽

你已使用后[部署规划器工具](site-recovery-deployment-planner.md)若要计算复制 （初始复制，然后增量） 所需的带宽，你可以控制使用几个选项进行复制所使用的带宽量：

* **限制带宽**： 将复制到 Azure 的 VMware 流量将经历特定的进程服务器。 你可以限制在作为进程服务器运行的计算机上的带宽。
* **影响带宽**： 您可以使用几个注册表项的用于复制的带宽来影响：
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\UploadThreadsPerVM**注册表值指定用于磁盘的数据传输 （初始或增量复制） 的线程数。 较高的值会增加用于复制的网络带宽。
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DownloadThreadsPerVM**指定的故障回复期间用于数据传输的线程数。

### <a name="throttle-bandwidth"></a>限制带宽

1. 充当进程服务器的计算机上打开 Azure Backup MMC 管理单元。 默认情况下，备份的快捷方式也不能在桌面上，或在以下文件夹： C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin。
2. 在管理单元中，单击**更改属性**。

    ![Azure 备份 MMC 的屏幕截图管理单元中选项，若要更改属性](./media/site-recovery-vmware-to-azure/throttle1.png)
3. 上**限制**选项卡上，选择**为备份操作启用 internet 带宽使用限制**。 设置工作的限制和非工作小时数。 有效范围是从 512 Kbps 到每秒 102 Mbps。

    ![Azure 备份属性的屏幕截图对话框](./media/site-recovery-vmware-to-azure/throttle2.png)

你还可以使用[Set-obmachinesetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet 设置限制。 下面是一个示例：

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**设置 OBMachineSetting NoThrottle**指示是否需要对没有限制。

### <a name="influence-network-bandwidth-for-a-vm"></a>VM 的的影响网络带宽

1. 在 VM 的注册表中，导航到**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**。
   * 若要影响复制的磁盘上的带宽流量，修改的值**UploadThreadsPerVM**，或者创建密钥，如果它不存在。
   * 若要影响从 Azure 故障回复流量的带宽，修改的值**DownloadThreadsPerVM**。
2. 默认值为 4。 在"预配过度"的网络中，应从默认值更改这些注册表项。 最大值为 32。 监视流量来优化值。


## <a name="deploy-additional-process-servers"></a>部署其他进程服务器

如果你有缩放出你的部署超出 200 源计算机，或每日改动率超过 2 TB 总共有，你需要额外的进程服务器来处理流量。 按照这些说明来设置进程服务器。 设置服务器之后, 你将迁移源计算机以使用它。

1. 在**Site Recovery 服务器**，单击配置服务器，然后单击**进程服务器**。

    ![屏幕快照的 Site Recovery 服务器选项以添加进程服务器](./media/site-recovery-vmware-to-azure/migrate-ps1.png)
2. 在**服务器类型**，单击**进程服务器 （本地）**。

    ![屏幕快照的进程服务器对话框](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
3. 下载 Site Recovery 统一安装程序文件，并运行该程序安装进程服务器。 这还将其注册到保管库中。
4. 在**在开始之前**，选择**添加要向外扩展部署的其他进程服务器**。
5. 完成该向导在相同的方式时你[设置](#step-2-set-up-the-source-environment)配置服务器。

    ![屏幕快照的 Azure Site Recovery 统一安装程序向导](./media/site-recovery-vmware-to-azure/add-ps1.png)
6. 在**配置服务器详细信息**，指定的 IP 地址的配置服务器，以及的密码。 若要获取通行短语，运行**[SiteRecoveryInstallationFolder]\home\sysystems\bin\genpassphrase.exe – n**配置服务器上。

    ![屏幕快照的配置服务器详细信息页](./media/site-recovery-vmware-to-azure/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>迁移要使用新进程服务器的计算机
1. 在**设置** > **Site Recovery 服务器**，单击配置服务器，然后展开**处理服务器**。

    ![屏幕快照的进程服务器对话框](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. 右键单击进程服务器当前正在使用，然后单击**交换机**。

    ![屏幕快照的配置服务器对话框](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. 在**选择目标进程服务器**，选择你想要使用，新进程服务器，然后选择服务器将处理虚拟机。 单击以获取有关服务器的信息的信息图标。 为了帮助你做出加载决策，将显示具有所需将每个所选的虚拟机复制到新进程服务器的平均空间。 单击复选标记以开始复制到新的进程服务器。


## <a name="next-steps"></a>后续步骤

下载并运行[Azure 站点恢复部署计划程序](https://aka.ms/asr-deployment-planner)
