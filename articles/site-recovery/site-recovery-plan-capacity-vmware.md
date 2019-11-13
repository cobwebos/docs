---
title: 为 VMware 灾难恢复规划容量 Azure Site Recovery
description: 本文可帮助你在使用 Azure Site Recovery 设置 VMware VM 到 Azure 的灾难恢复时规划容量和缩放。
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 467c70a722b8a243be6ac2826188a4ba3459aa06
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961359"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>规划容量和缩放以便将 VMware 灾难恢复到 Azure

本文介绍在使用 [Azure Site Recovery](site-recovery-overview.md) 将本地 VMware VM 和物理服务器复制到 Azure 时如何规划容量和缩放。

## <a name="how-do-i-start-capacity-planning"></a>如何开始容量规划？

若要了解 Azure Site Recovery 基础结构要求，请针对 VMware 复制运行 [Azure Site Recovery 部署规划器](https://aka.ms/asr-deployment-planner-doc)，以收集有关复制环境的信息。 有关详细信息，请参阅[关于用于 VMware 到 Azure 复制的 Azure Site Recovery 部署规划器](site-recovery-deployment-planner.md)。 

Site Recovery 部署规划器将提供一份报告，其中包含有关兼容和不兼容的 VM、每个 VM 的磁盘以及每个磁盘数据变动率的完整信息。 该工具还将汇总符合目标 RPO 所要满足的网络带宽要求，以及成功完成复制和测试故障转移所需的 Azure 基础结构。

## <a name="capacity-considerations"></a>容量注意事项

组件 | 详细信息
--- | ---
**复制** | **每日最大更改率**：一台受保护的计算机只能使用一个进程服务器。 单个进程服务器可以处理多达 2 TB 的每日更改率。 因此，2 TB 是受保护计算机支持的每日数据更改率上限。<br /><br /> **最大吞吐量**：在 Azure 中，一台复制的计算机可以属于一个存储帐户。 一个标准 Azure 存储帐户每秒最多可以处理 20,000 个请求。 我们建议将源计算机中的每秒输入/输出操作次数 (IOPS) 限制在 20,000。 例如，如果源计算机有 5 个磁盘，每个磁盘在源计算机上生成 120 IOPS（8 K 大小），则 Azure 中源计算机的每磁盘 IOPS 限制为 500。 （所需的存储帐户数等于源计算机总 IOPS 除以 20,000。）
**配置服务器** | 配置服务器必须能够处理受保护计算机上运行的所有工作负荷的每日更改率容量。 配置计算机必须有足够的带宽，可持续将数据复制到 Azure 存储。<br /><br /> 最佳做法是将配置服务器放置在与要保护的计算机相同的网络和 LAN 网段上。 可将配置服务器放置在不同的网络中，但要保护的计算机应可通过第 3 层网络来发现它。<br /><br /> 以下部分的表中汇总了配置服务器的建议大小。
**进程服务器** | 默认情况下，第一个进程服务器安装在配置服务器上。 可以部署更多的进程服务器以扩展环境。 <br /><br /> 进程服务器从受保护计算机接收复制数据。 进程服务器使用缓存、压缩和加密来优化数据。 然后，进程服务器将数据发送到 Azure。 进程服务器计算机必须有足够的资源来执行这些任务。<br /><br /> 进程服务器使用基于磁盘的缓存。 如果发生网络瓶颈或服务中断，可使用至少 600 GB 的独立缓存磁盘来处理存储的数据更改。

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>配置服务器和内置进程处理器的大小建议

使用内置进程服务器保护工作负荷的配置服务器可以根据以下配置最多处理 200 个虚拟机：

CPU | 内存 | 缓存磁盘大小 | 数据更改率 | 受保护的计算机
--- | --- | --- | --- | ---
8 个 vCPU（2 个插槽 * 4 个核心 \@ 2.5 GHz） | 16 GB | 300 GB | 500 GB 或更少 | 用于复制 100 台以下的计算机。
12 个 vCPU（2 个插槽 * 6 个核心 \@ 2.5 GHz） | 18 GB | 600 GB | 501 GB 到 1 TB | 用于复制 100 到 150 台计算机。
16 个 vCPU（2 个插槽 * 8 个核心 \@ 2.5 GHz） | 32 GB | 1 TB | >1 TB 到 2 TB | 用于复制 151 到 200 台计算机。
使用 [OVF 模板](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)部署另一个配置服务器。 | | | | 如果要复制 200 台以上的计算机，请部署新的配置服务器。
部署另一个[进程服务器](vmware-azure-set-up-process-server-scale.md#download-installation-file)。 | | | >2 TB| 如果每日总数据更改率超过 2 TB，请部署新的横向扩展进程服务器。

在这些配置中：

* 每台源计算机有 3 个 100 GB 的磁盘。
* 我们使用了基准测试存储来测量缓存磁盘，该存储由 8 个采用 RAID 10 配置的 10K RPM 共享访问签名驱动器组成。

## <a name="size-recommendations-for-the-process-server"></a>进程服务器的建议大小

进程服务器是处理 Azure Site Recovery 中数据复制的组件。 如果每日更改率大于 2 TB，则必须添加一个横向扩展进程服务器来处理复制负载。 若要横向扩展，可以执行以下操作：

* 增加通过 [OVF 模板](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)部署的配置服务器数目。 例如，可以使用两个配置服务器来保护最多 400 台计算机。
* 添加[横向扩展进程服务器](vmware-azure-set-up-process-server-scale.md#download-installation-file)。 使用横向扩展进程服务器处理复制流量，而无需添加配置服务器。

下表描述了此方案：

* 设置了一个横向扩展进程服务器。
* 已将受保护的虚拟机配置为使用横向扩展进程服务器。
* 每台受保护的源计算机有 3 个 100 GB 的磁盘。

其他进程服务器 | 缓存磁盘大小 | 数据更改率 | 受保护的计算机
--- | --- | --- | ---
4 个 vCPU（2 个插槽 * 2 个核心 \@ 2.5 GHz），8 GB 内存 | 300 GB | 250 GB 或更少 | 用于复制 85 台或更少的计算机。
8 个 vCPU（2 个插槽 * 4 个核心 \@ 2.5 GHz），12 GB 内存 | 600 GB | 251 GB 到 1 TB | 用于复制 86 到 150 台计算机。
12 个 vCPU（2 个插槽 * 6 个核心 \@ 2.5 GHz），24 GB 内存 | 1 TB | >1 TB 到 2 TB | 用于复制 151 到 225 台计算机。

如何缩放服务器取决于是偏好纵向扩展模型还是横向扩展模型。 若要纵向扩展，请部署一些高端配置服务器和进程服务器。 若要横向扩展，请部署具有更少资源的更多服务器。 例如，如果需要对每日总数据更改率为 1.5 TB 的 200 台计算机进行保护，可执行以下操作之一：

* 设置单个进程服务器（16 个 vCPU，24 GB RAM）。
* 设置 2 个进程服务器（2 x 8 个 vCPU，2 * 12 GB RAM）。

## <a name="control-network-bandwidth"></a>控制网络带宽

使用 [Site Recovery 部署规划器](site-recovery-deployment-planner.md)计算复制（分别为初始复制和增量复制）所需的带宽后，可以使用多个选项来控制用于复制的带宽量：

* **限制带宽**：复制到 Azure 的 VMware 流量会经过特定的进程服务器。 可以在作为进程服务器运行的计算机上限制带宽。
* **影响带宽**：可以使用几个注册表项来影响用于复制的带宽：
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** 注册表值指定用于磁盘数据传输（初始或增量复制）的线程数。 使用较大的值会增加复制所用的网络带宽。
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** 注册表值指定故障回复期间用于数据传输的线程数。

### <a name="throttle-bandwidth"></a>限制带宽

1. 在用作进程服务器的计算机上打开 Azure 备份 MMC 管理单元。 默认情况下，桌面或以下文件夹中提供了备份快捷方式： C:\Program Files\Microsoft Azure Recovery Services Agent\bin。
2. 在该管理单元中，选择“更改属性”。

    ![用于更改属性的 Azure 备份 MMC 管理单元选项的屏幕截图](./media/site-recovery-vmware-to-azure/throttle1.png)
3. 在“限制”选项卡上，选择“为备份操作启用 Internet 带宽使用限制”。 设置工作和非工作小时数限制。 有效范围为 512 Kbps 到 1,023 Mbps。

    ![“Azure 备份属性”对话框的屏幕截图](./media/site-recovery-vmware-to-azure/throttle2.png)

也可以使用 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet 来设置限制。 下面是一个示例：

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 表示不需要限制。

### <a name="alter-the-network-bandwidth-for-a-vm"></a>更改 VM 的网络带宽

1. 在 VM 的注册表中，转到 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**。
   * 若要更改复制磁盘上的带宽流量，请修改 **UploadThreadsPerVM** 的值。 如果该项不存在，请创建该项。
   * 若要更改用于从 Azure 故障回复流量的带宽，请修改 **DownloadThreadsPerVM** 的值。
2. 每个项的默认值为 **4**。 在“过度预配型”网络中，这些注册表项需要更改，不能使用默认值。 可以使用的最大值为 **32**。 监视流量以优化值。

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>将 Azure Site Recovery 基础结构设置为保护 500 个以上的 VM

在设置 Site Recovery 基础结构之前，请访问环境以测量以下因素：兼容的虚拟机、每日数据更改率、实现 RPO 所要提供的网络带宽、所需的 Site Recovery 组件数，以及完成初始复制所需的时间。 完成以下步骤以收集所需的信息：

1. 若要测量这些参数，请在环境中运行 Site Recovery 部署规划器。 如需指导帮助，请参阅[关于用于 VMware 到 Azure 复制的 Site Recovery 部署规划器](site-recovery-deployment-planner.md)。
2. 部署符合[配置服务器的大小建议](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)的配置服务器。 如果生产工作负荷超过 650 个虚拟机，请部署另一个配置服务器。
3. 根据测得的每日数据更改率，借助[大小指导](vmware-azure-set-up-process-server-scale.md#download-installation-file)部署[横向扩展进程服务器](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)。
4. 如果预期磁盘虚拟机的数据更改率超过 2 MBps，请确保使用高级托管磁盘。 Site Recovery 部署规划器运行特定的一段时间。 报告中可能不会捕获其他时间段的数据更改率峰值。
5. 请根据所要实现的 RPO [设置网络带宽](site-recovery-plan-capacity-vmware.md#control-network-bandwidth)。
6. 设置基础结构时，请为工作负荷启用灾难恢复。 有关操作方法，请参阅[为 VMware 到 Azure 的复制设置源环境](vmware-azure-set-up-source.md)。

## <a name="deploy-additional-process-servers"></a>部署额外的进程服务器

如果将部署扩展到 200 台以上的源计算机，或者每日总变动率超过 2 TB，则必须添加进程服务器来处理流量。 我们在 9.24 版本中增强了产品，以便提供有关何时设置横向扩展进程服务器的[进程服务器警报](vmware-physical-azure-monitor-process-server.md#process-server-alerts)。 [设置进程服务器](vmware-azure-set-up-process-server-scale.md)以保护新的源计算机或[均衡负载](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)。

### <a name="migrate-machines-to-use-the-new-process-server"></a>对计算机进行迁移，以使用新的进程服务器

1. 选择“设置” **“Site Recovery 服务器”。**  >  选择配置服务器，然后展开“进程服务器”。

    ![“进程服务器”对话框的屏幕截图](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. 右键单击当前正在使用的进程服务器，然后选择“切换”。

    ![“配置服务器”对话框的屏幕截图](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. 在“选择目标进程服务器”中，选择要使用的新进程服务器。 然后选择该服务器将要处理的虚拟机。 若要获取有关该服务器的信息，请选择信息图标。 为了帮助你做出负载决策，随后会显示将每个所选虚拟机复制到新进程服务器所需的平均空间。 请选择勾选标记开始复制到新的进程服务器。

## <a name="deploy-additional-master-target-servers"></a>部署其他主目标服务器

在以下情况下，需要多个主目标服务器：

*   要保护基于 Linux 的虚拟机。
*   配置服务器上的主目标服务器无法访问 VM 的数据存储。
*   主目标服务器上的磁盘总数（服务器上的本地磁盘数加上要保护的磁盘数）超过 60。

若要了解如何为基于 Linux 的虚拟机添加主目标服务器，请参阅[安装用于故障回复的 Linux 主目标服务器](vmware-azure-install-linux-master-target.md)。

为基于 Windows 的虚拟机添加主目标服务器：

1. 转到“恢复服务保管库” **“Site Recovery 基础结构”** “配置服务器” >  > 。
2. 选择所需的配置服务器，然后选择“主目标服务器”。

    ![显示“添加主目标服务器”按钮的屏幕截图](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. 下载统一安装程序文件并在 VM 上运行该文件，以安装主目标服务器。
4. 选择“安装主目标” **“下一步”**  > 。

    ![显示“安装主目标”选项的屏幕截图](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. 选择默认安装位置，然后选择“安装”。

     ![显示默认安装位置的屏幕截图](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. 若要将主目标注册到配置服务器，请选择“转到配置”。

    ![显示“转到配置”按钮的屏幕截图](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. 输入配置服务器的 IP 地址，然后输入通行短语。 若要了解如何生成通行短语，请参阅[生成配置服务器通行短语](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)。 

    ![显示配置服务器 IP 地址和通行短语输入位置的屏幕截图](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. 选择“注册”。 完成注册后，选择“完成”。

注册成功完成后，该服务器将在 Azure 门户上的“恢复服务保管库” **“Site Recovery 基础结构”** “配置服务器”中列出，在配置服务器的主目标服务器列表中可以找到它。 >  > 

 > [!NOTE]
 > 下载最新版本的[适用于 Windows 的主目标服务器统一安装程序文件](https://aka.ms/latestmobsvc)。

## <a name="next-steps"></a>后续步骤

下载并运行 [Site Recovery 部署规划器](https://aka.ms/asr-deployment-planner)。
