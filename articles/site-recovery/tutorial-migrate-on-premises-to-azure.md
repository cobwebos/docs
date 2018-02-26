---
title: "使用 Azure Site Recovery 将本地计算机迁移到 Azure | Microsoft 文档"
description: "本文将介绍如何使用 Azure Site Recovery 将本地计算机迁移到 Azure。"
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/18/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 406f0890da1ef4123b16082e7371d67f6328ea2c
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2018
---
# <a name="migrate-on-premises-machines-to-azure"></a>将本地计算机迁移到 Azure

除了使用 [Azure Site Recovery](site-recovery-overview.md) 服务管理和协调本地计算机和 Azure VM 的灾难恢复以实现业务连续性和灾难恢复 (BCDR) 外，还可以使用 Site Recovery 管理本地计算机到 Azure 的迁移。


本教程演示如何将本地 VM 和物理服务器迁移到 Azure。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 选择复制目标
> * 设置源和目标环境
> * 设置复制策略
> * 启用复制
> * 运行测试迁移，确保一切按预期正常工作
> * 面向 Azure 运行一次性故障转移

此教程为系列教程中的第三个教程。 本教程假设你已完成前面教程中的以下任务：

1. [准备 Azure](tutorial-prepare-azure.md)
2. 准备本地 [VMware](tutorial-prepare-on-premises-vmware.md) 服务器或 Hyper-V 服务器。

在开始之前，查看用于灾难恢复的 [VMware](concepts-vmware-to-azure-architecture.md) 或 [Hyper-V](concepts-hyper-v-to-azure-architecture.md) 体系结构会有所帮助。


## <a name="prerequisites"></a>先决条件

不支持半虚拟化驱动程序导出的设备。


## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

1. 登录到 [Azure 门户](https://portal.azure.com) > **恢复服务**。
2. 单击“创建资源” > “监视和管理” > “备份和站点恢复”。
3. 在“名称”中，指定友好名称 **ContosoVMVault**。 如果有多个订阅，请选择合适的一个。
4. 创建资源组 **ContosoRG**。
5. 指定 Azure 区域。 若要查看受支持的区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)中的“地域可用性”。
6. 若要从仪表板快速访问保管库，请单击“固定到仪表板”，然后单击“创建”。

   ![新保管库](./media/tutorial-migrate-on-premises-to-azure/onprem-to-azure-vault.png)

新保管库将添加到“仪表板”中的“所有资源”下，以及“恢复服务保管库”主页面上。



## <a name="select-a-replication-goal"></a>选择复制目标

选择要复制的内容以及要复制到的位置。
1. 单击“恢复服务保管库”> 保管库。
2. 在“资源”菜单中，依次单击“Site Recovery” > “准备基础结构” > “保护目标”。
3. 在“保护目标”中，选择要迁移的内容。
    - VMware：选择“到 Azure” > “是，使用 VMWare vSphere 虚拟机监控程序”。
    - “物理计算机”：选择“到 Azure” > “未虚拟化/其他”。
    - Hyper-V：选择“到 Azure” > “是，使用 Hyper-V”。 如果 Hyper-V VM 由 VMM 管理，则选择“是”。


## <a name="set-up-the-source-environment"></a>设置源环境

- [设置](tutorial-vmware-to-azure.md#set-up-the-source-environment) VMware VM 的源环境。
- [设置](tutorial-physical-to-azure.md#set-up-the-source-environment)物理服务器的源环境。
- [设置](hyper-v-azure-tutorial.md#set-up-the-source-environment) Hyper-V VM 的源环境。

## <a name="set-up-the-target-environment"></a>设置目标环境

选择并验证目标资源。

1. 单击“准备基础结构” > “目标”，并选择要使用的 Azure 订阅。
2. 指定资源管理器部署模型。
3. Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。

## <a name="set-up-a-replication-policy"></a>设置复制策略

- 为 VMware VM [设置复制策略](tutorial-vmware-to-azure.md#create-a-replication-policy)。
- 为物理服务器[设置复制策略](tutorial-physical-to-azure.md#create-a-replication-policy)。
- 为 Hyper-V VM [设置复制策略](hyper-v-azure-tutorial.md#set-up-a-replication-policy)。


## <a name="enable-replication"></a>启用复制

- 为 VMware VM [启用复制](tutorial-vmware-to-azure.md#enable-replication)。
- 为物理服务器[启用复制](tutorial-physical-to-azure.md#enable-replication)。
- 为 Hyper-V VM [启用复制](hyper-v-azure-tutorial.md#enable-replication)。


## <a name="run-a-test-migration"></a>运行测试迁移

运行[“测试故障转移”](tutorial-dr-drill-azure.md)，确保一切如预期正常运行。


## <a name="migrate-to-azure"></a>迁移到 Azure

为想要迁移的计算机运行故障转移。

1. 在“设置” > “复制的项”中，单击计算机 >“故障转移”。
2. 在“故障转移”中，选择要故障转移到的“恢复点”。 选择最新的恢复点。
3. 加密密钥设置与此方案无关。
4. 选择“在开始故障转移前关闭计算机”。 在触发故障转移之前，Site Recovery 将尝试关闭源虚拟机。 即使关机失败，故障转移也仍会继续。 可以在“作业”页上跟踪故障转移进度。
5. 检查 Azure VM 是否在 Azure 中按预期显示。
6. 在“复制的项”中，右键单击 VM >“完成迁移”。 该操作将完成迁移过程、停止 VM 的复制，并停止对 VM 的 Site Recovery 计费。

    ![完成迁移](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **请勿取消正在进行的故障转移**：在故障转移开始前，VM 复制已停止。 如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。

在某些情况下，故障转移需要大约八到十分钟的时间完成其他进程。 你可能注意到物理服务器、VMware Linux 计算机、未启用 DHCP 服务的 VMware VM 和未安装以下启动驱动程序：storvsc、vmbus、storflt、intelide、atapi 的 VMware VM 需要更长的测试故障转移时间。


## <a name="next-steps"></a>后续步骤

在本教程中，已将本地 VM 迁移到 Azure VM。 现在可以为 Azure VM 配置灾难恢复。

> [!div class="nextstepaction"]
> 从本地站点迁移后为 Azure VM [设置灾难恢复](site-recovery-azure-to-azure-after-migration.md)。
