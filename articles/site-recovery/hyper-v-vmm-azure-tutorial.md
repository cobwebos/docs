---
title: 使用 Azure Site Recovery 设置 VMM 云中的本地 Hyper-V VM 到 Azure 的灾难恢复 | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 服务设置 System Center VMM 云中的本地 Hyper-V VM 到 Azure 的灾难恢复。
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: c759ca51fde6c3ead782d48b709c5a40b321c166
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916126"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-in-vmm-clouds-to-azure"></a>设置 VMM 云中的本地 Hyper-V VM 到 Azure 的灾难恢复

[Azure Site Recovery](site-recovery-overview.md) 服务可管理和协调本地计算机和 Azure 虚拟机 (VM) 的复制、故障转移和故障回复，进而有利于灾难恢复策略。

本教程演示如何对 Azure 设置本地 Hyper-V VM 的灾难恢复 本教程适用于由 System Center Virtual Machine Manager (VMM) 托管的 Hyper-V VM。 本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 选择复制源和目标。
> * 设置源复制坏境，包括本地 Site Recovery 组件和目标复制环境。
> * 设置网络映射，在 VMM VM 网络和 Azure 虚拟网络之间映射。
> * 创建复制策略
> * 为虚拟机启用复制

此教程为系列教程中的第三个教程。 本教程假设你已完成前面教程中的以下任务：

1. [准备 Azure](tutorial-prepare-azure.md)
2. [准备本地 Hyper-V](tutorial-prepare-on-premises-hyper-v.md)

在开始之前，[查看灾难恢复方案的体系结构](concepts-hyper-v-to-azure-architecture.md)会有所帮助。



## <a name="select-a-replication-goal"></a>选择复制目标

1. 在“所有服务” > “恢复服务保管库”中，单击在此系列教程中使用的保管库名称“ContosoVMVault”。
2. 在“入门”中，单击“Site Recovery”。 然后单击“准备基础结构”
3. 在“保护目标” > “计算机所在位置”中，选择“本地”。
4. 在“要将计算机复制到何处?”中，选择“复制到 Azure”。
5. 在“计算机是否已虚拟化”中，选择“是，带有 Hyper-V”。
6. 在“是否使用 System Center VMM”中，选择“是”。 然后单击“确定”。

    ![复制目标](./media/hyper-v-vmm-azure-tutorial/replication-goal.png)



## <a name="set-up-the-source-environment"></a>设置源环境

设置源环境时，安装 Azure Site Recovery 提供程序和 Azure 恢复服务代理，并在保管库中注册本地服务器。 

1. 在“准备基础结构”中，单击“源”。
2. 在“准备源”中单击“+VMM”以添加 VMM 服务器。 在“添加服务器”中，检查“服务器类型”中是否已显示“System Center VMM 服务器”。
3. 下载 Microsoft Azure Site Recovery 提供程序的安装程序。
4. 下载保管库注册密钥。 运行安装提供程序时需要用到此选项。 生成的密钥有效期为 5 天。
5. 下载恢复服务代理。

    ![下载](./media/hyper-v-vmm-azure-tutorial/download-vmm.png)

### <a name="install-the-provider-on-the-vmm-server"></a>安装 VMM 服务器上的提供程序

1. 在“Azure Site Recovery 提供程序安装程序向导”>“Microsoft 更新”中，选择使用 Microsoft 更新检查提供程序更新。
2. 在“安装”中接受提供程序的默认安装位置，并单击“安装”。 
3. 安装后，在“Microsoft Azure Site Recovery 向导”>“保管库设置”中，单击“浏览”，然后在“密钥文件”中选择下载的保管库密钥文件。
4. 指定 Azure Site Recovery 订阅和保管库名称 (ContosoVMVault)。 为 VMM 服务器指定友好名称以在保管库中标识此 VMM。
5. 在“代理设置”中，选择“在不使用代理的情况下直接连接到 Azure Site Recovery”。
6. 接受用于加密数据的证书的默认位置。 故障转移时，会解密已加密的数据。
7. 在“同步云元数据”中，选择“将云元数据同步到 Site Recovery 门户”。 此操作在每个服务器上只需执行一次。 然后单击“注册”。
8. 服务器在保管库中注册后，单击“完成”。

完成注册后，Azure Site Recovery 将检索服务器中的元数据，该 VMM 服务器显示在“Site Recovery 基础结构”中。

### <a name="install-the-recovery-services-agent"></a>安装恢复服务代理

在包含要复制的 VM 的每个 Hyper-V 主机上安装代理。

1. 在“Microsoft Azure 恢复服务代理安装向导”>“必备组件检查”中，单击“下一步”。 可自动安装任何缺少的必备组件。
2. 在“安装设置”中，接受安装位置和缓存位置。 缓存驱动器需要至少 5 GB 的存储空间。 我们建议使用 600 GB 或更多可用空间的驱动器。 然后单击“安装”。
3. 在“安装”中，当安装完成后，单击“关闭”以完成向导。

    ![安装代理](./media/hyper-v-vmm-azure-tutorial/mars-install.png)
    

## <a name="set-up-the-target-environment"></a>设置目标环境

1. 单击“准备基础结构” > “目标”。
2. 选择在故障转移后要在其中创建 Azure VM 的订阅和资源组 (ContosoRG)。
3. 选择“资源管理器”部署模型。

Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。


## <a name="configure-network-mapping"></a>配置网络映射

1. 在“Site Recovery 基础结构” > “网络映射” > “网络映射”中，单击“+网络映射”图标。
2. 在“添加网络映射”中，选择源 VMM 服务器。 选择 Azure 作为目标。
3. 在故障转移后，验证订阅和部署模型。
4. 在“源网络”中，选择源本地虚拟机网络。
5. 在“目标网络”中，选择在故障转移后副本 Azure VM 创建时所在的 Azure 网络。 然后单击“确定”。

    ![网络映射](./media/hyper-v-vmm-azure-tutorial/network-mapping-vmm.png)

## <a name="set-up-a-replication-policy"></a>设置复制策略

1. 单击“准备基础结构” > “复制设置” > “+创建和关联”。
2. 在“创建和关联策略”中指定策略名称“ContosoReplicationPolicy”。
3. 保留默认设置，并单击“确定”。
    - **复制频率**指示增量数据（初始复制之后）每五分钟复制一次。
    - **恢复点保留期**指示每个恢复点的保留时长为两个小时。
    - **应用一致性快照频率**指示每小时创建一次包含应用一致性快照的恢复点。
    - **初始复制开始时间**指示初始复制会立即开始。
    - **加密 Azure 上存储的数据** - 默认设置为“关闭”，指示不会加密 Azure 中的静态数据。
4. 创建策略后，单击“确定”。 当创建新策略时，该策略自动与 VMM 云关联。

## <a name="enable-replication"></a>启用复制

1. 在“复制应用程序”中，单击“源”。 
2. 在“源”中，选择 VMM 云。 然后单击“确定”。
3. 在“目标”中，将 Azure 作为目标验证，并验证保管库订阅，然后选择“资源管理器”模型。
4. 选择“contosovmsacct1910171607”存储帐户和“ContosoASRnet” Azure 网络。
5. 在“虚拟机” > “选择”中，选择想要复制的 VM。 然后单击“确定”。

 可以在“作业” > “Site Recovery 作业”中，跟踪“启用保护”操作的进度。 “最后完成保护”作业完毕后，初始复制即已完成，VM 可执行故障转移。


## <a name="next-steps"></a>后续步骤
[运行灾难恢复演练](tutorial-dr-drill-azure.md)
