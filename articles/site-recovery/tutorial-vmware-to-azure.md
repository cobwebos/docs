---
title: "使用 Azure Site Recovery 针对本地 VMware VM 设置到 Azure 的灾难恢复 | Microsoft 文档"
description: "了解如何使用 Azure Site Recovery 服务针对本地 VMware VM 设置到 Azure 的灾难恢复。"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ee445c8af2fc6620385d9c462d4c6551da3d7367
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-vmware-vms"></a>针对本地 VMware VM 设置到 Azure 的灾难恢复

本教程演示如何针对运行 Windows 的本地 VMware VM 设置到 Azure 的灾难恢复。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 为 Site Recovery 创建恢复服务保管库
> * 设置源和目标复制环境
> * 创建复制策略
> * 为虚拟机启用复制

此教程为系列教程中的第三个教程。 本教程假设你已完成前面教程中的以下任务：

1. [准备 Azure](tutorial-prepare-azure.md)
2. [准备本地 VMware](tutorial-prepare-on-premises-vmware.md)

在开始之前，[查看灾难恢复方案的体系结构](concepts-vmware-to-azure-architecture.md)会有所帮助。

## <a name="configure-vmware-account-permissions"></a>配置 VMware 帐户权限

1. 在 vCenter 级别创建角色。 为该角色指定名称 Azure_Site_Recovery。
2. 将以下权限分配到 Azure_Site_Recovery 角色。

   **任务** | **角色/权限** | **详细信息**
   --- | --- | ---
   **VM 发现** | 数据中心对象 –> 传播到子对象、角色=只读 | 至少一个只读用户。<br/><br/> 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。
   **完全复制、故障转移、故障回复** |  数据中心对象 – 传播到子对象、角色=Azure_Site_Recovery<br/><br/> 数据存储->分配空间、浏览数据存储、低级别文件操作、删除文件、更新虚拟机文件<br/><br/> 网络 -> 网络分配<br/><br/> 资源 -> 将 VM 分配到资源池、迁移关闭的 VM、迁移打开的 VM<br/><br/> 任务 -> 创建任务、更新任务<br/><br/> 虚拟机 -> 配置<br/><br/> 虚拟机 -> 交互 -> 回答问题、设备连接、配置 CD 媒体、配置软盘媒体、关闭电源、打开电源、安装 VMware 工具<br/><br/> 虚拟机 -> 清单 -> 创建、注册、取消注册<br/><br/> 虚拟机 -> 预配 -> 允许虚拟机下载、允许虚拟机文件上传<br/><br/> 虚拟机 -> 快照 -> 删除快照 | 在数据中心级别分配的对数据中心内所有对象具有访问权限的用户。<br/><br/> 要限制访问权限，请在选中“传播到子对象”的情况下将“无访问权”角色分配给子对象（vSphere 主机、数据存储、VM 和网络）。

3. 在 vCenter 服务器或 vSphere 主机上创建一个用户。 向该用户分配角色。

## <a name="specify-what-you-want-to-replicate"></a>指定要复制的内容

必须在要复制的每个 VM 上安装 Mobility Service。 为 VM 启用复制后，Site Recovery 会自动安装此服务。 为了实现自动安装，你需要准备一个由 Site Recovery 用于访问 VM 的帐户。

可以使用域或本地帐户。 对于 Linux VM，该帐户应是源 Linux 服务器上的根帐户。 对于 Windows VM，如果使用的不是域帐户，则在本地计算机上禁用远程用户访问控制：

  - 在注册表的 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 下，添加 DWORD 项 LocalAccountTokenFilterPolicy，并将值设为 1。

## <a name="set-up-the-source-environment"></a>设置源环境

设置源环境包括下载 Site Recovery 统一安装程序、设置配置服务器并将其注册到保管库中，以及发现 VM。

配置服务器是一个用于承载所有 Site Recovery 组件的本地 VMware VM。 此 VM 运行配置服务器、进程服务器和主目标服务器。

- 配置服务器在本地和 Azure 之间协调通信并管理数据复制。
- 进程服务器充当复制网关。 接收复制数据，通过缓存、压缩和加密对其进行优化，然后将数据发送到 Azure 存储。 进程服务器还会将移动服务安装在要复制的 VM 上，并在本地 VMware 服务器上执行 VM 的自动发现。
- 主目标服务器处理从 Azure 进行故障回复期间产生的复制数据。

配置服务器 VM 应该是满足以下要求的高可用性 VMware VM：

| **要求** | **详细信息** |
|-----------------|-------------|
| CPU 内核数| 8 |
| RAM | 12 GB |
| 磁盘数目 | 3 - 操作系统磁盘、进程服务器缓存磁盘、保留驱动器（用于故障回复） |
| 磁盘可用空间（进程服务器缓存） | 600 GB |
| 磁盘可用空间（保留磁盘） | 600 GB |
| 操作系统版本 | Windows Server 2012 R2 |
| 操作系统区域设置 | 美国英语 |
| VMware vSphere PowerCLI 版本 | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") |
| Windows Server 角色 | 不要启用这些角色：Active Directory 域服务、Internet Information Services、HYPER-V |
| NIC 类型 | VMXNET3 |
| IP 地址类型 | 静态 |
| 端口 | 443（控制通道协调）<br/>9443（数据传输）|

在配置服务器 VM 上，确保将系统时钟与时间服务器进行同步。
时间必须在 15 分钟内得到同步。 如果时间差大于 15 分钟，则安装程序将失败。

请确保配置服务器 VM 可以访问以下 URL：

- *.accesscontrol.windows.net。 用于访问控制和标识管理。
- *.backup.windowsazure.com。用于复制数据传输和协调。
- *.blob.core.windows.net。 用于访问存储所复制数据的存储帐户。
- *.hypervrecoverymanager.windowsazure.com。用于复制管理操作和协调。
- time.nist.gov and time.windows.com。用于检查系统时间与全球时间之间的时间同步。

Azure 政府云的 URL：

- *.ugv.hypervrecoverymanager.windowsazure.us
- *.ugv.backup.windowsazure.us
- *.ugi.hypervrecoverymanager.windowsazure.us
- *.ugi.backup.windowsazure.us

任何基于 IP 地址的防火墙规则应允许与 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=41653)，以及端口 443 (HTTPS) 和 9443（数据复制）进行通信。 确保允许订阅的 Azure 区域的 IP 地址范围，以及美国西部的 IP 地址范围（用于访问控制和标识管理）。

### <a name="download-the-site-recovery-unified-setup"></a>下载 Site Recovery 统一安装程序

1. 打开 [Azure 门户](https://portal.azure.com)，然后单击“所有资源”。
2. 单击名为 ContosoVMVault 的恢复服务保管库。
3. 单击“Site Recovery” > “准备基础结构” > “保护目标”。
4. 将计算机所在的位置选择为“本地”，将想要复制计算机的位置选择为“到 Azure”，然后选择“是，使用 VMware vSphere 虚拟机监控程序”。 然后单击“确定”。
5. 在“准备源”面板中，单击“+配置服务器”。
6. 在“添加服务器”中，检查“配置服务器”是否已显示在“服务器类型”中。
7. 下载站点恢复统一安装程序安装文件。
8. 下载保管库注册密钥。 运行统一安装程序时需要用到此密钥。 生成的密钥有效期为 5 天。

   ![设置源](./media/tutorial-vmware-to-azure/source-settings.png)

### <a name="set-up-the-configuration-server"></a>设置配置服务器

1. 运行统一安装程序安装文件。
2. 在“开始之前”中，选择“安装配置服务器和进程服务器”，然后单击“下一步”。

3. 在“第三方软件许可证”中，单击“我接受”，下载并安装 MySQL，然后单击“下一步”。

4. 在“注册”中，选择从保管库下载的注册密钥。

5. 在“Internet 设置”中，指定配置服务器上运行的提供程序如何通过 Internet 连接到 Azure Site Recovery。

   - 如果想要使用当前已在计算机上设置的代理进行连接，请选择“使用代理服务器连接到 Azure Site Recovery”。
   - 如果希望提供程序直接进行连接，请选择“在不使用代理服务器的情况下直接连接到 Azure Site Recovery”。
   - 如果现有代理要求身份验证，或者你想要使用自定义代理进行提供程序连接，请选择“使用自定义代理设置进行连接”，并指定地址、端口和凭据。

   ![防火墙](./media/tutorial-vmware-to-azure/combined-wiz4.png)

6. 在“先决条件检查”设置中运行检查，确保安装可以运行。 如果看到有关**全局时间同步检查**的警告，请检查系统时钟的时间（“日期和时间”设置）是否与时区相同。

   ![先决条件](./media/tutorial-vmware-to-azure/combined-wiz5.png)

7. 在“MySQL 配置”中，创建用于登录到要安装的 MySQL 服务器实例的凭据。

8. 在“环境详细信息”中，选择“是”，以保护 VMware VM。 安装程序会检查 PowerCLI 6.0 是否已安装。

9. 在“安装位置”中，选择要安装二进制文件和存储缓存的位置。 所选驱动器必须至少有 5 GB 的可用磁盘空间，但我们建议选择至少有 600 GB 可用空间的缓存驱动器。

10. 在“网络选择”中，指定侦听器（网络适配器和 SSL 端口），以便配置服务器在其上发送和接收复制数据。 端口 9443 是用于发送和接收复制流量的默认端口，但可以根据环境的要求修改此端口号。 此外，我们还要打开端口 443，将使用该端口协调复制操作。 请不要使用端口 443 来发送或接收复制流量。

11. 在“摘要”中复查信息，并单击“安装”。 安装程序安装配置服务器，并将其注册到 Azure Site Recovery 服务。

    ![摘要](./media/tutorial-vmware-to-azure/combined-wiz10.png)

    安装完成后，将生成通行短语。 启用复制时需要用到它，因此请复制并将它保存在安全的位置。 服务器显示在保管库中的“设置” > “服务器”窗格中。

### <a name="configure-automatic-discovery"></a>配置自动发现

若要发现 VM，配置服务器需要连接到本地 VMware 服务器。 本教程的目的是，使用在服务器具有管理员特权的帐户添加 vCenter 服务器或 vSphere 主机。

1. 在配置服务器上启动 CSPSConfigtool.exe。 桌面上已提供该工具的快捷方式，在 *安装位置*\home\svsystems\bin 文件夹中也可以找到它。

2. 单击“管理帐户” > “添加帐户”。

   ![添加帐户](./media/tutorial-vmware-to-azure/credentials1.png)

3. 在“帐户详细信息”中，添加用于自动发现的帐户。

   ![详细信息](./media/tutorial-vmware-to-azure/credentials2.png)

若要添加服务器，请执行以下操作：

1. 打开 [Azure 门户](https://portal.azure.com)，然后单击“所有资源”。
2. 单击名为 ContosoVMVault 的恢复服务保管库。
3. 单击“Site Recovery” > “准备基础结构” > “源”
4. 选择“+vCenter”，以连接到 vCenter 服务器或 vSphere ESXi 主机。
5. 在“添加 vCenter”中，为服务器指定一个友好名称。 然后，指定 IP 地址或 FQDN。
6. 除非 VMware 服务器在不同的端口上侦听请求，否则请保持端口设置为 443。
7. 选择要用于连接到服务器的帐户。 单击**“确定”**。

Site Recovery 将使用指定的设置连接到 VMware 服务器，并且将发现 VM。

> [!NOTE]
> 帐户名出现在门户中可能需要 15 分钟或更长时间。 若要立即更新，请单击“配置服务器” > ***服务器名称*** > “刷新服务器”。

## <a name="set-up-the-target-environment"></a>设置目标环境

选择并验证目标资源。

1. 单击“准备基础结构” > “目标”，并选择要使用的 Azure 订阅。
2. 指定目标部署模型是基于 Resource Manager 的还是经典的。
3. Site Recovery 会检查是否有一个或多个兼容的 Azure 存储帐户和网络。

   ![目标](./media/tutorial-vmware-to-azure/storage-network.png)

## <a name="create-a-replication-policy"></a>创建复制策略

1. 打开 [Azure 门户](https://portal.azure.com)，然后单击“所有资源”。
2. 单击名为 ContosoVMVault 的恢复服务保管库。
3. 若要创建复制策略，请单击“Site Recovery 基础结构”>“复制策略”>“+复制策略”。
4. 在“创建复制策略”中，指定策略名称 VMwareRepPolicy。
5. 在“RPO 阈值”中，使用默认值 60 分钟。 此值确定创建恢复点的频率。 如果连续复制超出此限制，将生成警报。
6. 在“恢复点保留期”中，针对每个恢复点的保留时间长度使用默认值 24 小时。 在本教程中，我们选择 72 小时。 可以将复制的虚拟机恢复到窗口中的任何点。
7. 在“应用一致快照频率”中，对于创建应用一致快照的频率使用默认值 60 分钟。 单击“确定”创建该策略。

   ![策略](./media/tutorial-vmware-to-azure/replication-policy.png)

此策略自动与配置服务器关联。 默认情况下会自动创建一个匹配策略以用于故障回复。 例如，如果复制策略是 **rep-policy**，则故障回复策略将是 **rep-policy-failback**。 从 Azure 启动故障回复之前，不会使用此策略。

## <a name="enable-replication"></a>启用复制

为 VM 启用复制后，Site Recovery 会安装移动服务。 可能要等 15 分钟或更长时间，更改才会生效并显示在门户中。

请按如下所述启用复制：

1. 单击“复制应用程序” > “源”。
2. 在“源”中选择配置服务器。
3. 在“计算机类型”中，选择“虚拟机”。
4. 在“vCenter/vSphere 虚拟机监控程序”中，选择管理 vSphere 主机的 vCenter 服务器，或选择该主机。
5. 选择进程服务器（配置服务器）。 然后，单击“确定”。
6. 在“目标”中，选择要创建故障转移 VM 的订阅和资源组。 选择要在 Azure 中用于故障转移 VM 的部署模型（经典或资源管理）。
7. 选择要用于复制数据的 Azure 存储帐户。
8. 选择 Azure VM 在故障转移后创建时所要连接的 Azure 网络和子网。
9. 选择“立即为选定的计算机配置”，将网络设置应用到选择保护的所有计算机。 选择“稍后配置”以选择每个计算机的 Azure 网络。
10. 在“虚拟机” > “选择虚拟机”中，单击并选择要复制的每个计算机。 只能选择可以启用复制的计算机。 然后单击“确定”。
11. 在“属性”>“配置属性”中，选择进程服务器在计算机上自动安装移动服务时使用的帐户。
12. 在“复制设置”>“配置复制设置”中，检查是否选择了正确的复制策略。
13. 单击“启用复制”。

可以在“设置” > “作业” > “Site Recovery 作业”中，跟踪“启用保护”作业的进度。 在“完成保护”作业运行之后，计算机就可以进行故障转移了。

要监视你添加的 VM，可以在“配置服务器”查看上次发现 VM 的时间。
> **上次联系位置**。 要添加 VM 而不想要等待执行计划的发现，请突出显示配置服务器（不要单击它），并单击“刷新”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [运行灾难恢复演练](site-recovery-test-failover-to-azure.md)

