---
title: Azure Migrate 复制设备的体系结构 |Microsoft Docs
description: 概述 Azure Migrate 复制设备
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 4f4dc307bee4190a0e94ace493053e0cfd01150e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811435"
---
# <a name="replication-appliance"></a>复制设备

本文介绍了 Azure Migrate 用来复制设备：服务器评估迁移 VMware Vm、 物理机和私钥/公钥云 Vm 到 Azure，使用基于代理的迁移时。 

该工具现已推出[Azure Migrate](migrate-overview.md)中心。 在中心提供了用于评估和迁移，本机工具以及工具从其他 Azure 服务和第三方独立软件供应商 (Isv)。


## <a name="appliance-overview"></a>设备概述

复制设备被部署为单个本地机，为 VMware VM 或物理服务器。 运行：
- **复制设备**:复制设备协调通信，并管理数据复制，在本地 VMware Vm 和物理服务器复制到 Azure。
- **进程服务器**：进程服务器，默认情况下，复制设备上安装并执行以下操作：
    - **复制网关**:它充当复制网关。 从机启用复制，它接收复制数据。 它可优化通过缓存、 压缩和加密，复制数据，并将其发送到 Azure。
    - **代理安装程序**:执行移动服务的推送安装。 必须安装此服务上每个运行在本地和想要复制迁移的计算机。

## <a name="appliance-deployment"></a>设备的部署

**将部署为** | 用途  | **详细信息**
--- | --- |  ---
VMware VM | 迁移 VMware Vm 使用 Azure 迁移的迁移工具和基于代理的迁移时，通常使用。 | 从 Azure 迁移中心中，下载 OVA 模板，并导入到 vCenter 服务器来创建设备 VM。
物理计算机 | 如果你没有 VMware 基础结构迁移的本地物理服务器时，或者您无法创建使用 OVA 模板的 VMware VM 使用。 | 从 Azure 迁移中心下载软件的安装程序并运行它来设置设备机。

## <a name="appliance-deployment-requirements"></a>设备部署要求

[查看](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements)部署要求。



## <a name="appliance-license"></a>设备许可证
该设备附带了 Windows Server 2016 评估版许可证，有效期为 180 天。 如果在评估期即将到期，我们建议你下载并部署新的设备，或激活设备 VM 的操作系统许可证。

## <a name="replication-process"></a>复制过程

1. 启用 VM 复制时，对 Azure 存储的初始复制将开始（使用指定复制策略）。 
2. 流量通过 Internet 复制到 Azure 存储公共终结点。 不支持通过站点到站点虚拟专用网络 (VPN) 将流量从本地站点复制到 Azure。
3. 初始复制完成后，增量复制开始。 记录为机跟踪的更改。
4. 通信按如下方式发生：
    - Vm 通过 HTTPS 443 端口复制设备进行入站通信，进行复制管理。
    - 复制设备来协调通过 HTTPS 443 出站端口与 Azure 的复制。
    - 虚拟机发送复制数据到 HTTPS 9443 端口上的进程服务器 （复制设备上运行） 的入站。 可以修改此端口。
    - 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。
5. 复制数据首先登陆 Azure 中的缓存存储帐户。 这些日志进行处理和数据存储在 Azure 中托管的磁盘。

![体系结构](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>设备升级

设备将从 Azure 迁移中心手动升级。 我们建议你始终运行最新版本。

1. 在 Azure 中迁移 > 服务器 > Azure 迁移：服务器评估，基础结构服务器，单击**配置服务器**。
2. 在中**配置服务器**，链接将显示在**代理版本**复制设备的新版本时可用。 
3. 将安装程序下载到的复制设备计算机，并安装的升级。 安装程序检测到版本当前在设备上运行。
 
## <a name="next-steps"></a>后续步骤

[了解如何](tutorial-assess-vmware.md#set-up-the-appliance-vm)若要设置 VMware 设备。
[了解如何](tutorial-assess-hyper-v.md#set-up-the-appliance-vm)以设置 HYPER-V 设备。

