---
title: Azure Migrate 复制设备体系结构
description: 概述用于基于代理的迁移的 Azure Migrate 复制设备。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ba14767bde5d6cdca3a82dbe4e8a115ec25cc911
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186554"
---
# <a name="replication-appliance"></a>复制设备

本文介绍了使用基于代理的迁移将 VMware Vm、物理计算机和私有/公有云 Vm 迁移到 Azure 时 Azure Migrate：服务器评估使用的复制设备。 

[Azure Migrate](migrate-overview.md)中心提供此工具。 该中心提供用于评估和迁移的本机工具，以及来自其他 Azure 服务和第三方独立软件供应商（Isv）的工具。


## <a name="appliance-overview"></a>设备概述

复制设备部署为作为 VMware VM 或物理服务器的单台本地计算机。 它运行：
- **复制设备**：复制设备用于协调本地 VMware vm 和物理服务器复制到 Azure 时的通信并管理数据复制。
- **进程服务器**：默认情况下安装在复制设备上的进程服务器，并执行以下操作：
    - **复制网关**：充当复制网关。 它从启用了复制的计算机接收复制数据。 它通过缓存、压缩和加密来优化复制数据，并将其发送到 Azure。
    - **代理安装程序**：执行移动服务的推送安装。 必须在要复制以进行迁移的每台本地计算机上安装和运行此服务。

## <a name="appliance-deployment"></a>设备部署

**部署为** | 用途 | **详细信息**
--- | --- |  ---
VMware VM | 通常在使用基于代理的迁移的 Azure Migrate 迁移工具迁移 VMware Vm 时使用。 | 从 Azure Migrate 中心下载 .OVA 模板，并导入到 vCenter Server 以创建设备 VM。
物理计算机 | 如果你没有 VMware 基础结构，或者无法使用 .OVA 模板创建 VMware VM，则在迁移本地物理服务器时使用。 | 从 Azure Migrate 集线器下载软件安装程序，并运行它以设置设备计算机。

## <a name="appliance-deployment-requirements"></a>设备部署要求

[查看](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements)部署要求。



## <a name="appliance-license"></a>设备许可证
该设备附带了 Windows Server 2016 评估版许可证，该许可证在180天内有效。 如果评估期接近过期，我们建议你下载并部署新设备，或者激活设备 VM 的操作系统许可证。

## <a name="replication-process"></a>复制过程

1. 启用 VM 复制时，对 Azure 存储的初始复制将开始（使用指定复制策略）。 
2. 流量通过 Internet 复制到 Azure 存储公共终结点。 不支持通过站点到站点虚拟专用网络 (VPN) 将流量从本地站点复制到 Azure。
3. 初始复制完成后，增量复制将会开始。 已记录对计算机所做的更改。
4. 通信按如下方式发生：
    - Vm 与用于复制管理的端口 HTTPS 443 入站上的复制设备通信。
    - 复制设备通过端口 HTTPS 443 出站来协调与 Azure 的复制。
    - Vm 将复制数据发送到 HTTPS 9443 入站端口上的进程服务器（在复制设备上运行）。 可以修改此端口。
    - 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。
5. 复制数据首先登陆 Azure 中的缓存存储帐户。 将处理这些日志，并将数据存储在 Azure 托管磁盘中。

![体系结构](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>设备升级

设备将从 Azure Migrate 中心手动升级。 建议始终运行最新版本。

1. 在 Azure Migrate > 服务器 > Azure Migrate：服务器评估、基础结构服务器，请单击 "**配置服务器**"。
2. 在**配置服务器**中，当复制设备的新版本可用时，**代理版本**中会出现一个链接。 
3. 将安装程序下载到复制设备计算机，然后安装升级。 安装程序将检测设备上当前正在运行的版本。
 
## <a name="next-steps"></a>后续步骤

[了解如何](tutorial-assess-vmware.md#set-up-the-appliance-vm)为 VMware 设置设备。
[了解如何](tutorial-assess-hyper-v.md#set-up-the-appliance-vm)为 hyper-v 设置设备。

