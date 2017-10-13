---
title: "查看在 Azure 区域之间执行 Azure VM 复制的体系结构 | Microsoft Docs"
description: "本文概述使用 Azure Site Recovery 服务在 Azure 区域之间复制 Azure VM 时所用的组件和体系结构。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/12/2017
ms.author: raynew
ms.openlocfilehash: f471add4f4dee26482e2820fb06d010d6c0c0e88
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="step-1-review-the-architecture-for-azure-vm-replication-between-azure-regions"></a>步骤 1：查看在 Azure 区域之间执行 Azure VM 复制的体系结构


查看此部署的[概述步骤](azure-to-azure-walkthrough-overview.md)后，阅读此文了解在使用 [Azure Site Recovery](site-recovery-overview.md) 将 Azure 虚拟机 (VM) 从一个 Azure 区域复制和恢复到另一个 Azure 区域时使用的组件和进程。

- 完成此文后，应清楚地了解将 Azure 虚拟机复制到另一个区域的工作原理。
- 请将任何评论发布到本文底部，或者在 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中提问。

>[!NOTE]
>使用 Site Recovery 服务复制 Azure VM 的功能目前处于预览状态。



## <a name="architectural-components"></a>体系结构组件

下图提供特定区域（在此示例中为美国东部）中 Azure VM 环境的高级视图。 在 Azure VM 环境中：
- 应用可在磁盘跨存储帐户分布的 VM 上运行。
- VM 可包含在虚拟网络的一个或多个子网内。

![customer-environment](./media/azure-to-azure-walkthrough-architecture/source-environment.png)

## <a name="replication-process"></a>复制过程

### <a name="step-1"></a>步骤 1

如果在 Azure 门户中启用 Azure VM 复制，系统会在目标区域中自动创建以下图表中显示的资源。 默认情况下，资源根据源区域设置创建。 可根据需要自定义目标设置。 [了解详细信息](site-recovery-replicate-azure-to-azure.md)。

![启用复制过程，步骤 1](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-1.png)

**资源** | **详细信息**
--- | ---
**目标资源组** | 复制的 VM 在故障转移后所属的资源组。
**目标虚拟网络** | 复制的 VM 在故障转移后所处的虚拟网络。 系统会在源虚拟网络和目标虚拟网络之间创建网络映射，反之亦然。
**缓存存储帐户** | 在源 VM 上的更改复制到目标存储帐户前，系统会跟踪这些更改并将更改发送到目标位置的缓存存储帐户。 这样可确保将对 VM 上运行的生产应用产生的影响降至最低。
**目标存储帐户**  | 向其复制数据的目标位置的存储帐户。
**目标可用性集**  | 复制的 VM 在故障转移后所处的可用性集。

### <a name="step-2"></a>步骤 2

由于启用了复制，Site Recovery 扩展移动服务会在 VM 上自动安装。 将出现以下情况：

1. VM 会注册到 Site Recovery。

2. VM 会配置连续复制。 在 VM 磁盘上写入的数据会连续传输到源位置的缓存存储帐户。

   ![启用复制过程，步骤 2](./media/azure-to-azure-walkthrough-architecture/enable-replication-step-2.png)

  
  请注意，Site Recovery 从不需要与 VM 进行入站连接。 仅需要与 Site Recovery 服务 URL/IP 地址、Office 365 身份验证 URL/IP 地址和缓存存储帐户 IP 地址进行出站连接。 

## <a name="continuous-replication-process"></a>连续复制过程

启用连续复制后，磁盘写入内容会立即传输到缓存存储帐户。 Site Recovery 处理数据，并将其发送到目标存储帐户。 处理数据后，目标存储帐户中每隔几分钟就会生成恢复点。

## <a name="failover-process"></a>故障转移过程

如果启动故障转移，系统会在目标资源组、目标虚拟网络、目标子网和目标可用性集中创建 VM。 在故障转移期间，可使用任何恢复点。

![故障转移过程](./media/azure-to-azure-walkthrough-architecture/failover.png)

## <a name="next-steps"></a>后续步骤

转到[第 2 步：验证先决条件和限制](azure-to-azure-walkthrough-prerequisites.md)
