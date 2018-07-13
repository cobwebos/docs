---
title: 使用 Azure Site Recovery 执行 Azure 到 Azure 的复制的体系结构 | Microsoft Docs
description: 本文概述使用 Azure Site Recovery 服务在 Azure 区域之间复制 Azure VM 时所用的组件和体系结构。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 33ab90f958e5033c0c563e4fd8921ee1f7d57c47
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37915667"
---
# <a name="azure-to-azure-replication-architecture"></a>Azure 到 Azure 复制体系结构


本文介绍使用 [Azure Site Recovery](site-recovery-overview.md) 服务在 Azure 区域之间对 Azure 虚拟机 (VM) 进行复制、故障转移和恢复时使用的体系结构。




## <a name="architectural-components"></a>体系结构组件

下图提供特定区域（在此示例中为“美国东部”位置）中 Azure VM 环境的概要视图。 在 Azure VM 环境中：
- 应用可在托管磁盘或非托管磁盘跨存储帐户分布的 VM 上运行。
- VM 可包含在虚拟网络的一个或多个子网内。


**Azure 到 Azure 复制**

![customer-environment](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>复制过程

### <a name="step-1"></a>步骤 1

启用 Azure VM 复制时，将根据源区域设置自动在目标区域中创建以下资源。 可根据需要自定义目标资源设置。

![启用复制过程，步骤 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**资源** | **详细信息**
--- | ---
**目标资源组** | 复制的 VM 在故障转移后所属的资源组。 此资源组的位置可以是除托管源虚拟机区域以外的任何 Azure 区域。
**目标虚拟网络** | 复制的 VM 在故障转移后所处的虚拟网络。 系统会在源虚拟网络和目标虚拟网络之间创建网络映射，反之亦然。
**缓存存储帐户** | 在源 VM 更改复制到目标存储帐户前，系统会跟踪这些更改并将更改发送到源位置的缓存存储帐户。 此步骤可确保将对 VM 上运行的生产应用程序产生的影响降至最低。
**目标存储帐户（如果源 VM 不使用托管磁盘）**  | 向其复制数据的目标位置的存储帐户。
**副本托管磁盘（如果源 VM 在托管磁盘上）**  | 向其复制数据的目标位置的托管磁盘。
**目标可用性集**  | 复制的 VM 在故障转移后所处的可用性集。

### <a name="step-2"></a>步骤 2

由于启用了复制，Site Recovery 扩展移动服务会自动安装在 VM 上：

1. VM 会注册到 Site Recovery。

2. VM 会配置连续复制。 在 VM 磁盘上写入的数据会连续传输到源位置的缓存存储帐户。

   ![启用复制过程，步骤 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)


 Site Recovery 不需要与 VM 进行入站连接。 以下项仅需要出站连接。

 - 站点恢复服务 URL/IP 地址
 - Office 365 身份验证 URL/IP 地址
 - 缓存存储帐户 IP 地址

如果启用了多 VM 一致性，则复制组中的计算机将通过端口 20004 相互通信。 请确保没有防火墙设备阻止 VM 之间通过端口 20004 进行的内部通信。

> [!IMPORTANT]
如果想要 Linux VM 成为复制组的一部分，请确保按照特定 Linux 版本的指南手动打开端口 20004 上的出站流量。

### <a name="step-3"></a>步骤 3

正在连续复制时，磁盘写入内容会立即传输到缓存存储帐户。 Site Recovery 处理数据，并将其发送到目标存储帐户或副本托管磁盘。 处理数据后，目标存储帐户中每隔几分钟就会生成恢复点。

## <a name="failover-process"></a>故障转移过程

如果启动故障转移，系统会在目标资源组、目标虚拟网络、目标子网和目标可用性集中创建 VM。 在故障转移期间，可使用任何恢复点。

![故障转移过程](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>后续步骤

将 Azure VM [快速复制](azure-to-azure-quickstart.md)到次要区域。
