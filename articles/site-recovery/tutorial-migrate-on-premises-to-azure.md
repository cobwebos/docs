---
title: "使用 Azure Site Recovery 将本地计算机迁移到 Azure | Microsoft 文档"
description: "本文将介绍如何使用 Azure Site Recovery 将本地计算机迁移到 Azure。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: cfd44f7f06faa7d1d00efa9427dbf5d1d0a89ef1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-on-premises-machines-to-azure"></a>将本地计算机迁移到 Azure

[Azure Site Recovery](site-recovery-overview.md) 服务管理和安排本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复。

本教程将演示如何使用 Site Recovery 将本地 VM 和物理服务器迁移到 Azure。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 设置部署的先决条件。
> * 为 Site Recovery 创建恢复服务保管库
> * 部署本地管理服务器
> * 设置复制策略并启用复制
> * 运行灾难恢复演练以确保一切正常
> * 面向 Azure 运行一次性故障转移

## <a name="overview"></a>概述

通过为计算机启用复制来迁移计算机，并将其故障转移到 Azure。


## <a name="prerequisites"></a>先决条件

以下是针对本教程需要完成的任务。

- [准备](tutorial-prepare-azure.md) Azure 资源，其中包括 Azure 订阅、Azure 虚拟网络和存储帐户。
- [准备](tutorial-prepare-on-premises-vmware.md) VMware 本地 VMware 服务器和 VM。
- 请注意，不支持半虚拟化驱动程序导出的设备。


## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>选择保护目标

选择要复制的内容以及要复制到的位置。
1. 单击“恢复服务保管库”> 保管库。
2. 在“资源”菜单中，单击“Site Recovery” > “准备基础结构” > “保护目标”。
3. 在“保护目标”中，选择：
    - VMware：选择“到 Azure” > “是，使用 VMWare vSphere 虚拟机监控程序”。
    - “物理计算机”：选择“到 Azure” > “未虚拟化/其他”。
    - Hyper-V：选择“到 Azure” > “是，使用 Hyper-V”。


## <a name="set-up-the-source-environment"></a>设置源环境

- [设置](tutorial-vmware-to-azure.md#set-up-the-source-environment) VMware VM 的源环境。
- [设置](tutorial-physical-to-azure.md#set-up-the-source-environment)物理服务器的源环境。
- [设置](tutorial-hyper-v-to-azure.md#set-up-the-source-environment) Hyper-V VM 的源环境。

## <a name="set-up-the-target-environment"></a>设置目标环境

选择并验证目标资源。

1. 单击“准备基础结构” > “目标”，并选择要使用的 Azure 订阅。
2. 指定目标部署模型。
3. Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。

## <a name="create-a-replication-policy"></a>创建复制策略

- 为 VMware VM [设置复制策略](tutorial-vmware-to-azure.md#create-a-replication-policy)。


## <a name="enable-replication"></a>启用复制

- 为 VMware VM [启用复制](tutorial-vmware-to-azure.md#enable-replication)。


## <a name="run-a-test-migration"></a>运行测试迁移

运行[测试故障转移](tutorial-dr-drill-azure.md)，确保一切如预期正常运行。


## <a name="migrate-to-azure"></a>迁移到 Azure

为想要迁移的计算机运行故障转移。

1. 在“设置” > “复制的项”中，单击计算机 >“故障转移”。
2. 在“故障转移”中，选择要故障转移到的“恢复点”。 选择最新的恢复点。
3. 加密密钥设置与此方案无关。
4. 如果希望 Site Recovery 在触发故障转移之前尝试关闭源虚拟机，请选择“在开始故障转移之前关闭计算机”。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。
5. 检查 Azure VM 是否在 Azure 中按预期显示。
6. 在“复制的项”中，右键单击 VM >“完成迁移”。 该操作将完成迁移过程、停止 VM 的复制，并停止对 VM 的 Site Recovery 计费。

    ![完成迁移](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **请勿取消正在进行的故障转移**：在故障转移开始前，停止 VM 复制。 如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。

在某些情况下，故障转移需要大约八到十分钟的时间完成其他进程。 你可能注意到物理服务器、VMware Linux 计算机、未启用 DHCP 服务的 VMware VM 和未安装以下启动驱动程序：storvsc、vmbus、storflt、intelide、atapi 的 VMware VM 需要更长的测试故障转移时间。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在迁移到 Azure 后，将 Azure VM 复制到另一个区域](site-recovery-azure-to-azure-after-migration.md)
