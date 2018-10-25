---
title: 使用 Azure Site Recovery 将 Azure Stack VM 复制到 Azure | Microsoft Docs
description: 了解如何使用 Azure Site Recovery 服务为 Azure Stack VM 设置到 Azure 的灾难恢复。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 10/09/2018
ms.author: raynew
ms.openlocfilehash: 802c2223a72a89dbe2a97404aab4b0fc85c391ed
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902818"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>将 Azure Stack VM 复制到 Azure

本文介绍如何使用 [Azure Site Recovery 服务](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)为 Azure Stack VM 设置到 Azure 的灾难恢复。

Site Recovery 有助于实现业务连续性和灾难恢复 (BCDR) 策略。 该服务可确保在出现预期内和意外中断时，VM 工作负载仍然可用。

- Site Recovery 可协调并管理 VM 到 Azure 存储的复制。
- 主站点出现中断时，可使用 Site Recovery 进行到 Azure 的故障转移。
- 进行故障转移时，会根据存储的 VM 数据创建 Azure VM，用户可继续访问在这些 Azure VM 上运行的工作负载。
- 一切恢复正常运行后，可将 Azure VM 故障回复到主站点，然后再次开始复制到 Azure 存储。


在本文中，学习如何：

> [!div class="checklist"]
> * **步骤 1：做好复制 Azure Stack 的准备**。 检查 VM 是否符合 Site Recovery 要求，并准备安装 Site Recovery 移动服务。 此服务安装在要复制的每个 VM 上。
> * **步骤 2：设置恢复服务保管库**。 为 Site Recovery 设置保管库，并指定要复制的内容。 在保管库中配置和管理 Site Recovery 的组件和操作。
> * **步骤 3：设置源复制环境**。 设置 Site Recovery 配置服务器。 配置服务器是单个 Azure Stack VM，可运行 Site Recovery 需要的所有组件。 设置配置服务器后，在保管库中进行注册。
> * **步骤 4：设置目标复制环境**。 选择 Azure 帐户以及要使用的 Azure 存储帐户和网络。 复制期间，会将 VM 数据复制到 Azure 存储。 进行故障转移后，Azure VM 会加入指定的网络。
> * **步骤 5：启用复制**。 配置复制设置，启用 VM 复制。 启用复制后，会在 VM 上安装移动服务。 Site Recovery 执行 VM 的初始复制，然后开始持续复制。
> * **步骤 6：运行灾难恢复演练**：复制正常运行后，可运行演练来验证故障转移是否按预期工作。 要启动演练，请在 Site Recovery 中运行测试故障转移。 测试故障转移不会对生产环境造成任何影响。

完成这些步骤后，即可按需随时运行到 Azure 的完全故障转移。

## <a name="architecture"></a>体系结构

![体系结构](./media/azure-stack-site-recovery/architecture.png)

**位置** | 组件 |**详细信息**
--- | --- | ---
**配置服务器** | 在单个 Azure Stack VM 上运行。 | 在每个订阅中都可以设置配置服务器 VM。 此 VM 运行以下 Site Recovery 组件：<br/><br/> - 配置服务器：在本地和 Azure 之间协调通信并管理数据复制。 - 进程服务器：充当复制网关。 它接收复制数据，通过缓存、压缩和加密对其进行优化，然后将数据发送到 Azure 存储。<br/><br/> 如果要复制的 VM 超出了下述限制，则可设置单独的独立进程服务器。 [了解详细信息](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-process-server-scale)。
**移动服务** | 会安装在要复制的每个 VM 上。 | 在本文所述步骤中，我们准备了一个帐户，以便复制启用后自动在 VM 上安装移动服务。 如果不想自动安装该服务，则可使用许多其他方法。 [了解详细信息](https://docs.microsoft.com/azure/site-recovery/vmware-azure-install-mobility-service)。
**Azure** | 在 Azure 中，你需要一个恢复服务保管库、一个存储帐户和一个虚拟网络。 |  复制的数据存储在存储帐户中。 进行故障转移时，Azure VM 将添加到 Azure 网络。 


复制按如下方式进行：

1. 在保管库中，指定复制源和目标，设置配置服务器，创建复制策略并启用复制。
2. 移动服务会安装到计算机（如果已使用推送安装），并且计算机会根据复制策略开始复制。
3. 服务器数据的初始副本将复制到 Azure 存储。
4. 完成初始复制后，开始将增量更改复制到 Azure。 计算机的受跟踪更改保存在 .hrl 文件中。
5. 配置服务器可协调与 Azure（HTTPS 443 出站端口）的复制管理。
6. 进程服务器从源计算机接收数据、优化和加密数据，然后将其发送到 Azure 存储（443 出站端口）。
7. 复制的计算机与配置服务器通信（HTTPS 443 入站端口，用于复制管理）。 计算机将复制数据发送到进程服务器（HTTPS 9443 入站端口，可修改）。
8. 流量通过 Internet 复制到 Azure 存储公共终结点。 或者，可以使用 Azure ExpressRoute 公共对等互连。 不支持通过站点到站点 VPN 将流量从本地站点复制到 Azure。

## <a name="prerequisites"></a>先决条件

下面是设置此方案所需的项。

要求 | **详细信息**
--- | ---
**Azure 订阅帐户** | 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。
**Azure 帐户权限** | 使用的 Azure 帐户需以下权限：<br/><br/> - 创建恢复服务保管库<br/><br/> - 在用于方案的资源组和虚拟网络中创建虚拟机<br/><br/> - 向指定的存储帐户进行写入<br/><br/> 请注意：<br/><br/> - 如果创建帐户，则你是自己的订阅的管理员，可以执行所有操作。<br/><br/> - 如果你使用现有订阅并且不是管理员，则需要请求管理员为你分配“所有者”或“参与者”权限。<br/><br/> - 如需更加细化的权限，请查看[此文](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control)。 
**Azure Stack VM** | 需要租户订阅中的 Azure Stack VM，该 VM 将部署为 Site Recovery 配置服务器。 


### <a name="prerequisites-for-the-configuration-server"></a>配置服务器的先决条件

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]


 
## <a name="step-1-prepare-azure-stack-vms"></a>步骤 1：准备 Azure Stack VM

### <a name="verify-the-operating-system"></a>验证操作系统

确保 VM 正在运行的是表中列出的操作系统。


**操作系统** | **详细信息**
--- | ---
**64 位 Windows** | Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2（自 SP1 起）
**CentOS** | 5.2 到 5.11、6.1 到 6.9、7.0 到 7.3
**Ubuntu** | 14.04 LTS 服务器、16.04 LTS 服务器。 查看[支持的内核](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>准备安装移动服务

要复制的所有 VM 都必须安装移动服务。 为使进程服务器在复制启用后自动将该服务安装到 VM 上，请验证 VM 设置。

#### <a name="windows-machines"></a>Windows 计算机

- 需在要启用复制的 VM 与运行进程服务器（默认情况下，此为配置服务器 VM）的计算机之间建立网络连接。
- 在启用复制的计算机上，需要有具有管理员权限的帐户（域或本地）。
    - 可在设置 Site Recovery 时指定此帐户。 然后，在复制启用后，进程服务器使用此帐户安装移动服务。
    - 此帐户仅供 Site Recovery 用于推送安装，并用于更新移动服务。
    - 如果使用的不是域帐户，则需在 VM 上禁用远程用户访问控制：
        - 在注册表中的 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 下，创建 DWORD 值 LocalAccountTokenFilterPolicy。
        - 将值设置为 1。
        - 若要在命令提示符下执行此操作，请键入以下内容：REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1。
- 在要复制的 VM 上的 Windows 防火墙中，允许“文件和打印机共享”以及 WMI。
    - 若要执行此操作，请运行 wf.msc 打开 Windows 防火墙控制台。 依次右键单击“入站规则” > “新建规则”。 选择“预定义”，然后从列表中选择“文件和打印机共享”。 完成向导，选择以允许连接，然后单击“完成”。
    - 对于域计算机，可使用 GPO 来执行此操作。

    
#### <a name="linux-machines"></a>Linux 计算机

- 确保 Linux 计算机与进程服务器之间已建立网络连接。
- 在启用了复制的计算机上，需要具有源 Linux 服务器的根用户帐户：
    - 可在设置 Site Recovery 时指定此帐户。 然后，在复制启用后，进程服务器使用此帐户安装移动服务。
    - 此帐户仅供 Site Recovery 用于推送安装，并用于更新移动服务。
- 确保源 Linux 服务器上的 /etc/hosts 文件包含用于将本地主机名映射到所有网络适配器关联的 IP 地址的条目。
- 在要复制的计算机上安装最新的 openssh、openssh-server 和 openssl 包。
- 确保安全外科 (SSH) 已启用且正在端口 22 上运行。
- 启用 sshd_config 文件中的 SFTP 子系统和密码身份验证：
    1. 为此，请以根用户身份登录。
    2. 在 /etc/ssh/sshd_config 文件中，找到以“PasswordAuthentication”开头的行。 取消注释该行，并将值更改为 **yes**。
    3. 找到以 **Subsystem** 开头的行，并取消注释该行。

        ![Linux 移动服务](./media/azure-stack-site-recovery/linux-mobility.png)

    4. 重启 sshd 服务。


### <a name="note-the-vm-private-ip-address"></a>记下 VM 专用 IP 地址

找到要复制的每台计算机的 IP 地址：

1. 在 Azure Stack 门户中，单击 VM。
2. 在“资源”菜单上，单击“网络接口”。
3. 记下专用 IP 地址。

    ![专用 IP 地址](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>第 2 步：创建保管库并选择复制目标

1. 在 Azure 门户中，选择“创建资源” > “监视 + 管理” > “备份和站点恢复”。
2. 在“名称”中，输入一个友好名称以标识此保管库。 
3. 在“资源”组中，创建或选择资源组。 我们将使用 contosoRG。
4. 在“位置”中，输入 Azure 区域。 我们将使用“西欧”。
5. 若要从仪表板快速访问保管库，请选择“固定到仪表板” > “创建”。

   ![创建新的保管库](./media/azure-stack-site-recovery/new-vault-settings.png)

   新保管库显示在“仪表板” > “所有资源”中，以及“恢复服务保管库”主页上。

### <a name="select-a-replication-goal"></a>选择复制目标

1. 在“恢复服务保管库”中，指定保管库名称。 我们将使用 ContosoVMVault。
2. 在“入门”中，选择“Site Recovery”， 然后选择“准备基础结构”。
3. 在“保护目标” > “计算机所在位置”中，选择“本地”。
4. 在“要将计算机复制到何处?”中，选择“复制到 Azure”。
5. 在“计算机是否已虚拟化”中，选择“尚未虚拟化/其他”。 然后选择“确定”。

    ![保护目标](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>步骤 3：设置源环境

设置配置服务器计算机，在保管库中进行注册，并找到要复制的计算机。

1. 单击“准备基础结构” > “源”。
2. 在“准备源”中，单击“+配置服务器”。

    ![设置源](./media/azure-stack-site-recovery/plus-config-srv.png)

3. 在“添加服务器”中，检查“配置服务器”是否已显示在“服务器类型”中。
5. 下载站点恢复统一安装程序安装文件。
6. 下载保管库注册密钥。 运行统一安装程序时，需要注册密钥。 生成的密钥有效期为 5 天。

    ![设置源](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>运行 Azure Site Recovery 统一安装程序

若要安装并注册配置服务器，请与要用于配置服务器的 VM 建立 RDP 连接，然后运行统一安装程序。

开始操作之前， [请务必将时钟与 VM 上的](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)时间服务器同步。 如果时间与当地时间误差超过五分钟，则安装失败。

现在来安装配置服务器：

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> 还可通过命令行安装配置服务器。 [了解详细信息](http://aka.ms/installconfigsrv)。

> 帐户名出现在门户中可能需要 15 分钟或更长时间。 若要立即更新，请选择“配置服务器” > ***服务器名称*** > “刷新服务器”。

## <a name="step-4-set-up-the-target-environment"></a>步骤 4：设置目标环境

选择并验证目标资源。

1. 在“准备基础结构” > “目标”中，选择要使用的 Azure 订阅。
2. 指定目标部署模型。
3. Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。 如果未找到，则需创建至少一个存储帐户和虚拟网络，方可完成向导。


## <a name="step-5-enable-replication"></a>第 5 步：启用复制

### <a name="create-a-replication-policy"></a>创建复制策略

1. 依次单击“准备基础结构” > “复制设置”。
2. 在“创建复制策略”中指定策略名称。
3. 在“RPO 阈值”中，指定恢复点目标 (RPO) 限制。
    - 会根据设置的时间创建复制数据的恢复点。
    - 此设置不会影响持续复制。 如果在未创建恢复点的情况下达到阈值限制，则会发出警报。
4. 在“恢复点保留期”中，指定每个恢复点的保留时长。 可将复制的 VM 恢复到指定的时间窗口中的任何点。
5. 在“应用一致性快照频率”中，指定创建应用程序一致性快照的频率。

    - “应用一致”快照是 VM 内应用数据的时间点快照。
    - 卷影复制服务 (VSS) 确保 VM 上的应用在创建快照时处于一致状态。
6. 选择“确定”以创建策略。


### <a name="confirm-deployment-planning"></a>确认部署规划

可立即跳过此步骤。 在“部署规划”下拉列表中，单击“是，我已完成”。



### <a name="enable-replication"></a>启用复制

请确保已完成[步骤1：准备计算机](#step-1-prepare-azure-stack-vms)中的所有任务。 随后请按如下所述启用复制：

1. 选择“复制应用程序” > “源”。
2. 在“源”中选择配置服务器。
3. 在“计算机类型”中，选择“物理计算机”。
4. 选择进程服务器（配置服务器）。 然后单击“确定”。
5. 在“目标”中，选择故障转移后要在其中创建 VM 的订阅和资源组。 选择要用于故障转移的 VM 的部署模型。
6. 选择要在其中存储复制的数据的 Azure 存储帐户。
7. 选择 Azure VM 在故障转移后创建时所要连接的 Azure 网络和子网。
8. 选择“立即为选定的计算机配置”，将网络设置应用到选择保护的所有计算机。 如需为每台计算机单独选择 Azure 网络，请选择“稍后配置”。
9. 在“物理计算机”中，单击“+物理计算机”。 指定每台计算机的 IP 地址名称以及要复制的操作系统。

    - 使用计算机的内部 IP 地址。
    - 如果指定公共 IP 地址，则复制可能无法按预期进行。

10. 在“属性” > “配置属性”中，选择进程服务器在计算机上自动安装移动服务时使用的帐户。
11. 在“复制设置” > “配置复制设置”中，检查是否选择了正确的复制策略。
12. 单击“启用复制”。
13. 在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。

> [!NOTE]
> 为 VM 启用复制后，Site Recovery 会安装移动服务。

> 可能要等 15 分钟或更长时间，更改才会生效并显示在门户中。

> 若要监视添加的 VM，请在“配置服务器” > “上次联系时间”中查看上次发现 VM 的时间。 若要添加 VM 而不想要等待计划的发现，请突出显示配置服务器（不要选择它），然后选择“刷新”。


## <a name="step-6-run-a-disaster-recovery-drill"></a>步骤 6：运行灾难恢复演练

运行到 Azure 的测试故障转移，以确保一切如预期正常运行。 此故障转移不会影响生产环境。

### <a name="verify-machine-properties"></a>验证计算机属性

运行测试故障转移前，请验证计算机属性，确保其符合 [Azure 要求](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements)。 可按如下方式查看和修改属性：

1. 在“受保护的项”中，单击“复制的项”>“虚拟机”。
2. “复制的项”窗格中具有 VM 信息、运行状况状态和最新可用恢复点的摘要。 单击“属性”可查看更多详细信息。
3. 在“计算和网络”中，按需修改设置。

    - 可修改 Azure 名称、资源组、目标大小、[可用性集](../virtual-machines/windows/tutorial-availability-sets.md)和托管的磁盘设置。
    - 还可查看和修改网络设置。 其中包括故障转移后 Azure VM 加入的网络/子网，以及将分配给 VM 的 IP 地址。
1. 在“磁盘”中，可查看关于 VM 上的操作系统和数据磁盘的信息。
   

### <a name="run-a-test-failover"></a>运行测试故障转移

运行测试故障转移时需执行下列操作：

1. 运行必备项检查，确保故障转移所需的所有条件都已就绪。
2. 故障转移使用指定的恢复点处理数据：
    - **最新处理**：计算机故障转移到由 Site Recovery 处理的最新恢复点。 将显示时间戳。 使用此选项时，无需费时处理数据，因此 RTO（恢复时间目标）会较低。
    - **最新的应用一致**：计算机故障转移到最新的应用一致恢复点。
    - **自定义**： 选择用于故障转移的恢复点。

3. 会使用已处理的数据创建 Azure VM。
4. 测试故障转移可自动清理在演练期间创建的 Azure VM。

按如下方式为 VM 运行测试故障转移：

1. 在“设置” > “复制的项”中，单击“VM”>“+测试故障转移”。
2. 在本演练中，我们将选择使用“最新处理”恢复点。 
3. 在“测试故障转移”中，选择目标 Azure 网络。
4. 单击“确定”开始故障转移。
5. 可通过单击 VM 打开其属性来跟踪进度。 或者，可在保管库名称  > “设置” > “作业” >“Site Recovery 作业”中单击“测试故障转移”作业。
6. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户 >“虚拟机”中。 检查 VM 大小是否合适、是否已连接到正确的网络且正在运行。
7. 现在应该能够连接到 Azure 中复制的 VM。 [了解详细信息](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover)。
8. 若要删除在测试故障转移期间创建的 Azure VM，请在 VM 上单击“清理测试故障转移”。 在“说明”中，保存与测试性故障转移相关联的任何观测结果。

## <a name="fail-over-and-fail-back"></a>故障转移和故障回复

设置复制后，运行演练以确保一切正常，之后则可按需将计算机故障转移到 Azure。

运行故障转移前，如果要在故障转移后连接 Azure 中的计算机，则可在开始前，[准备进行连接](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover)。

然后按如下所述运行故障转移：


1. 在“设置” > “复制的项”中，单击计算机 >“故障转移”。
2. 选择要使用的恢复点。
3. 在“测试故障转移”中，选择目标 Azure 网络。
4. 选择“在开始故障转移前关闭计算机”。 选择此设置后，Site Recovery 会在开始故障转移前尝试关闭源计算机。 但即使关机失败，故障转移也仍会继续。 
5. 单击“确定”开始故障转移。 可以在“作业”页上跟踪故障转移进度。
6. 故障转移完成后，副本 Azure VM 会显示在 Azure 门户 >“虚拟机”中。 如果打算在故障转移后进行连接，请检查 VM 大小是否合适、是否已连接到正确的网络且正在运行。
7. 验证 VM 后，单击“提交”完成故障转移。 这会删除所有可用的恢复点。

> [!WARNING]
> 请勿取消正在进行的故障转移：在故障转移开始前，停止 VM 复制。 如果取消正在进行的故障转移，故障转移会停止，但 VM 将不再进行复制。


### <a name="fail-back-to-azure-stack"></a>故障回复到 Azure Stack

主站点恢复正常运行后，可从 Azure 故障回复到 Azure Stack。 若要进行此操作，需下载 Azure VM VHD，并将其上传到 Azure Stack。

1. 关闭 Azure VM，以便可下载 VHD。 
2. 若要开始下载 VHD，请安装 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。
3. 导航到 Azure 门户中的 VM（使用 VM 名称）。
4. 在“磁盘”中，单击磁盘名称，然后收集设置。

    - 例如，我们的测试中使用的 VHD URI：可将 https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd 分解，获得用于下载 VHD 的以下输入参数。
        - 存储帐户：502055westcentralus
        - 容器：wahv9b8d2ceb284fb59287
        - VHD 名称：copied-3676553984.vhd

5. 现在请使用 Azure 存储资源管理器下载 VHD。
6. 按照[这些步骤](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm)将 VHD 上传到 Azure Stack。
7. 在现有 VM 或新 VM 中，附加上传的 VHD。
8. 检查 OS 磁盘是否正确，并启动 VM。


此时，故障回复完成。


## <a name="conclusion"></a>结束语

在本文中，我们将 Azure Stack VM 复制到了 Azure。 通过复制，我们运行了灾难恢复演练，以确保到 Azure 的故障转移按预期工作。 本文还介绍了如何运行到 Azure 的完全故障转移，以及到 Azure Stack 的故障回复。

## <a name="next-steps"></a>后续步骤

故障回复后，可重新保护 VM 并再次开始将其复制到 Azure。若要执行此操作，请重复本文中的步骤。

