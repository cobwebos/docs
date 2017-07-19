---
title: "用于在 Azure 之间进行复制的 Azure Site Recovery 支持矩阵 | Microsoft Docs"
description: "总结了 Azure Site Recovery 在不同区域之间复制 Azure 虚拟机 (VM) 以满足灾难恢复 (DR) 需求时支持的操作系统和配置。"
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/10/2017
ms.author: sujayt
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 7c30f5164b9fe7ff6044bbf23767a5db9a0f7c30
ms.contentlocale: zh-cn
ms.lasthandoff: 06/15/2017


---
# <a name="azure-site-recovery-support-matrix-for-replicating-from-azure-to-azure"></a>用于在 Azure 之间进行复制的 Azure Site Recovery 支持矩阵


>[!NOTE]
>
> 用于 Azure 虚拟机的 Site Recovery 复制当前处于预览状态。

本文总结了 Azure Site Recovery 在不同区域之间复制和恢复 Azure 虚拟机时支持的配置和组件。

## <a name="user-interface-options"></a>用户界面选项

**用户界面** |  **支持/不支持**
--- | ---
**Azure 门户** | 支持
**经典门户** | 不支持
**PowerShell** | 目前不支持
**REST API** | 目前不支持
**CLI** | 目前不支持


## <a name="resource-move-support"></a>资源移动支持

**资源移动类型** | **支持/不支持** | **备注**  
--- | --- | ---
**跨资源组移动保管库** | 不支持 |不能跨资源组移动恢复服务保管库。
**跨资源组移动计算、存储和网络** | 不支持 |如果在启用复制后移动虚拟机（或其相关组件，如存储和网络），则需为虚拟机禁用复制后重新启用复制。


## <a name="support-for-deployment-models"></a>部署模型支持

**部署模型** | **支持/不支持** | **备注**  
--- | --- | ---
**经典** | 支持 | 只能在复制经典虚拟机后将其恢复为经典虚拟机。 不能将其恢复为 Resource Manager 虚拟机。 如果直接向 Azure 区域部署不带虚拟网络的经典 VM，将不受支持。
**Resource Manager** | 支持 |

## <a name="support-for-replicated-machine-os-versions"></a>已复制的计算机操作系统版本支持

以下支持适用于在所提及的 OS 上运行的所有工作负荷。

#### <a name="windows"></a>Windows

- 64 位 Windows Server 2012 R2
- Windows Server 2012
- 带 SP1（或更高版本）的 Windows Server 2008 R2

#### <a name="linux"></a>Linux

- Red Hat Enterprise Linux 6.7、6.8、7.1、7.2、7.3
- CentOS 6.5、6.6、6.7、6.8、7.0、7.1、7.2、7.3
- Oracle Enterprise Linux 6.4、6.5（运行 Red Hat 兼容内核或 Unbreakable Enterprise Kernel Release 3 (UEK3)）
- SUSE Linux Enterprise Server 11 SP3

## <a name="supported-file-systems-and-guest-storage-configurations-on-azure-virtual-machines-running-linux-os"></a>运行 Linux OS 的 Azure 虚拟机上支持的文件系统和来宾存储配置

* 文件系统：ext3、ext4、ReiserFS（仅限 Suse Linux Enterprise Server）和 XFS
* 卷管理器：LVM2
* 多路径软件：设备映射程序

## <a name="region-support"></a>区域支持

可以在同一地理群集中的任意两个区域之间复制和恢复 VM。

**地理群集** | **Azure 区域**
-- | --
美洲 | 加拿大东部、加拿大中部、美国中南部、美国中西部、美国东部、美国东部 2、美国西部、美国西部 2、美国中部、美国中北部
欧洲 | 英国西部、英国南部、北欧、西欧
亚洲 | 印度南部、印度中部、东南亚、东亚、日本东部、日本西部
澳大利亚   | 澳大利亚东部、澳大利亚东南部

>[!NOTE]
>
> 对于巴西南部区域，只能复制和故障转移到以下区域之一，然后进行故障回复：美国中南部、美国中西部、美国东部、美国东部 2、美国西部、美国西部 2 和美国中北部。


## <a name="support-for-compute-configuration"></a>计算配置支持

**配置** | **支持/不支持** | **备注**
--- | --- | ---
大小 | 具有至少 2 个 CPU 内核和 1 GB RAM 的任意 Azure VM 大小 | 请参阅 [Azure 虚拟机大小](../virtual-machines/windows/sizes.md)
可用性集 | 支持 | 如果在门户中执行“启用复制”步骤时使用默认选项，系统会基于源区域配置自动创建可用性集。 可以随时在“复制的项”>“设置”>“计算和网络”>“可用性集”中更改目标可用性集。
混合使用权益 (HUB) VM | 支持 | 如果源 VM 启用了 HUB 许可证，则测试故障转移或故障转移 VM 也使用 HUB 许可证。
虚拟机规模集 | 不支持 |
Azure 库映像 — 由 Microsoft 发布 | 支持 | 只要 VM 在 Site Recovery 支持的操作系统上运行，便支持该配置
Azure 库映像 — 由第三方发布 | 支持 | 只要 VM 在 Site Recovery 支持的操作系统上运行，便支持该配置。
自定义映像 — 由第三方发布 | 支持 | 只要 VM 在 Site Recovery 支持的操作系统上运行，便支持该配置。
使用 Site Recovery 迁移的 VM | 支持 | 如果是使用 Site Recovery 迁移到 Azure 中的 VMware/物理计算机，则需要卸载较旧版本的移动服务并重新启动计算机，然后再将该计算机复制到另一个 Azure 区域。

## <a name="support-for-storage-configuration"></a>存储配置支持

**配置** | **支持/不支持** | **备注**
--- | --- | ---
最大 OS 磁盘大小 | Azure 支持的最大 OS 磁盘大小| 请参阅 [VM 使用的磁盘](../storage/storage-about-disks-and-vhds-windows.md#disks-used-by-vms)。
最大数据磁盘大小 | Azure 支持的最大数据磁盘大小| 请参阅 [VM 使用的磁盘](../storage/storage-about-disks-and-vhds-windows.md#disks-used-by-vms)。
数据磁盘数 | 特定 Azure VM 大小最多支持 64 个 | 请参阅 [Azure 虚拟机大小](../virtual-machines/windows/sizes.md)
临时磁盘 | 始终从复制中排除 | 临时磁盘始终从复制中排除。 按照 Azure 指南，不能将任何永久数据放在临时磁盘上。 有关更多详细信息，请参阅 [Azure VM 上的临时磁盘](../storage/storage-about-disks-and-vhds-windows.md#temporary-disk)。
磁盘上的数据更改率 | 每个磁盘最大 6 Mbps | 如果磁盘上的平均数据更改率一直超过 6 Mbps，复制将不同步。 但是，如果只是偶尔出现数据迸发，数据更改率间或高于 6 Mbps，但随后又降下来，则复制可同步。 在此情况下，可能会看到恢复点稍有延迟。
标准存储帐户上的磁盘 | 支持 |
高级存储帐户上的磁盘 | 支持 | 如果 VM 将磁盘分散在高级和标准存储帐户上，则可以为每个磁盘选择不同的目标存储帐户，以确保在目标区域中具有相同的存储配置
标准托管磁盘 | 不支持 |  
高级托管磁盘 | 不支持 |
存储空间 | 不支持 |         
静态加密 (SSE) | 支持 | 对于缓存和目标存储帐户，可以选择启用了 SSE 的存储帐户。     
Azure 磁盘加密 (ADE) | 不支持 |
热添加/移除磁盘 | 不支持 | 如果在 VM 上添加或删除数据磁盘，则需为 VM 禁用复制后重新启用复制。
排除磁盘 | 不支持|   默认排除临时磁盘。
LRS | 支持 |
GRS | 支持 |
RA-GRS | 支持 |
ZRS | 支持 |  
冷存储和热存储 | 不支持 | 冷存储和热存储不支持虚拟机磁盘

>[!IMPORTANT]
> 确保按照[存储指南](../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)配置源 Azure 虚拟机，以免产生任何性能问题。 如果遵从默认设置，Site Recovery 将基于源配置创建所需的存储帐户。 如果自定义和选择自己的设置，请确保按照 (../storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks) 配置源 VM。

## <a name="support-for-network-configuration"></a>网络配置支持
**配置** | **支持/不支持** | **备注**
--- | --- | ---
网络接口 (NIC) | 特定 Azure VM 大小支持最大数量的 NIC | 在测试故障转移或故障转移操作期间创建 VM 时，会创建 NIC。 故障转移 VM 上的 NIC 数目取决于启用复制时源 VM 上的 NIC 数目。 如果在启用复制后添加/删除 NIC，不会影响故障转移 VM 上的 NIC 计数。
内部负载均衡器 | 支持 | 需要使用恢复计划中的 azure 自动化脚本关联预配置的负载均衡器。
内部负载均衡器 | 支持 | 需要使用恢复计划中的 azure 自动化脚本关联预配置的负载均衡器。
公共 IP| 支持 | 需要将已有的公共 IP 关联到 NIC，或者使用恢复计划中的 azure 自动化脚本创建一个公共 IP 并将其关联到 NIC。
NIC 上的 NSG (Resource Manager)| 支持 | 需要使用恢复计划中的 azure 自动化脚本将 NSG 关联到 NIC。  
子网上的 NSG（Resource Manager 和经典）| 支持 | 需要使用恢复计划中的 azure 自动化脚本将 NSG 关联到 NIC。
VM 上的 NSG（经典）| 支持 | 需要使用恢复计划中的 azure 自动化脚本将 NSG 关联到 NIC。
保留 IP（静态 IP）/保留源 IP | 支持 | 如果源 VM 上的 NIC 具有静态 IP 配置，并且目标子网具有相同的可用 IP，则会将它分配给故障转移 VM。 如果目标子网没有相同的可用 IP，则为此 VM 保留子网中的某个可用 IP。 可以在“复制的项”>“设置”>“计算和网络”>“网络接口”中指定所选择的固定 IP。 可以选择 NIC，并指定所选的子网和 IP。
动态 IP| 支持 | 如果源 VM 上的 NIC 具有动态 IP 配置，故障转移 VM 上的 NIC 也默认为动态。 可以在“复制的项”>“设置”>“计算和网络”>“网络接口”中指定所选择的固定 IP。 可以选择 NIC，并指定所选的子网和 IP。
流量管理器集成 | 支持 | 可按以下方式预配置流量管理器：正常情况下，流量路由到源区域中的终结点；发生故障转移时，流量路由到目标区域中的终结点。
Azure 托管的 DNS | 支持 |
自定义 DNS  | 支持 |    
未经身份验证的代理 | 支持 | 请参阅[网络指南文档](site-recovery-azure-to-azure-networking-guidance.md)。    
经过身份验证的代理 | 不支持 | 如果 VM 对出站连接使用经过身份验证的代理，则不能使用 Azure Site Recovery 复制该 VM。    
具有本地网络的站点到站点 VPN（带或不带 ExpressRoute）| 支持 | 确保按以下方式配置 UDR 和 NSG：Site Recovery 流量不路由到本地网络。 请参阅[网络指南文档](site-recovery-azure-to-azure-networking-guidance.md)。  
VNET 到 VNET 连接 | 支持 | 请参阅[网络指南文档](site-recovery-azure-to-azure-networking-guidance.md)。  


## <a name="next-steps"></a>后续步骤
- 详细了解 [Azure VM 复制网络指南](site-recovery-azure-to-azure-networking-guidance.md)
- [复制 Azure VM](site-recovery-azure-to-azure.md)，开始对工作负荷进行保护

