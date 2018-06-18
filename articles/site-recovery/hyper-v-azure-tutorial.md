---
title: 使用 Azure Site Recovery 为 Hyper-V VM（不包含 VMM）设置到 Azure 的灾难恢复 | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 服务为本地 Hyper-V VM（不包含 VMM）设置灾难恢复。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/21/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9ee5478412b02615efec983dd0b99c12fc2d9213
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34643577"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>对 Azure 设置本地 Hyper-V VM 的灾难恢复

[Azure Site Recovery](site-recovery-overview.md) 服务可管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复，进而有利于灾难恢复策略。

本教程演示如何对 Azure 设置本地 Hyper-V VM 的灾难恢复 本教程适用于并非由 System Center Virtual Machine Manager (VMM) 托管的 Hyper-V VM。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 选择复制源和目标。
> * 设置源复制坏境，包括本地 Site Recovery 组件和目标复制环境。
> * 创建复制策略。
> * 为 VM 启用复制。

此教程为系列教程中的第三个教程。 本教程假设你已完成前面教程中的以下任务：

1. [准备 Azure](tutorial-prepare-azure.md)
2. [准备本地 Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

在开始之前，[查看灾难恢复方案的体系结构](concepts-hyper-v-to-azure-architecture.md)会有所帮助。

## <a name="select-a-replication-goal"></a>选择复制目标


1. 在“所有服务” > “恢复服务保管库”中，选择在上一教程中准备的保管库“ContosoVMVault”。
2. 在“入门”中，单击“Site Recovery”。 然后单击“准备基础结构”
3. 在“保护目标” > “计算机所在位置”中，选择“本地”。
4. 在“要将计算机复制到何处?”中，选择“复制到 Azure”。
5. 在“是否要使用 System Center VMM 管理 Hyper-V 主机”中，选择“否”。 然后单击“确定”。

    ![复制目标](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>确认部署规划

在规划大型部署时，应确保完成[针对 Hyper-V 复制的部署规划](hyper-v-deployment-planner-overview.md)。 对于本教程，在“是否已完成部署规划?”中，从下拉列表中选择“我将稍后进行”。

![部署规划](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>设置源环境

若要设置源环境，请创建 Hyper-V 站点，并将 Hyper-V 主机添加到该站点。 然后，在每个主机上下载并安装 Azure Site Recovery 提供程序和 Azure 恢复服务代理，并将 Hyper-V 站点注册到保管库中。 

1. 在“准备基础结构”中，单击“源”。
2. 单击“+Hyper-V 站点”，指定在上一教程中创建的站点的名称“ContosoHyperVSite”。

    ![Hyper-V 站点](./media/hyper-v-azure-tutorial/hyperv-site.png)

3. 创建站点后，单击“+Hyper-V Server”。

    ![Hyper-V Server](./media/hyper-v-azure-tutorial/hyperv-server.png)

4. 下载提供程序安装程序文件。
6. 下载保管库注册密钥。 运行提供程序安装程序时需要此密钥。 生成的密钥有效期为 5 天。

    ![下载提供程序](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>安装提供程序

在每个添加到 ContosoHyperVSite 站点的 Hyper-V 主机上运行提供程序安装程序文件 (AzureSiteRecoveryProvider.exe)。 安装程序在每个 Hyper-V 主机上安装 Azure Site Recovery 提供程序和恢复服务代理。

1. 在“Azure Site Recovery 提供程序安装程序向导”>“Microsoft 更新”中，选择使用 Microsoft 更新检查提供程序更新。
2. 在“安装”中接受提供程序和代理的默认安装位置，并单击“安装”。
3. 安装后，在“Microsoft Azure Site Recovery 向导”>“保管库设置”中，单击“浏览”，然后在“密钥文件”中选择下载的保管库密钥文件。 
4. 指定 Azure Site Recovery 订阅、保管库名称 (ContosoVMVault) 和 Hyper-V 服务器所属的 Hyper-V 站点 (ContosoHyperVSite)。
5. 在“代理设置”中，选择“在不使用代理的情况下直接连接到 Azure Site Recovery”。
6. 在保管库中注册服务器后，在“注册”中单击“完成”。

Azure Site Recovery 将检索 Hyper-V 服务器中的元数据，该服务器显示在“Site Recovery 基础结构” > “Hyper-V 主机”中。 此过程最多可能需要 30 分钟。


## <a name="set-up-the-target-environment"></a>设置目标环境

选择并验证目标资源。 

1. 单击“准备基础结构” > “目标”。
2. 选择在故障转移后要在其中创建 Azure VM 的订阅和资源组“ContosoRG”。
3. 选择“资源管理器”部署模型。

Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。


## <a name="set-up-a-replication-policy"></a>设置复制策略

> [!NOTE]
> 至于 Hyper-V 到 Azure 的复制策略，将会停用这个 15 分钟的复制频率选项，改用 5 分钟的和 30 秒的复制频率设置。 将会自动更新那些使用 15分钟复制频率的复制策略，使之改用 5 分钟的复制频率设置。 与 15 分钟的复制频率相比，5 分钟和 30 秒的复制频率选项可以改进复制性能和恢复点目标，并会将对带宽使用和数据传输量的影响降至最低。

1. 单击“准备基础结构” > “复制设置” > “+创建和关联”。
2. 在“创建和关联策略”中指定策略名称“ContosoReplicationPolicy”。
3. 保留默认设置，并单击“确定”。
    - **复制频率**指示增量数据（初始复制之后）每五分钟复制一次。
    - **恢复点保留期**指示每个恢复点的保留时长为两个小时。
    - **应用一致性快照频率**指示每小时创建一次包含应用一致性快照的恢复点。
    - **初始复制开始时间**指示初始复制会立即开始。
4. 创建策略后，单击“确定”。 创建新策略时，该策略自动与指定的 Hyper-V 站点 (ContosoHyperVSite) 关联

    ![复制策略](./media/hyper-v-azure-tutorial/replication-policy.png)


## <a name="enable-replication"></a>启用复制


1. 在“复制应用程序”中，单击“源”。 
2. 在“源”中，选择“ContosoHyperVSite”站点。 然后单击“确定”。
3. 在“目标”中，将 Azure 作为目标验证，并验证保管库订阅，然后选择“资源管理器”部署模型。
4. 选择在上一教程中为复制数据创建的 contosovmsacct1910171607 存储帐户，以及故障转移后 Azure VM 所在的 ContosoASRnet 网络。
5. 在“虚拟机” > “选择”中，选择想要复制的 VM。 然后单击“确定”。

 可以在“作业” > “Site Recovery 作业”中，跟踪“启用保护”操作的进度。 “最后完成保护”作业完毕后，初始复制即已完成，虚拟机可执行故障转移。

## <a name="next-steps"></a>后续步骤
[运行灾难恢复演练](tutorial-dr-drill-azure.md)
