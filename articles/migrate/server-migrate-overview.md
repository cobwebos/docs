---
title: 选择具有 Azure 迁移服务器迁移的 VMware 迁移选项 |Microsoft Docs
description: 提供对 Azure 的 Azure 迁移服务器迁移的迁移 VMware Vm 的选项概述
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: f8bfbe26dc4c6ddbcf622f91938ba060de00b2ec
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811565"
---
# <a name="select-a-vmware-migration-option"></a>选择 VMware 迁移选项

可以将 VMware Vm 迁移到 Azure 中使用 Azure 迁移 Server 迁移工具。 此工具提供了几个用于 VMware VM 迁移的选项：

- 使用无代理复制的迁移。 将 Vm 迁移而无需在其上安装任何内容。
- 使用复制代理进行迁移。 复制在 VM 上安装代理。


尽管无代理复制是从部署角度来看更容易，但它当前具有许多限制。

## <a name="agentless-migration-limitations"></a>无代理迁移限制

限制如下所示：

- **同时复制**:从 vCenter 服务器，可同时复制最多为 50 的 Vm。<br/> 如果有超过 50 个迁移的 Vm，创建多个批次的 Vm。<br/> 更多复制一次将影响性能。
- **VM 磁盘**:你想要迁移的 VM 必须少于或等于 60 磁盘。
- **VM 操作系统**:一般情况下，Azure Migrate 可以迁移任何 Windows Server 或 Linux 操作系统，但它可能需要在 Vm 上的更改，以便它们可以在 Azure 中运行。 Azure Migrate，可自动为这些操作系统的更改：
    - Red Hat Enterprise Linux 6.5 + 7.0 +
    - CentOS 6.5+, 7.0+
    - SUSE Linux Enterprise Server 12 SP1+
    - Ubuntu 14.04LTS，16.04LTS、 18.04LTS
    - Debian 7、 8
    - 对于其他操作系统，你需要进行迁移之前手动调整。 [迁移教程](tutorial-migrate-vmware.md)介绍了如何执行此操作。
- **Linux 启动**:如果 /boot 专用分区上，它应驻留在 OS 磁盘，并且不能跨越多个磁盘。<br/> 如果 /boot 是根 （/） 分区的一部分，/ 分区应在 OS 磁盘上并不跨其他磁盘。
- **UEFI 引导**:具有 UEFI boot 的虚拟机不支持进行迁移。
- **加密磁盘/卷 (BitLocker，cryptfs)** :使用加密的磁盘卷的虚拟机不支持进行迁移。
- **RDM/传递磁盘**:如果 Vm 有 RDM 或传递磁盘，这些磁盘不会复制到 Azure
- **NFS**:不会复制在 Vm 上装载为卷的 NFS 卷。
- **目标存储**:仅，您可以将 VMware Vm 迁移到 Azure Vm 使用托管磁盘 （HDD 标准、 高级 SSD）。



## <a name="deployment-steps-comparison"></a>部署步骤比较

完成后查看限制，了解部署每个解决方案时所涉及的步骤可能会帮助您决定选择哪个选项。

**任务** | **详细信息** |**无代理** | **基于代理的**
--- | --- | --- | ---
**为迁移准备 VMware 服务器和 Vm** | 在 VMware 服务器和 Vm 上配置一些设置。 | 必填 | 必填
**添加服务器迁移工具** | 在 Azure Migrate 项目中添加 Azure 迁移 Server 迁移工具。 | 需要 | 必填
**部署 Azure Migrate 设备** | 设置用于 VM 发现和评估 VMware VM 上的轻量设备。 | 必填 | 非必需。
**在 Vm 上安装移动服务** | 想要复制的每个 VM 上安装移动服务 | 不是必需 | 必填
**部署 Azure 迁移服务器迁移复制设备** | 设置 VMware VM 上的设备以发现 Vm，并在 Vm 和迁移服务器迁移的 Azure 上运行的移动服务之间桥接 | 不是必需 | 必填
**将 Vm 复制**。 启用 VM 复制。 | 配置复制设置，并选择要复制的 Vm | 需要 | 需要
**运行测试迁移** | 运行测试迁移，以确保一切如预期正常运行。 | 需要 | 必填
**运行完整迁移** | 将 Vm 迁移。 | 需要 | 必填




## <a name="next-steps"></a>后续步骤

[将 VMware Vm 迁移](tutorial-migrate-vmware.md)使用无代理迁移。



