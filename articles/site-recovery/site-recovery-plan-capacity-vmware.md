---
title: 规划容量和缩放以便使用 Azure Site Recovery 将 VMware 灾难恢复到 Azure | Microsoft Docs
description: 请阅读本文了解在使用 Azure Site Recovery 设置 VMware VM 到 Azure 的灾难恢复时，如何规划容量和缩放
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 12/12/2018
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 6f644416a9e56009aadd0f8e1b217402d625af84
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788729"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>规划容量和缩放以便将 VMware 灾难恢复到 Azure

请阅读本文，了解如何规划容量和缩放，以便使用 [Azure Site Recovery](site-recovery-overview.md) 将本地 VMware VM 和物理服务器复制到 Azure。

## <a name="how-do-i-start-capacity-planning"></a>如何开始容量规划？

若要了解 Azure Site Recovery 基础结构要求，请针对 VMware 复制运行 [Azure Site Recovery 部署规划器](https://aka.ms/asr-deployment-planner-doc)，以收集有关复制环境的信息。 [详细了解](site-recovery-deployment-planner.md)此工具。 此工具将提供一份报告，其中包含有关兼容和不兼容的 VM、每个 VM 的磁盘以及每个磁盘数据变化率的完整信息。 该工具还将汇总符合目标 RPO 所要满足的网络带宽要求，以及成功完成复制和测试故障转移所需的 Azure 基础结构。

## <a name="capacity-considerations"></a>容量注意事项

组件 | **详细信息** |
--- | --- | ---
**复制** | **每日最大更改率：** 一台受保护的计算机只能使用一个进程服务器，一个进程服务器可处理的每日更改率最高可达 2 TB。 因此，2 TB 是受保护计算机支持的每日数据更改率上限。<br/><br/> **最大吞吐量：** 在 Azure 中，一个复制的计算机可能属于一个存储帐户。 标准存储帐户每秒最多可以处理 20,000 个请求，因此建议将源计算机中的每秒输入/输出操作数 (IOPS) 保持在 20,000。 例如，如果源计算机有 5 个磁盘，每个磁盘在源计算机上生成 120 IOPS（8K 大小），则 Azure 中单磁盘 IOPS 限制为 500。 （所需的存储帐户数等于源计算机总 IOPS 除以 20,000。）
**配置服务器** | 配置服务器应该能够处理跨所有工作负荷（运行在受保护的计算机上）的每日更改率容量，并需要有足够的带宽，才能持续将数据复制到 Azure 存储。<br/><br/> 最佳做法是，将配置服务器置于与所要保护的计算机相同的网络和 LAN 网段上。 可以将配置服务器置于另一网络中，但所要保护的计算机应可通过第 3 层网络来查看它。<br/><br/> 以下部分的表中汇总了配置服务器的建议大小。
**进程服务器** | 默认情况下，第一个进程服务器安装在配置服务器上。 可以部署更多的进程服务器以扩展环境。 <br/><br/> 进程服务器接收受保护计算机提供的复制数据，并对其通过缓存、压缩和加密进行优化。 然后它将数据发送到 Azure。 进程服务器计算机应有足够的资源来执行这些任务。<br/><br/> 进程服务器使用基于磁盘的缓存。 在出现网络瓶颈或中断时，使用单独的大小至少为 600 GB 的缓存磁盘来处理存储的数据更改。

## <a name="size-recommendations-for-the-configuration-serverin-built-process-server"></a>配置服务器/内置进程处理器的大小建议

通过 [OVF 模板](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template)部署的每个配置服务器包含一个内置的进程服务器。 利用内置的进程服务器保护虚拟机时，将以不同的比率利用配置服务器的资源，例如 CPU、内存和可用空间。 因此，利用内置的进程服务器时，要求是不相同的。
根据以下配置，使用内置进程服务器保护工作负荷的配置服务器最多可以处理 200 个虚拟机

CPU | **内存** | **缓存磁盘大小** | **数据更改率** | **受保护的计算机**
--- | --- | --- | --- | ---
8 个 vCPU（2 个插槽 * 4 个核心 \@ 2.5 千兆赫 [GHz]） | 16 GB | 300 GB | 500 GB 或更少 | 复制少于 100 台计算机。
12 个 vCPU（2 个插槽 * 6 个核心 \@ 2.5 GHz） | 18 GB | 600 GB | 500 GB 到 1 TB | 复制 100-150 台计算机。
16 个 vCPU（2 个插槽 * 8 个核心 \@ 2.5 GHz） | 32 GB | 1 TB | 1 TB 到 2 TB | 复制 150-200 台计算机。
通过[OVF 模板](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template)部署另一个配置服务器 | | | | 如果要复制 200 个以上的计算机，请部署新的配置服务器。
部署另一个[进程服务器](vmware-azure-set-up-process-server-scale.md#download-installation-file) | | | >2 TB| 如果每日总数据变动率超过 2 TB，请部署新的横向扩展进程服务器。

其中：

* 每台源计算机均配置 3 个磁盘，每个磁盘 100 GB。
* 我们使用了包含 8 个 SAS 驱动器 (10 K RPM) 的基准测试存储空间，使用 RAID 10 进行缓存磁盘度量。

## <a name="size-recommendations-for-the-configuration-server"></a>配置服务器的建议大小

如果你不打算使用配置服务器作为进程服务器，请遵循下面提供的配置，这样最多可以处理 650 个虚拟机

CPU | **RAM** | **OS 磁盘大小** | **数据更改率** | **受保护的计算机**
--- | --- | --- | --- | ---
24 个 vCPU（2 个插槽 * 12 个核心 \@ 2.5 GHz）| 32GB | 80GB | 不适用 | 最多 650 个 VM

其中，每台源计算机配置了 3 个磁盘，每个磁盘 100 GB。

由于未利用进程服务器功能，因此数据变动率不适用。 若要维持上述容量，可以遵照[此处](vmware-azure-manage-process-server.md#balance-the-load-on-process-server)的指导，将工作负荷从内置进程服务器切换到另一个横向扩展进程服务器。

## <a name="size-recommendations-for-the-process-server"></a>进程服务器的建议大小

进程服务器是处理 Azure Site Recovery 中数据复制进程的组件。 如果每日更改率大于 2 TB，则需要添加一个横向扩展进程服务器来处理复制负载。 若要横向扩展，可以执行以下操作：

* 增加通过 [OVF 模板](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template)部署的配置服务器数目。 例如，可以通过两台配置服务器来保护最多 400 台计算机。
* 添加[横向扩展进程服务器](vmware-azure-set-up-process-server-scale.md#download-installation-file)，并使用这些服务器来处理流量，而不是添加配置服务器。

下表介绍了一种方案，在该方案中：

* 已设置横向扩展进程服务器。
* 已将受保护的虚拟机配置为使用横向扩展进程服务器。
* 每台受保护的源计算机配置为了三个磁盘，每个磁盘 100 GB。

**额外的进程服务器** | **缓存磁盘大小** | **数据更改率** | **受保护的计算机**
--- | --- | --- | ---
4 个 vCPU（2 个插槽 * 2 个核心 \@ 2.5 GHz），8 GB 内存 | 300 GB | 250 GB 或更少 | 复制 85 台或更少的计算机。
8 个 vCPU（2 个插槽 * 4 个核心 \@ 2.5 GHz），12 GB 内存 | 600 GB | 250 GB 到 1 TB | 复制 85-150 台计算机。
12 个 vCPU（2 个插槽 * 6 个核心 \@ 2.5 GHz），24 GB 内存 | 1 TB | 1 TB 到 2 TB | 复制 150-225 台计算机。

使用哪种方式来扩展服务器取决于是偏好纵向扩展模型还是横向扩展模型。  纵向扩展时，需要部署一些高端配置服务器和进程服务器，而横向扩展时，需要使用更少资源部署更多服务器。 例如，如果需要对每日总数据更改率为 1.5 TB 的 200 台计算机进行保护，可执行以下某项操作：

* 设置具有 16 个 vCPU、24 GB RAM 的单个进程服务器。
* 设置 2 个进程服务器（2 x 8 个 vCPU，2 * 12 GB RAM）。

## <a name="control-network-bandwidth"></a>控制网络带宽

使用[部署计划器工具](site-recovery-deployment-planner.md)计算复制（初始复制和增量复制）所需的带宽后，可以使用下列几个选项来控制用于复制的带宽量：

* **限制带宽**：复制到 Azure 的 VMware 流量会经过特定的进程服务器。 可以在运行进程服务器的计算机上限制带宽。
* **控制带宽**：可以使用几个注册表项来控制用于复制的带宽：
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** 注册表值指定用于磁盘数据传输（初始或增量复制）的线程数。 使用较大的值会增加复制所用的网络带宽。
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** 指定故障回复期间用于数据传输的线程数。

### <a name="throttle-bandwidth"></a>限制带宽

1. 在作为进程服务器的计算机上打开 Azure 备份 MMC 管理单元。 默认情况下，备份的快捷方式位于桌面上或在以下文件夹中：C:\Program Files\Microsoft Azure Recovery Services Agent\bin。
2. 在管理单元中，单击“更改属性”。

    ![Azure 备份 MMC 管理单元选项（用于更改属性）的屏幕截图](./media/site-recovery-vmware-to-azure/throttle1.png)
3. 在“限制”选项卡上，选择“为备份操作启用 Internet 带宽使用限制”。 设置工作和非工作小时数限制。 有效范围为 512 Kbps 到 1023 Mbps。

    ![“Azure 备份属性”对话框的屏幕截图](./media/site-recovery-vmware-to-azure/throttle2.png)

也可以使用 [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) cmdlet 来设置限制。 下面是一个示例：

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** 表示不需要限制。

### <a name="influence-network-bandwidth-for-a-vm"></a>影响 VM 的网络带宽

1. 在 VM 的注册表中导航到 **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsofte Azure Backup\Replication**。
   * 若要影响复制磁盘上的带宽流量，请修改 **UploadThreadsPerVM** 的值，或者创建该项（如果不存在）。
   * 若要影响用于从 Azure 故障回复流量的带宽，请修改 **DownloadThreadsPerVM** 的值。
2. 默认值为 4。 在“过度预配型”网络中，这些注册表项需要更改，不能使用默认值。 最大值为 32。 监视流量以优化值。

## <a name="setup-azure-site-recovery-infrastructure-to-protect-more-than-500-virtual-machines"></a>将 Azure Site Recovery 基础结构设置为保护 500 个以上的虚拟机

在设置 Azure Site Recovery 基础结构之前，需要访问环境以测量以下因素：兼容的虚拟机、每日数据更改率、满足所需 RPO 的网络带宽、所需的 Azure Site Recovery 组件数、完成初始复制所需的时间。等等，

1. 若要测量这些参数，请务必借助[此处](site-recovery-deployment-planner.md)所述的指导，在环境中运行部署规划器。
2. 根据[此处](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server)所述的要求部署配置服务器。 如果生产工作负荷超过 650 个虚拟机，请部署另一个配置服务器。
3. 基于测得的每日数据更改率，借助[此处](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)所述的大小指导部署[横向扩展进程服务器](vmware-azure-set-up-process-server-scale.md#download-installation-file)。
4. 如果预期磁盘虚拟机的数据更改率超过 2 MBps，请务必[设置高级存储帐户](tutorial-prepare-azure.md#create-a-storage-account)。 由于部署规划器只会运行特定的一段时间，报告中可能不会捕获其他时间段的数据更改率峰值。
5. 根据所需的 RPO [设置网络带宽](site-recovery-plan-capacity-vmware.md#control-network-bandwidth)。
6. 设置基础结构后，遵照[操作方法部分](vmware-azure-set-up-source.md)发布的指导对工作负荷启用灾难恢复。

## <a name="deploy-additional-process-servers"></a>部署附加的进程服务器

如果必须将部署扩大到 200 台以上源计算机，或者每日总改动率超过 2 TB，则需要额外的进程服务器来处理流量。 按照[本文](vmware-azure-set-up-process-server-scale.md)所述说明设置进程服务器。 设置服务器后，可以迁移源计算机来使用它。

### <a name="migrate-machines-to-use-the-new-process-server"></a>对计算机进行迁移，以使用新的进程服务器

1. 在“设置” > “Site Recovery 服务器”中，单击配置服务器，并展开“进程服务器”。

    ![“进程服务器”对话框的屏幕截图](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. 右键单击当前正在使用的进程服务器，并单击“切换”。

    ![“配置服务器”对话框的屏幕截图](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. 在“选择目标进程服务器”中，选择要使用的新进程服务器，然后选择该服务器将要处理的虚拟机。 单击信息图标，获取服务器的相关信息。 为了帮助你做出负载决策，随后会显示将每个所选虚拟机复制到新进程服务器所需的平均空间。 单击复选标记，开始复制到新的进程服务器。

## <a name="deploy-additional-master-target-servers"></a>部署其他主目标服务器

在以下方案中将需要其他主目标服务器

1. 如果尝试保护基于 Linux 的虚拟机。
2. 如果配置服务器上可用的主目标服务器不能访问 VM 的数据存储。
3. 如果主目标服务器上的磁盘总数（服务器上的 本地磁盘数 + 要保护的磁盘数）超过 60。

若要为基于 Linux 的虚拟机添加新的主目标服务器，请[单击此处](vmware-azure-install-linux-master-target.md)。

对于基于 Windows 的虚拟机，请按照以下说明进行操作。

1. 导航到“恢复服务保管库” > “Site Recovery 基础结构” > “配置服务器”。
2. 单击所需的“配置服务器”>“+主目标服务器”。![add-master-target-server.png](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. 下载统一设置并在 VM 上运行，以设置主目标服务器。
4. 选择“安装主目标服务器” > “下一步”。 ![choose-MT.PNG](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. 选择默认安装位置 >单击“安装”。 ![MT-installation](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. 单击“转到配置”向配置服务器注册主目标服务器。 ![MT-proceed-configuration.PNG](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. 输入配置服务器的 IP 地址和密码。 [单击此处](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)了解如何生成密码。![cs-ip-passphrase](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. 单击“注册”，并在注册后单击“完成”。
9. 注册成功后，此服务器在“恢复服务保管库” > “Site Recovery 基础结构” > “配置服务器”>“相关配置服务器的主目标服务器”下的门户上列出。

 >[!NOTE]
 >还可在[此处](https://aka.ms/latestmobsvc)下载适用于 Windows 的最新版本的主目标服务器统一设置。

## <a name="next-steps"></a>后续步骤

下载并运行 [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner)
