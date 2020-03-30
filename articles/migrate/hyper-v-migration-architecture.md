---
title: Hyper-V 迁移在 Azure 迁移中的工作方式如何？
description: 了解 Azure 迁移的超 V 迁移
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 8bca88fc63a7fc04a22d2a68adbe59259b07f50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185882"
---
# <a name="how-does-hyper-v-replication-work"></a>Hyper-V 复制如何工作？

本文概述了使用 Azure 迁移服务器迁移工具迁移 Hyper-VM 时使用的体系结构和过程。

[Azure Migrate](migrate-services-overview.md) 提供一个中心用于跟踪本地应用、工作负荷与私有云/公有云 VM 的发现、评估及其到 Azure 的迁移。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商 (ISV) 产品。

## <a name="agentless-migration"></a>无代理迁移

Azure 迁移服务器迁移工具使用针对 Hyper-V 优化的迁移工作流为本地超 VM 提供无代理复制。 您仅在 Hyper-V 主机或群集节点上安装软件代理。 不需要在超 VM 上安装任何内容。

## <a name="server-migration-and-azure-site-recovery"></a>服务器迁移和 Azure 站点恢复

Azure 迁移服务器迁移是一种工具，用于将本地工作负荷和基于云的 VM 迁移到 Azure。 站点恢复是一种灾难恢复工具。 这些工具共享一些用于数据复制的通用技术组件，但用途不同。 


## <a name="architectural-components"></a>体系结构组件

![体系结构](./media/hyper-v-replication-architecture/architecture.png)



**组件** | **部署** | 
--- | --- 
**复制提供程序** | Microsoft Azure 站点恢复提供程序安装在 Hyper-V 主机上，并在 Azure 迁移服务器迁移中注册。<br/> 提供程序协调对超 VM 的复制。
**恢复服务代理** | Microsoft Azure 恢复服务代理处理数据复制。 它与提供程序一起将数据从超 VM 复制到 Azure。<br/> 复制的数据将上载到 Azure 订阅中的存储帐户。 服务器迁移工具处理复制的数据，并将其应用于订阅中的副本磁盘。 复制副本磁盘用于在迁移时创建 Azure VM。

- 组件由单个设置文件安装，该文件从门户中的 Azure 迁移服务器迁移下载。
- 提供程序和应用程序使用出站 HTTPS 端口 443 连接与 Azure 迁移服务器迁移进行通信。
- 来自提供商和代理的通信是安全和加密的。


## <a name="replication-process"></a>复制过程

1. 启用 Hyper-V VM 的复制时，开始初始复制。
2. 将拍摄超 V VM 快照。
3. VM 上的 VHD 逐个复制，直到它们全部复制到 Azure。 初始复制时间取决于 VM 大小和网络带宽。
4. 使用 Hyper-V 副本跟踪初始复制期间发生的磁盘更改，并存储在日志文件（hrl 文件）中。
    - 日志文件与磁盘位于同一文件夹中。
    - 每个磁盘都有一个发送到辅助存储的关联 hrl 文件。
    - 当初始复制正在进行时，快照和日志将占用磁盘资源。
4. 初始复制完成后，VM 快照将被删除，增量复制开始。
5. 增量磁盘更改在 hrl 文件中跟踪。 复制日志由恢复服务代理定期上载到 Azure 存储帐户。


## <a name="performance-and-scaling"></a>性能和缩放

Hyper-V 的复制性能受以下因素的影响：VM 大小、VM 的数据更改率（更改率）、用于日志文件存储的 Hyper-V 主机上的可用空间、复制数据的上载带宽以及 Azure 中的目标存储。

- 如果同时复制多台计算机，请使用 Hyper-V 的[Azure 站点恢复部署规划器](../site-recovery/hyper-v-deployment-planner-overview.md)来帮助优化复制。
- 规划 Hyper-V 复制，并根据容量在 Azure 存储帐户上分发复制。

### <a name="control-upload-throughput"></a>控制上传吞吐量

您可以限制用于将数据上载到每个 Hyper-V 主机上的 Azure 的带宽量。 小心。 如果将值设置得太低，将对复制产生负面影响，并延迟迁移。


1. 登录到 Hyper-V 主机或群集节点。
2. 运行**C：\程序文件\微软 Azure 恢复服务代理\bin_wabadmin.msc，** 以打开 Windows Azure 备份 MMC 管理单元。
3. 在该管理单元中，选择“更改属性”。****
4. 在 **"限制"中**，选择**启用备份操作的互联网带宽使用限制**。 设置工作和非工作小时数限制。 有效范围为 512 Kbps 到 1,023 Mbps。
I

### <a name="influence-upload-efficiency"></a>影响上传效率

如果您有用于复制的备用带宽，并且希望增加上载，则可以增加为上载任务分配的线程数，如下所示：

1. 使用 Regedit 打开注册表。
2. 导航到关键HKEY_LOCAL_MACHINE_软件\微软\Windows Azure 备份\复制\上传线程PerVM
3. 增加用于为每个复制 VM 的数据上载的线程数的值。 默认值为 4，最大值为 32。 




## <a name="next-steps"></a>后续步骤

使用 Azure 迁移服务器迁移尝试[Hyper-V 迁移](tutorial-migrate-hyper-v.md)。
