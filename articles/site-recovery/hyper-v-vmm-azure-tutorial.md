---
title: 适用于在本地 HYPER-V Vm 设置灾难恢复，在对 Azure Site Recovery 的 VMM 云中 |Microsoft Docs
description: 了解如何使用 Site Recovery 设置到 Azure 的 System Center VMM 云中的本地 HYPER-V Vm 灾难恢复设置。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 653db1497fcce5981bba7416f073b0330ca2861f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66398150"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>设置 VMM 云中的本地 Hyper-V VM 到 Azure 的灾难恢复

本文介绍如何为灾难恢复到 Azure 使用的托管通过 System Center Virtual Machine Manager (VMM)，在本地 HYPER-V Vm 启用复制[Azure Site Recovery](site-recovery-overview.md)服务。 如果不使用 VMM 中，[遵循本教程](hyper-v-azure-tutorial.md)相反。

本文是系列教程的第三篇文章，介绍如何为本地 VMware VM 设置到 Azure 的灾难恢复。 在上一篇教程中，我们已[准备本地 Hyper-V 环境](hyper-v-prepare-on-premises-tutorial.md)，以便能够灾难恢复到 Azure。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 选择复制源和目标。
> * 设置源复制坏境，包括本地 Site Recovery 组件和目标复制环境。
> * 设置网络映射 VMM VM 网络和 Azure 虚拟网络之间进行映射。
> * 创建复制策略。
> * 为 VM 启用复制。

> [!NOTE]
> 教程中介绍了某个方案的最简单部署路径。 它们尽可能使用默认选项，并且不显示所有可能的设置和路径。 有关详细说明，请查看 [Site Recovery 文档](https://docs.microsoft.com/azure/site-recovery)的“操作指南”部分所列的文章  。

## <a name="before-you-begin"></a>开始之前

此教程为系列教程中的第三个教程。 本教程假设你已完成前面教程中的任务：

1. [准备 Azure](tutorial-prepare-azure.md)
2. [准备本地 Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>选择复制目标

1. 在 Azure 门户中，转到“恢复服务保管库”，然后选择保管库  。 在前面的教程中，我们已准备好保管库 **ContosoVMVault**。
2. 在“入门”中选择“Site Recovery”，然后选择“准备基础结构”    。
3. 在“保护目标” > “计算机位于何处?”中，选择“本地”    。
4. 在“要将计算机复制到何处?”中，选择“复制到 Azure”   。
5. 在“计算机是否已虚拟化?”中，选择“是，带有 Hyper-V”   。
6. 在中**你将使用 System Center VMM 管理 HYPER-V 主机？** ，选择**是**。
7.  选择“确定”  。

    ![复制目标](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>确认部署规划

1. 在“部署规划”中，若要规划大型部署，请通过页面上的链接下载适用于 Hyper-V 的部署规划器。  [详细了解](hyper-v-deployment-planner-overview.md) Hyper-V 部署规划。
2. 本教程不需要使用部署规划器。 在“是否已完成部署规划?”  中选择“我将稍后进行”  ，然后选择“确定”  。

## <a name="set-up-the-source-environment"></a>设置源环境

在设置源环境时，您在 VMM 服务器上安装 Azure Site Recovery 提供程序和在保管库中注册服务器。 在每台 Hyper-V 主机上安装 Azure 恢复服务代理。

1. 在中**准备基础结构**，选择**源**。
2. 在中**准备源**，选择 **+ VMM**添加 VMM 服务器。 在“添加服务器”中，检查“服务器类型”中是否已显示“System Center VMM 服务器”    。
3. 下载 Microsoft Azure Site Recovery 提供程序的安装程序。
4. 下载保管库注册密钥。 运行提供程序安装程序时需要此密钥。 生成的密钥有效期为 5 天。
5. 下载 Microsoft Azure 恢复服务代理的安装程序。

    ![下载提供程序、 注册密钥和代理](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>安装 VMM 服务器上的提供程序

1. 在“Azure Site Recovery 提供程序安装程序向导”>“Microsoft 更新”中，选择使用 Microsoft 更新检查提供程序更新  。
2. 在中**安装**，接受默认安装位置提供程序，然后选择**安装**。
3. 安装后，在 Microsoft Azure Site Recovery 注册向导 >**保管库设置**，选择**浏览**，然后在**密钥文件**，选择保管库密钥文件已下载。
4. 指定 Azure Site Recovery 订阅和保管库名称 (ContosoVMVault  )。 为 VMM 服务器指定友好名称以在保管库中标识此 VMM。
5. 在“代理设置”中，选择“在不使用代理的情况下直接连接到 Azure Site Recovery”   。
6. 接受用于加密数据的证书的默认位置。 故障转移时，会解密已加密的数据。
7. 在“同步云元数据”  中，选择“将云元数据同步到 Site Recovery 门户”  。 此操作需要每个服务器上执行仅一次。 然后，选择“注册”  。
8. 在保管库中注册服务器后，选择**完成**。

完成注册后，Azure Site Recovery 将检索服务器中的元数据，该 VMM 服务器显示在“Site Recovery 基础结构”  中。

### <a name="install-the-recovery-services-agent-on-hyper-v-hosts"></a>在 Hyper-V 主机上安装恢复服务代理

每个包含想要复制的 Vm 的 HYPER-V 主机上安装代理。

1. 在 Microsoft Azure 恢复服务代理安装向导中 >**必备项检查**，选择**下一步**。 将自动安装任何缺少的先决条件。
2. 在中**安装设置**，接受安装位置和缓存位置。 缓存驱动器需要至少 5 GB 的存储空间。 我们建议使用 600 GB 或更多可用空间的驱动器。 然后，选择“安装”  。
3. 在中**安装**，当安装完成后，选择**关闭**以完成该向导。

    ![安装代理](./media/hyper-v-vmm-azure-tutorial/mars-install.png)

## <a name="set-up-the-target-environment"></a>设置目标环境

1. 选择“准备基础结构” > “目标”。  
2. 选择在故障转移后要在其中创建 Azure VM 的订阅和资源组 (ContosoRG  )。
3. 选择“资源管理器”部署模型  。

Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。

## <a name="configure-network-mapping"></a>配置网络映射

1. 在中**Site Recovery 基础结构** > **网络映射** > **网络映射**，选择 **+ 网络映射**图标。
2. 在“添加网络映射”  中，选择源 VMM 服务器。 选择 Azure  作为目标。
3. 在故障转移后，验证订阅和部署模型。
4. 在“源网络”  中，选择源本地虚拟机网络。
5. 在“目标网络”  中，选择在故障转移后副本 Azure VM 创建时所在的 Azure 网络。 选择“确定”。 

    ![网络映射](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>设置复制策略

1. 选择“准备基础结构”   > “复制设置”   > “+创建和关联”  。
2. 在“创建和关联策略”  中指定策略名称。 我们将使用 **ContosoReplicationPolicy**。
3. 保留默认设置，并选择**确定**。
    - **复制频率**指示，初始复制后，增量数据将复制每隔五分钟。
    - **恢复点保留期**指示将两个小时内保留的每个恢复点。
    - **应用一致性快照频率**指示每小时创建一次包含应用一致性快照的恢复点。
    - “初始复制开始时间”指示初始复制会立即开始  。
    - **加密数据存储在 Azure 上**设置为默认值 (**关闭**)，并指示未加密静态数据在 Azure 中的。
4. 创建策略后，选择“确定”  。 创建新策略时，它自动具有与 VMM 云关联。

## <a name="enable-replication"></a>启用复制

1. 在“复制应用程序”中，选择“源”   。
2. 在“源”  中，选择 VMM 云。 选择“确定”。 
3. 在中**目标**，验证目标 (Azure) 的保管库订阅，然后选择**资源管理器**模型。
4. 选择**contosovmsacct1910171607**存储帐户和**ContosoASRnet** Azure 网络。
5. 在“虚拟机” > “选择”中，选择要复制的 VM   。 选择“确定”。 

   可以在“作业”   > “Site Recovery 作业”  中，跟踪“启用保护”  操作的进度。 “最后完成保护”作业完成后，初始复制即已完成，VM 可执行故障转移  。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [运行灾难恢复演练](tutorial-dr-drill-azure.md)
