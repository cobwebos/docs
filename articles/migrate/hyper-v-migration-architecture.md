---
title: 使用 Azure 迁移服务器迁移的 HYPER-V 迁移是如何工作的？ | Microsoft Docs
description: 提供 Azure 迁移服务器迁移的 HYPER-V 迁移的概述
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9148e76a9f2abd369ae595422d785a347e58dfab
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811708"
---
# <a name="how-does-hyper-v-replication-work"></a>HYPER-V 复制如何工作？

本文概述了体系结构和使用 Azure 迁移 Server 迁移工具迁移的 HYPER-V Vm 时使用的过程。

[Azure Migrate](migrate-services-overview.md)提供中心，以跟踪发现、 评估和迁移的本地应用和工作负荷，及私钥/公钥云 Vm 到 Azure。 中心提供了评估和迁移，以及第三方独立软件供应商 (ISV) 产品/服务的 Azure 迁移工具。

## <a name="agentless-migration"></a>无代理迁移

Azure 迁移 Server 迁移工具提供了无代理的本地 HYPER-V Vm 复制使用适用于 HYPER-V 的迁移工作流。 仅在 HYPER-V 主机或群集节点上安装软件代理。 不需在 HYPER-V Vm 上安装。

## <a name="server-migration-and-azure-site-recovery"></a>服务器迁移和 Azure Site Recovery

Azure 迁移服务器迁移是一个用于迁移的本地工作负荷和基于云的 Vm，Azure 工具。 Site Recovery 是灾难恢复工具。 这些工具共享数据复制所用的一些常见技术组件，但满足不同目的。 


## <a name="architectural-components"></a>体系结构组件

![体系结构](./media/hyper-v-replication-architecture/architecture.png)



组件  | **部署** | 
--- | --- 
**复制提供程序** | 在 HYPER-V 主机上安装和注册 Azure 迁移服务器迁移到 Microsoft Azure Site Recovery 提供程序。<br/> 提供程序来协调 HYPER-V Vm 复制。
**恢复服务代理** | Microsoft Azure 恢复服务代理处理数据的复制。 它适用于要将数据从 HYPER-V Vm 复制到 Azure 的提供程序。<br/> 复制的数据上载到你的 Azure 订阅中的存储帐户。 Server 迁移工具的过程复制的数据，并将其应用于订阅中的副本磁盘。 磁盘副本用于创建 Azure Vm 迁移时。

- 安装组件的单个安装程序文件，从 Azure 迁移服务器迁移门户中下载。
- 提供程序和设备使用出站 HTTPS 端口 443 连接来与 Azure 迁移服务器迁移通信。
- 从提供程序和代理的通信的安全性和加密。


## <a name="replication-process"></a>复制过程

1. 当你启用的 HYPER-V VM 复制时，初始复制开始。
2. 将 HYPER-V VM 快照。
3. 在 VM 上的 Vhd 是-逐个复制，直到它们已全部复制到 Azure。 初始复制时间取决于 VM 大小和网络带宽。
4. 使用 HYPER-V 副本，并存储在日志文件 （hrl 文件） 中跟踪的初始复制期间发生磁盘更改。
    - 日志文件位于与磁盘相同的文件夹中。
    - 每个磁盘有一个关联的 hrl 文件发送到辅助存储。
    - 当初始复制正在进行时，快照和日志将占用磁盘资源。
4. 初始复制完成后，将删除 VM 快照，并开始将增量复制。
5. Hrl 文件跟踪的增量磁盘更改。 复制日志会定期上传到 Azure 存储帐户的恢复服务代理。


## <a name="performance-and-scaling"></a>性能和缩放

包括在 Azure 中的 VM 大小、 Vm 的数据更改率 （变动）、 日志文件存储的复制数据上传带宽和目标存储的 HYPER-V 主机上的可用空间的因素会影响复制的 HYPER-V 的性能。

- 如果要同时复制多台计算机，使用[Azure Site Recovery Deployment Planner](../site-recovery/hyper-v-deployment-planner-overview.md) hyper-v，以帮助优化复制。
- 规划 HYPER-V 复制，并分配到 Azure 存储帐户，根据容量的复制。

### <a name="control-upload-throughput"></a>控件上传吞吐量

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

试用[HYPER-V 迁移](tutorial-migrate-hyper-v.md)使用 Azure 迁移服务器迁移。
