---
title: "适用于 VMware 到 Azure 部署的 Azure Site Recovery Deployment Planner | Microsoft Docs"
description: "本文为 Azure Site Recovery Deployment Planner 用户指南。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/04/2017
ms.author: nisoneji
ms.openlocfilehash: 0910d5802d64ca637b3ecd1e392a6df8629c7f25
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2017
---
# <a name="azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>适用于 VMware 到 Azure 部署的 Azure Site Recovery 部署规划器
本文为适用于 VMware 到 Azure 生产部署的 Azure Site Recovery Deployment Planner 用户指南。

## <a name="overview"></a>概述

在开始使用 Site Recovery 保护任何 VMware 虚拟机 (VM) 之前，请根据每日数据更改率分配足够的带宽以满足所需的恢复点目标 (RPO)。 请确保在本地部署适当数量的配置服务器和进程服务器。

此外还需创建适当类型和数量的目标 Azure 存储帐户。 考虑到使用量会随着时间的推移而增加，从而导致源生产服务器数目的增加，请创建标准或高级存储帐户。 选择每个 VM 的存储类型时，需要考虑到工作负荷特征（例如每秒读/写 I/O 操作数 [IOPS] 或数据变动量）和 Site Recovery 限制。

Azure Site Recovery 部署规划器（第 2 版）是一个命令行工具，适用于 Hyper-V 到 Azure 和 VMware 到 Azure 灾难恢复方案。 可以使用此工具远程分析 VMware VM（不会给生产造成任何影响），了解成功进行复制和测试性故障转移所需满足的带宽和 Azure 存储要求。 不需在本地安装任何 Site Recovery 组件即可运行此工具。 但是，为了获得准确的吞吐量结果，建议在满足最终需要部署的 Site Recovery 配置服务器（生产部署的前几个步骤之一）的最低要求的 Windows Server 上运行 Planner。

该工具提供以下详细信息：

**兼容性评估**

* 根据磁盘数量、磁盘大小、IOPS、变动量、启动类型 (EFI/BIOS) 和 OS 版本评估 VM 的合格性
 
**网络带宽需求与 RPO 评估**

* 增量复制所需的估计网络带宽
* Site Recovery 可以获得的从本地到 Azure 的吞吐量
* 要批处理的 VM 数，具体取决于在给定时间内完成初始复制所需的估计带宽
* 给定带宽可以实现的 RPO
* 在预配较低带宽的情况下，对所需 RPO 的影响。

**Azure 基础结构要求**

* 每个 VM 的存储类型（标准或高级存储帐户）要求
* 要为复制设置的标准和高级存储帐户总数
* 基于 Azure 存储指南的存储帐户命名建议
* 适用于所有 VM 的存储帐户归置
* 针对订阅执行测试性故障转移或故障转移之前要设置的 Azure 核心数
* 适用于每个本地 VM 的 Azure VM 建议大小

**本地基础结构要求**
* 在本地部署配置服务器和进程服务器必须达到的数量

**DR 到 Azure 的估算成本**
* DR 到 Azure 的总估算成本：计算、存储、网络和 Azure Site Recovery 许可证成本
* 每个 VM 的详细成本分析


>[!IMPORTANT]
>
>由于使用量可能会随着时间的推移而增加，所有上述工具计算在执行时都会假定在工作负荷特征中存在一个 30% 的增长系数，而且所有分析指标（读/写 IOPS、变动量等）的值都会使用 95%。 这两个元素（增长系数和百分位数计算）均可配置。 若要详细了解增长系数，请参阅“增长系数考虑因素”部分。 若要详细了解百分位值，请参阅“用于计算的百分位值”部分。
>

## <a name="support-matrix"></a>支持矩阵

| | **VMware 到 Azure** |**Hyper-V 到 Azure**|**Azure 到 Azure**|**Hyper-V 到辅助站点**|**VMware 到辅助站点**
--|--|--|--|--|--
支持的方案 |是|是|否|是*|否
支持的版本 | vCenter 6.5、6.0 或 5.5| Windows Server 2016、Windows Server 2012 R2 | 不可用 |Windows Server 2016、Windows Server 2012 R2|不可用
支持的配置|vCenter、ESXi| Hyper-V 群集、Hyper-V 主机|不可用|Hyper-V 群集、Hyper-V 主机|不可用|
可以按 Azure Site Recovery 部署规划器的运行实例进行分析的服务器数 |单个（一次只能分析属于一个 vCenter Server 或一个 ESXi 服务器的 VM）|多个（一次可以分析多个主机或主机群集的 VM）| 不可用 |多个（一次可以分析多个主机或主机群集的 VM）| 不可用

*此工具主要用于 Hyper-V 到 Azure 灾难恢复方案。 对于 Hyper-V 到辅助站点灾难恢复，只能将其用于了解源端建议，例如所需网络带宽、每个源 Hyper-V 服务器上需要的可用存储空间，以及初始复制批处理数和批次定义。  忽略报表中的 Azure 建议和成本。 另外，“获取吞吐量”操作不适用于 Hyper-V 到辅助站点灾难恢复方案。

## <a name="prerequisites"></a>先决条件
该工具的操作分两个主要阶段：分析和报告生成。 此外还有第三个选项，即仅计算吞吐量。 下表提供了可以进行分析和吞吐量测量的服务器的要求：

| 服务器要求 | 说明|
|---|---|
|分析和吞吐量测量| <ul><li>操作系统：Microsoft Windows Server 2016 或 Microsoft Windows Server 2012 R2<br>（理想情况下，至少符合[配置服务器的建议大小](https://aka.ms/asr-v2a-on-prem-components)）</li><li>计算机配置：8 个 vCPU，16 GB RAM，300 GB HDD</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Microsoft Visual C++ Redistributable for Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>可在此服务器中通过 Internet 访问 Azure</li><li>Azure 存储帐户</li><li>服务器上的管理员访问权限</li><li>至少 100 GB 的可用磁盘空间（假定有 1000 个 VM，每个平均包含 3 个磁盘，分析时间为 30 天）</li><li>VMware vCenter 统计信息级别设置应设置为 2 或更高级别</li><li>允许 443 端口：ASR Deployment Planner 使用此端口连接到 vCenter 服务器/ESXi 主机</ul></ul>|
| 报告生成 | 装有 Microsoft Excel 2013 或更高版本的 Windows 电脑或 Windows Server |
| 用户权限 | 用于在分析期间访问 VMware vCenter 服务器/VMware vSphere ESXi 主机的用户帐户的只读权限 |

> [!NOTE]
>
>该工具只能分析使用 VMDK 和 RDM 磁盘的 VM， 无法分析使用 iSCSI 或 NFS 磁盘的 VM。 Site Recovery 支持 VMware 服务器的 iSCSI 和 NFS 磁盘，但由于 Deployment Planner 不在来宾计算机中并且只使用 vCenter 性能计数器进行分析，该工具无法洞察这些磁盘类型。
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>下载和提取部署规划器工具
1. 下载最新版本的 [Azure Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner)。  
该工具已打包到 .zip 文件夹中。 该工具的当前版本仅支持 VMware 到 Azure 的方案。

2. 将 .zip 文件夹复制到要从中运行该工具的 Windows Server。  
可以从 Windows Server 2012 R2 运行该工具，前提是该服务器具有网络访问权限，可以连接到 vCenter 服务器/vSphere ESXi 主机，其中包含要分析的 VM。 但是，我们建议在其硬件配置符合[配置服务器大小调整指南](https://aka.ms/asr-v2a-on-prem-components)的服务器上运行该工具。 如果已在本地部署 Site Recovery 组件，请从配置服务器运行该工具。

 我们建议在运行该工具的服务器上实施与配置服务器（包含内置的进程服务器）一样的硬件配置。 此类配置可确保该工具所报告的已实现吞吐量与 Site Recovery 在复制过程中能够达到的实际吞吐量相符。 吞吐量计算取决于服务器上的可用网络带宽和服务器的硬件配置（CPU、存储等）。 如果从任何其他服务器运行该工具，则会计算从该服务器到 Microsoft Azure 的吞吐量。 另外，由于该服务器的硬件配置可能不同于配置服务器的硬件配置，该工具所报告的已实现吞吐量可能不准确。

3. 解压缩 .zip 文件夹。  
该文件夹包含多个文件和子文件夹。 可执行文件是父文件夹中的 ASRDeploymentPlanner.exe。

    示例：  
    将 .zip 文件复制到 E:\ 驱动器并将它解压缩。
   E:\ASR Deployment Planner_v2.0zip

    E:\ASR Deployment Planner_v2.0\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>更新至最新版本的部署规划器
如果部署规划器为旧版，请执行以下操作之一：
 * 如果最新版不包含分析修补程序且分析已在当前版本的 Planner 上进行，请继续该分析。
 * 如果最新版本不包含分析修补程序，则建议停止在当前版本上进行分析，使用新版本重新开始分析。


 >[!NOTE]
 >
 >开始使用新版本进行分析以后，请传递相同的输出目录路径，以便工具将配置文件数据追加到现有文件。 将使用完整的分析数据集来生成报告。 如果传递不同的输出目录，则会创建新文件，生成报告时不能使用旧的分析数据。
 >
 >每个新的 Deployment Planner 都是 .zip 文件的累积更新。 不需将最新文件复制到旧文件夹。 可以创建和使用新文件夹。

## <a name="next-steps"></a>后续步骤
* [运行部署规划器](site-recovery-vmware-deployment-planner-run.md)。
