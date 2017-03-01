---
title: "使用 Azure Site Recovery 实现 StorSimple 文件共享的自动灾难恢复 | Microsoft 文档"
description: "介绍有关针对 Microsoft Azure StorSimple 存储中托管的文件共享创建灾难恢复解决方案的步骤和最佳实践。"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/16/2016
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: c78eda33a64d630ba18aba6a174db372eb41dde9
ms.openlocfilehash: a0817ac21afb83745ec2cc213fc6029fa395d479


---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>使用 Azure Site Recovery 针对 StorSimple 上托管的文件共享创建自动灾难恢复解决方案
## <a name="overview"></a>概述
Microsoft Azure StorSimple 是一种混合型云存储解决方案，可解决通常与文件共享关联的非结构化数据的复杂性。 StorSimple 使用云存储作为本地解决方案的扩展，可跨本地存储和云存储自动将数据分层。 使用本地快照和云快照的集成式数据保护不需要扩建存储基础结构。

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) 是一项基于 Azure 的服务，可提供灾难恢复 (DR) 功能，对虚拟机的复制、故障转移和恢复进行协调。 Azure Site Recovery 支持一系列复制技术，可以前后一致地对虚拟机和应用程序进行复制、保护和无缝故障转移，转移目标是私有云/公用云或托管商的云。

使用 Azure Site Recovery、虚拟机复制和 StorSimple 云快照功能，可以保护整个文件服务器环境。 发生中断时，可以在 Azure 中使用单键恢复功能使文件系统在几分钟内联机。

本文档详细说明如何为 StorSimple 存储上托管的文件共享创建灾难恢复解决方案，以及使用单键恢复计划来执行计划内、计划外及测试故障转移。 简单而言，本文档说明如何修改 Azure Site Recovery 保管库中的恢复计划，以便在发生灾难时实现 StorSimple 故障转移。 此外，介绍了支持的配置与先决条件。 本文档假设读者熟悉 Azure Site Recovery 和 StorSimple 体系结构的基本概念。

## <a name="supported-azure-site-recovery-deployment-options"></a>支持的 Azure Site Recovery 部署选项
客户可以将文件服务器部署为在 Hyper-V 或 VMware 上运行的物理服务器或虚拟机 (VM)，然后基于从 StorSimple 存储中划分出来的卷创建文件共享。 Azure Site Recovery 可以保护辅助站点或 Azure 中的物理部署与虚拟部署。 本文档详细介绍某个 DR 解决方案，它使用 Azure 作为 Hyper-V 上托管的文件服务器 VM 的恢复站点，在 StorSimple 存储中使用文件共享。 文件服务器 VM 位于 VMware VM 或物理计算机上的其他方案也可以通过类似的方式实现。

## <a name="prerequisites"></a>先决条件
使用 Azure Site Recovery 针对 StorSimple 存储上托管的文件共享实现单键灾难恢复解决方案时，必须满足以下先决条件：

* 已在 Hyper-V 或 VMware 或物理计算机上托管本地 Windows Server 2012 R2 文件服务器 VM
* 已在 Azure StorSimple Manager 中注册本地 StorSimple 存储设备
* 已在 Azure StorSimple Manager（可以保持关闭状态）中创建 StorSimple Cloud Appliance
* 已在 StorSimple 存储设备上配置的卷中托管文件共享
* 已在 Microsoft Azure 订阅中创建 [Azure Site Recovery 服务保管库](../site-recovery/site-recovery-vmm-to-vmm.md)

此外，如果 Azure 是恢复站点，则可以在 VM 上运行 [Azure 虚拟机准备情况评估工具](http://azure.microsoft.com/downloads/vm-readiness-assessment/)，确保这些 VM 与 Azure VM 和 Azure Site Recovery 服务兼容。

为避免出现延迟问题（可能会导致成本增加），请确保在同一区域中创建 StorSimple Cloud Appliance、自动化帐户和存储帐户。

## <a name="enable-dr-for-storsimple-file-shares"></a>为 StorSimple 文件共享启用 DR
若要实现完整复制与恢复，本地环境的每个组件都需要受到保护。 本部分介绍如何执行以下操作：

* 设置 Active Directory 和 DNS 复制（可选）
* 使用 Azure Site Recovery 保护文件服务器 VM
* 保护 StorSimple 卷
* 配置网络

### <a name="set-up-active-directory-and-dns-replication-optional"></a>设置 Active Directory 和 DNS 复制（可选）
如果想要保护运行 Active Directory 和 DNS 的计算机，使它们能够在 DR 站点中可供使用，需要明确地对它们进行保护（以便在故障转移之后可以使用身份验证来访问文件服务器）。 根据客户本地环境的复杂性，可以使用两个建议的选项。

#### <a name="option-1"></a>选项 1
如果客户的整个本地网站中只有少量的应用程序和一个域控制器，并且需要故障转移整个站点，我们建议使用 Azure Site Recovery 复制将域控制器计算机复制到辅助站点（适用于站点到站点以及站点到 Azure 方案）。

#### <a name="option-2"></a>方法 2
如果客户有大量应用程序，正在运行 Active Directory 林，并且每次只故障转移少量的应用程序，我们建议在 DR 站点（辅助站点或 Azure 中）另外设置一个域控制器。

请参阅 [Automated DR solution for Active Directory and DNS using Azure Site Recovery](../site-recovery/site-recovery-active-directory.md)（使用 Azure Site Recovery 为 Active Directory 和 DNS 创建自动 DR 解决方案），获取在 DR 站点上提供域控制器的说明。 本文档余下内容假设 DR 站点上提供了域控制器。

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>使用 Azure Site Recovery 保护文件服务器 VM
此步骤需要准备本地服务器环境、创建并准备 Azure Site Recovery 保管库，以及为 VM 启用文件保护。

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>准备本地文件服务器环境
1. 将“用户帐户控制”设置为“永不通知”。 只有这样设置，才可以在通过 Azure Site Recovery 故障转移后使用 Azure 自动化脚本连接 iSCSI 目标。

   1. 按 Windows 键 + Q 并搜索 **UAC**。
   2. 选择“更改用户帐户控制设置”。
   3. 将底部横条朝“永不通知”方向拖动。
   4. 单击“确定”，然后在出现提示时选择“是”。

      ![](./media/storsimple-dr-using-asr/image1.png)
2. 在每个文件服务器 VM 上安装 VM 代理。 必须这样做才能在已故障转移的 VM 上运行 Azure 自动化脚本。

   1. [将代理下载](http://aka.ms/vmagentwin)到 `C:\\Users\\<username>\\Downloads`。
   2. 以管理员模式（以管理员身份运行）打开 Windows PowerShell，然后输入以下命令导航到下载位置：

      `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`

      > [!NOTE]
      > 根据具体的版本，文件名可能有所不同。
      >
      >
3. 单击“下一步”。
4. 接受**协议条款**，然后单击“下一步”。
5. 单击“完成” 。
6. 使用从 StorSimple 存储中划分出来的卷创建文件共享。 有关详细信息，请参阅 [Use the StorSimple Manager service to manage volumes](storsimple-manage-volumes.md)（使用 StorSimple Manager 服务管理卷）。

   1. 在本地 VM 上，按 Windows 键 + Q 并搜索 **iSCSI**。
   2. 选择“iSCSI 发起程序”。
   3. 选择“配置”选项卡并复制发起程序的名称。
   4. 登录到 [Azure 经典门户](https://manage.windowsazure.com/)。
   5. 选择“StorSimple”选项卡，然后选择包含物理设备的 StorSimple Manager 服务。
   6. 创建卷容器，然后创建卷。 （这些卷供文件服务器 VM 上的文件共享使用）。 创建卷时，请复制发起程序名称并为“访问控制记录”提供适当的名称。
   7. 选择“配置”选项卡并记下设备的 IP 地址。
   8. 在本地 VM 上，再次转到“iSCSI 发起程序”，然后在“快速连接”部分中输入 IP。 单击“快速连接”（设备现在应已连接）。
   9. 打开 Azure 管理门户，然后选择“卷和设备”选项卡。 单击“自动配置”。 应会出现刚刚创建的卷。
   10. 在门户中选择“设备”选项卡，然后选择“创建新虚拟设备”。 （此虚拟设备将在发生故障转移时使用）。 可将此新虚拟设备保持脱机状态，以免产生额外的费用。 若要使虚拟机脱机，请转到门户的“虚拟机”部分，然后关闭该虚拟机。
   11. 返回本地 VM 并打开磁盘管理（按 Windows 键 + X 并选择“磁盘管理”）。
   12. 可以看到一些附加的磁盘（数量取决于创建的卷数）。 右键单击第一个磁盘，选择“初始化磁盘”，然后选择“确定”。 右键单击“未分配”部分，选择“新建简单卷”，为卷分配一个盘符，然后完成向导操作。
   13. 针对所有磁盘重复步骤 I。 现在，可以在 Windows 资源管理器中的“此电脑”上看到所有磁盘。
   14. 使用文件和存储服务角色在这些卷上创建文件共享。

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>创建和准备 Azure Site Recovery 保管库
在保护文件服务器 VM 之前，请参阅 [Azure Site Recovery 文档](../site-recovery/site-recovery-hyper-v-site-to-azure.md)了解 Azure Site Recovery。

#### <a name="to-enable-protection"></a>启用保护
1. 通过 Azure Site Recovery 将 iSCSI 目标与要保护的本地 VM 断开连接：

   1. 按 Windows 键 + Q 并搜索 **iSCSI**。
   2. 选择“设置 iSCSI 发起程序”。
   3. 断开以前连接的 StorSimple 设备。 或者，可以在启用保护时将文件服务器关闭几分钟。

   > [!NOTE]
   > 这将导致文件共享暂时不可用
   >
   >
2. 通过 Azure Site Recovery 门户为文件服务器 VM [启用虚拟机保护](../site-recovery/site-recovery-hyper-v-site-to-azure.md#step-6-enable-replication)。
3. 初始同步开始时，可以再次重新连接目标。 转到 iSCSI 发起程序，选择 StorSimple 设备，然后单击“连接”。
4. 当同步完成且 VM 的状态为“受保护”时，请选择 VM，选择“配置”选项卡，然后相应地更新 VM 的网络（这是已故障转移的 VM 所属的网络）。 如果网络未显示，则表示同步仍在进行。

### <a name="enable-protection-of-storsimple-volumes"></a>保护 StorSimple 卷
如果没有为 StorSimple 卷选择“为此卷启用默认备份”选项，请转到 StorSimple Manager 服务中的“备份策略”，然后为所有卷创建适当的备份策略。 建议将备份频率设置为要查看的应用程序的恢复点目标 (RPO)。

### <a name="configure-the-network"></a>配置网络
对于文件服务器 VM，请在 Azure Site Recovery 中配置网络设置，使 VM 网络能够在故障转移之后连接到正确的 DR 网络。

可以在“VMM 云”或“保护组”中选择 VM 来配置网络设置，如下图中所示。

![](./media/storsimple-dr-using-asr/image2.png)

## <a name="create-a-recovery-plan"></a>创建恢复计划
可以在 ASR 中创建恢复计划，将文件共享的故障转移过程自动化。 发生中断时，只需按一下鼠标就能在几分钟内让文件共享重新联机。 若要启用此自动化功能，需要一个 Azure 自动化帐户。

#### <a name="to-create-the-account"></a>创建帐户
1. 转到 Azure 经典门户，然后转到“自动化”部分。
2. 创建新的自动化帐户。 将该帐户保留在创建 StorSimple Cloud Appliance 和存储帐户的同一个地理区域。
3. 单击“新建”&gt;“应用程序服务”&gt;“自动化”&gt;“Runbook”&gt;“从库中”，将所需的全部 Runbook 导入自动化帐户。

   ![](./media/storsimple-dr-using-asr/image3.png)
4. 通过“灾难恢复”窗格添加库中的以下 Runbook：

   * 故障转移 StorSimple 卷容器
   * 在测试故障转移 (TFO) 后清理 StorSimple 卷
   * 故障转移后在 StorSimple 设备上装载卷
   * 启动 StorSimple 虚拟设备
   * 在 Azure VM 中卸载自定义脚本扩展

     ![](./media/storsimple-dr-using-asr/image4.png)
5. 通过在自动化帐户中选择 Runbook 并转到“创作”选项卡来发布所有脚本。 完成此步骤后，“Runbook”选项卡将如下所示：

    ![](./media/storsimple-dr-using-asr/image5.png)
6. 在自动化帐户中转到“资产”选项卡，单击“添加设置”&gt;“添加凭据”，然后添加 Azure 凭据 – 将资产命名为 AzureCredential。

   使用 Windows PowerShell 凭据。 这应该是包含组织 ID、用户名和密码的凭据，它有权访问此 Azure 订阅并且已禁用多重身份验证。 只有满足此要求，才能在故障转移期间代表用户进行身份验证，使 DR 站点上的文件服务器卷启动。
7. 在自动化帐户中选择“资产”选项卡，单击“添加设置”&gt;“添加变量”，然后添加以下变量。 可以选择将这些资产加密。 这些变量特定于恢复计划。 如果恢复计划（将在下一步骤中创建）名为 TestPlan，则变量应该是 TestPlan-StorSimRegKey、TestPlan-AzureSubscriptionName，等等。

   * *RecoveryPlanName***-StorSimRegKey**：StorSimple Manager 服务的注册密钥。
   * *RecoveryPlanName***-AzureSubscriptionName**：Azure 订阅的名称。
   * *RecoveryPlanName***-ResourceName**：包含 StorSimple 设备的 StorSimple 资源的名称。
   * *RecoveryPlanName***-DeviceName**：必须故障转移的设备。
   * *RecoveryPlanName***-TargetDeviceName**：容器故障转移之后所在的 StorSimple Cloud Appliance。
   * *RecoveryPlanName***-VolumeContainers**：需故障转移的设备上显示的卷容器逗号分隔字符串，例如 volcon1、volcon2、volcon3。
   * RecoveryPlanName**-TargetDeviceDnsName**：目标设备的服务名称（可以在“虚拟机”部分中找到：服务名称与 DNS 名称相同）。
   * *RecoveryPlanName***-StorageAccountName**：存储脚本（必须在已故障转移的 VM 上运行）的存储帐户名。 可以是具有一些空间暂时存储脚本的任何存储帐户。
   * *RecoveryPlanName***-StorageAccountKey**：上述存储帐户的访问密钥。
   * *RecoveryPlanName***-ScriptContainer**：存储脚本的云容器的名称。 如果该容器不存在，系统会创建该容器。
   * *RecoveryPlanName***-VMGUIDS**：保护 VM 时，Azure Site Recovery 将为每个 VM分配唯一 ID，该 ID 可提供已故障转移的 VM 的详细信息。 若要获取 VMGUID，请选择“恢复服务”选项卡，然后单击“受保护的项”&gt;“保护组”&gt;“计算机”&gt;“属性”。 如果有多个 VM，请以逗号分隔字符串的形式添加 GUID。
   * *RecoveryPlanName***-AutomationAccountName** – 已在其中添加 Runbook 和资产的自动化帐户的名称。

   例如，如果恢复计划名称为 fileServerpredayRP，则添加所有资产后，“资产”选项卡应如下所示。

   ![](./media/storsimple-dr-using-asr/image6.png)
8. 转到“恢复服务”部分，然后选择前面创建的 Azure Site Recovery 保管库。
9. 选择“恢复计划”选项卡并创建新恢复计划，如下所示：

   a.  指定名称并选择相应的**保护组**。

   b.  从保护组中选择要包含在恢复计划中的 VM。

   c.  创建恢复计划后，请选择该恢复计划打开恢复计划自定义视图。

   d.  选择“所有已关闭的组”，单击“脚本”，然后选择“在所有已关闭的组前面添加主端脚本”。

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。  选择自动化帐户（在其中添加了 Runbook 的帐户），然后选择“Fail over-StorSimple-Volume-Containers”Runbook。

   f.  单击“组 1: 启动”，选择“虚拟机”，然后添加要在恢复计划中保护的 VM。

   g.  单击“组 1: 启动”，选择“脚本”，然后根据“组 1 后面”步骤的顺序添加以下所有脚本。

   * Start-StorSimple-Virtual-Appliance Runbook
   * Fail over-StorSimple-volume-containers Runbook
   * Mount-volumes-after-failover Runbook
   * Uninstall-custom-script-extension Runbook
10. 在同一个“组 1: 后续步骤”部分中，在上面 4 个脚本后面添加一个手动操作。 通过此操作可以确认所有配置是否正常运行。 只需为测试故障转移添加此操作（因此选中“测试故障转移”复选框即可）。
11. 执行手动操作后，使用针对其他 Runbook 执行的相同过程添加 Cleanup 脚本。 保存恢复计划。

    > [!NOTE]
    > 运行测试故障转移时，应在手动操作步骤中验证所有配置，因为在目标设备上克隆的 StorSimple 卷将在手动操作完成后执行清理的过程中被删除。
    >
    >

    ![](./media/storsimple-dr-using-asr/image7.png)

## <a name="perform-a-test-failover"></a>执行测试故障转移
请参阅 [Active Directory DR 解决方案](../site-recovery/site-recovery-active-directory.md)随附的指南，了解在测试故障转移期间有关 Active Directory 的具体注意事项。 执行测试故障转移时，本地设置不受干扰。 已附加到本地 VM 的 StorSimple 卷将克隆到 Azure 上的 StorSimple Cloud Appliance。 Azure 中用于测试的 VM 将会启动，云卷将附加到 VM。

#### <a name="to-perform-the-test-failover"></a>执行测试故障转移
1. 在 Azure 经典门户中，选择你的 Site Recovery 保管库。
2. 单击为文件服务器 VM 创建的恢复计划。
3. 单击“测试故障转移”。
4. 选择虚拟网络开始测试故障转移过程。

   ![](./media/storsimple-dr-using-asr/image8.png)
5. 当辅助环境启动时，可以执行验证。
6. 完成验证后，单击“验证完成”。 系统将清理测试故障转移环境并完成 TFO 操作。

## <a name="perform-an-unplanned-failover"></a>执行计划外故障转移
在计划外故障转移期间，StorSimple 卷将故障转移到虚拟设备，副本 VM 将在 Azure 上启动，卷将附加到 VM。

#### <a name="to-perform-an-unplanned-failover"></a>执行计划外故障转移
1. 在 Azure 经典门户中，选择你的 Site Recovery 保管库。
2. 单击为文件服务器 VM 创建的恢复计划。
3. 单击“故障转移”，然后选择“计划外故障转移”。

   ![](./media/storsimple-dr-using-asr/image9.png)
4. 选择目标网络，然后单击勾选图标 ✓ 开始故障转移过程。

## <a name="perform-a-planned-failover"></a>执行计划内故障转移
在计划内故障转移期间，本地文件服务器 VM 将正常关闭，并为 StorSimple 设备上的卷创建云备份快照。 StorSimple 卷将故障转移到虚拟设备，副本 VM 将在 Azure 上启动，卷将附加到 VM。

#### <a name="to-perform-a-planned-failover"></a>执行计划内故障转移
1. 在 Azure 经典门户中，选择你的 Site Recovery 保管库。
2. 单击为文件服务器 VM 创建的恢复计划。
3. 单击“故障转移”，然后选择“计划内故障转移”。
4. 选择目标网络，然后单击勾选图标 ✓ 开始故障转移过程。

## <a name="perform-a-failback"></a>执行故障回复
在故障回复期间，StorSimple 卷容器在创建备份之后故障回复到物理设备。

#### <a name="to-perform-a-failback"></a>执行故障回复
1. 在 Azure 经典门户中，选择你的 Site Recovery 保管库。
2. 单击为文件服务器 VM 创建的恢复计划。
3. 单击“故障转移”，然后选择“计划内故障转移”或“计划外故障转移”。
4. 单击“更改方向”。
5. 选择适当的数据同步和 VM 创建选项。
6. 单击勾选图标 ✓ 开始故障回复过程。

   ![](./media/storsimple-dr-using-asr/image10.png)

## <a name="best-practices"></a>最佳实践
### <a name="capacity-planning-and-readiness-assessment"></a>容量规划和准备情况评估
#### <a name="hyper-v-site"></a>Hyper-V 站点
使用 [User Capacity Planner 工具](http://www.microsoft.com/download/details.aspx?id=39057)为 Hyper-V 副本环境设计服务器、存储和网络基础结构。

#### <a name="azure"></a>Azure
可以在 VM 上运行 [Azure 虚拟机准备情况评估工具](http://azure.microsoft.com/downloads/vm-readiness-assessment/)，确保这些 VM 与 Azure VM 和 Azure Site Recovery 服务兼容。 准备情况评估工具将检查 VM 配置，当配置与 Azure 不兼容时会发出警告。 例如，如果 C: 磁盘大小超过 127 GB，它就会发出警告。

容量规划至少包括两个重要过程：

* 将本地 Hyper-V VM 映射为 Azure VM 大小（例如 A6、A7、A8 和 A9）。
* 确定所需的 Internet 带宽。

## <a name="limitations"></a>限制
* 目前只能故障转移 1 台 StorSimple 设备（故障转移到单个 StorSimple Cloud Appliance）。 不支持跨越多台 StorSimple 设备的文件服务器方案。
* 如果在为 VM 启用保护时发生错误，请确保已断开连接 iSCSI 目标。
* 由于备份策略跨越不同卷容器而分组在一起的所有卷容器将一同故障转移。
* 在卷容器中选择的所有卷将故障转移。
* 总大小超过 64 TB 的卷无法故障转移，因为单个 StorSimple Cloud Appliance 的容量上限为 64 TB。
* 如果计划内/计划外故障转移失败并且 Azure 中已创建 VM，请不要清理 VM， 而应该执行故障回复。 如果删除了 VM，则无法再次打开本地 VM。
* 故障转移后如果看不到卷，请转到 VM，打开“磁盘管理”，重新扫描磁盘，然后将卷联机。
* 在某些情况下，DR 站点中的盘符可能与本地盘符不同。 如果发生此情况，需要在故障转移完成后手动更正问题。
* 应该针对在自动化帐户中作为资产输入的 Azure 凭据禁用多重身份验证。 如果不禁用这种身份验证，脚本将无法自动运行，恢复计划将会失败。
* 故障转移作业超时：如果故障转移卷容器花费的时间超过每个脚本的 Azure Site Recovery 限制（目前为 120 分钟），StorSimple 脚本将会超时。
* 备份作业超时：如果备份卷花费的时间超过每个脚本的 Azure Site Recovery 限制（目前为 120 分钟），StorSimple 脚本将会超时。

  > [!IMPORTANT]
  > 请从 Azure 门户手动运行备份，然后再次运行恢复计划。
  >
  >
* 克隆作业超时：如果克隆卷花费的时间超过每个脚本的 Azure Site Recovery 限制（目前为 120 分钟），StorSimple 脚本将会超时。
* 时间同步错误：StorSimple 脚本出错，指出尽管在门户中备份成功，但事实上备份失败。 发生此问题的可能原因是 StorSimple 设备的时间没有与时区中的当前时间同步。

  > [!IMPORTANT]
  > 请将设备时间与时区中的当前时间同步。
  >
  >
* 设备故障转移错误：如果运行恢复计划时进行设备故障转移，StorSimple 脚本可能会失败。

  > [!IMPORTANT]
  > 请在设备故障转移完成后重新运行恢复计划。
  >
  >

## <a name="summary"></a>摘要
使用 Azure Site Recovery，可以针对在 StorSimple 存储中托管文件共享的文件服务器 VM 创建完整的自动化灾难恢复计划。 发生服务中断时，可以在数秒内从任何位置启动故障转移，在数分钟内启动和运行应用程序。



<!--HONumber=Jan17_HO4-->


