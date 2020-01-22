---
title: 将 Azure VM 备份到恢复服务保管库中
description: 介绍如何使用 Azure 备份在恢复服务保管库中备份 Azure Vm
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 95c185c09558f3d1a525c9bcf15f3957118c4311
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294025"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>将 Azure VM 备份到恢复服务保管库中

本文介绍如何使用[Azure 备份](backup-overview.md)服务在恢复服务保管库中备份 azure vm。

在本文中，学习如何：

> [!div class="checklist"]
>
> * 准备 Azure VM。
> * 创建保管库。
> * 发现 Vm 并配置备份策略。
> * 为 Azure Vm 启用备份。
> * 运行初始备份。

> [!NOTE]
> 本文介绍如何设置保管库并选择要备份的 Vm。 若要备份多个 VM，则本文的内容会有所帮助。 或者，可以直接从 VM 设置[备份单个 AZURE vm](backup-azure-vms-first-look-arm.md) 。

## <a name="before-you-start"></a>开始之前

* [查看](backup-architecture.md#architecture-built-in-azure-vm-backup)Azure VM 备份体系结构。
* [了解](backup-azure-vms-introduction.md) Azure VM 备份和备份扩展。
* 在配置备份之前，请[查看支持矩阵](backup-support-matrix-iaas.md)。

此外，在某些情况下，你可能需要执行几项操作：

* 在**vm 上安装 vm 代理**： azure 备份通过将扩展安装到在计算机上运行的 Azure VM 代理来备份 azure vm。 如果 VM 是从 Azure marketplace 映像创建的，则代理已安装并正在运行。 如果创建自定义 VM，或迁移本地计算机，则可能需要[手动安装代理](#install-the-vm-agent)。
* **明确允许出站访问**：通常，无需显式允许 Azure VM 的出站网络访问，就能与 azure 备份进行通信。 但是，某些 Vm 可能会遇到连接问题，并在尝试连接时显示**ExtensionSnapshotFailedNoNetwork**错误。 如果发生这种情况，应[显式允许出站访问](#explicitly-allow-outbound-access)，以便 azure 备份扩展可以与 AZURE 公共 IP 地址进行通信，以进行备份通信。

## <a name="create-a-vault"></a>创建保管库

 保管库可以存储备份以及在不同时间创建的恢复点，并可以存储与备份的计算机相关联的备份策略。 按如下所述创建保管库：

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 在 "搜索" 中，键入 "**恢复服务**"。 在 "**服务**" 下，单击 "**恢复服务保管库**"。

     ![搜索恢复服务保管库](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. 在**恢复服务保管库**菜单中，单击 " **+ 添加**"。

     ![创建恢复服务保管库步骤 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. 在 "**恢复服务保管库**" 中，键入一个友好名称以标识保管库。
    * 名称对于 Azure 订阅需要是唯一的。
    * 该名称可以包含 2 到 50 个字符。
    * 名称必须以字母开头，只能包含字母、数字和连字符。
5. 选择应在其中创建保管库的 Azure 订阅、资源组和地理区域。 然后单击“创建”。
    * 创建保管库可能需要一段时间。
    * 可以在门户的右上区域中监视状态通知。

创建保管库后，它将显示在 "恢复服务保管库" 列表中。 如果未看到创建的保管库，请选择“刷新”。

![备份保管库列表](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> Azure 备份现在允许自定义由 Azure 备份服务创建的资源组名称。 有关详细信息，请参阅[适用于虚拟机的 Azure 备份资源组](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)。

### <a name="modify-storage-replication"></a>修改存储复制

默认情况下，保管库使用[异地冗余存储（GRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。

* 如果保管库是你的主要备份机制，则建议你使用 GRS。
* 可以使用[本地冗余存储（LRS）](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)来获得更便宜的选项。

修改存储复制类型，如下所示：

1. 在新保管库中，单击 "**设置**" 部分中的 "**属性**"。
2. 在 "**属性**" 中的 "**备份配置**" 下，单击 "**更新**"。
3. 选择存储复制类型，然后单击 "**保存**"。

      ![设置新保管库的存储配置](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > 在设置保管库并包含备份项后，不能修改存储复制类型。 如果要执行此操作，则需要重新创建保管库。

## <a name="apply-a-backup-policy"></a>应用备份策略

配置保管库的备份策略。

1. 在保管库中，单击 "**概述**" 部分中的 " **+ 备份**"。

   ![“备份”按钮](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. 在**备份目标** > **工作负荷的运行位置？选择 "** **Azure**"。 在 "**要备份的内容"** 中，选择 "**虚拟机** >   **" 确定 "** 。 这会在保管库中注册 VM 扩展。

   ![“备份”和“备份目标”窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. 在“备份策略”中，选择要与保管库关联的策略。
    * 默认策略每天备份一次 VM。 每日备份保留30天。 即时恢复快照保留两天。
    * 如果你不想使用默认策略，请选择 "**新建**"，然后按照下一过程中所述创建自定义策略。

      ![默认备份策略](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. 在 "**选择虚拟机**" 中，选择要使用策略备份的 vm。 然后单击“确定”。

   * 已验证所选 Vm。
   * 只能选择与保管库位于同一区域中的 VM。
   * 只能在单个保管库中备份 VM。

     ![“选择虚拟机”窗格](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. 在 "**备份**" 中，单击 "**启用备份**"。 这会将策略部署到保管库和 VM，并在 Azure VM 上运行的 VM 代理中安装备份扩展。

     ![“启用备份”按钮](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

启用备份后：

* 无论 VM 是否在运行，备份服务都会安装备份扩展。
* 初始备份将按照备份计划运行。
* 当备份运行时，请注意：
  * 正在运行的 VM 具有捕获应用程序一致恢复点的最大机会。
  * 但是，即使 VM 已关闭，也会对其进行备份。 此类 VM 称为脱机 VM。 在这种情况下，恢复点将是崩溃一致性恢复点。

### <a name="create-a-custom-policy"></a>创建自定义策略

如果选择创建新的备份策略，请填写策略设置。

1. 在 "**策略名称**" 中，指定一个有意义的名称。
2. 在 "**备份计划**" 中，指定何时进行备份。 对于 Azure Vm，你可以执行每日或每周备份。
3. 在 "**即时还原**" 中，指定要在本地保留快照以进行即时还原的时间长度。
    * 还原时，备份的 VM 磁盘将从存储复制到整个网络，恢复存储位置。 使用即时还原，你可以利用在备份作业期间拍摄的本地存储的快照，而无需等待备份数据传输到保管库。
    * 可以保留一到五天的即时还原快照。 默认设置为两天。
4. 在 "**保持期**" 中，指定要保留每日或每周备份点的时间长度。
5. 在 "**每月备份点的保留期**" 中，指定是否要保留每月备份和每周备份。
6. 单击“确定”保存策略。

    ![新建备份策略](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure 备份不支持根据 Azure VM 备份的夏令时时差自动调整时钟。 当时间发生变化时，请根据需要手动修改备份策略。

## <a name="trigger-the-initial-backup"></a>触发初始备份

初始备份将按照计划运行，但你可以立即运行，如下所示：

1. 在保管库菜单中，单击“备份项”。
2. 在 "**备份项**" 中，单击 " **Azure 虚拟机**"。
3. 在 "**备份项**" 列表中，单击省略号（...）。
4. 单击“立即备份”。
5. 现在，在 "**备份**" 中使用 "日历" 控件选择应该保留恢复点的最后一天。 然后单击“确定”。
6. 监视门户通知。 可以在保管库仪表板 >“备份作业” > “进行中”监视作业进度。 创建初始备份可能需要一些时间，具体取决于 VM 的大小。

## <a name="verify-backup-job-status"></a>验证备份作业状态

每个 VM 备份的备份作业详细信息包括两个阶段：**快照**阶段，后跟 "将**数据传输到保管库**" 阶段。<br/>
快照阶段保证与磁盘一起存储的恢复点的可用性，以便进行**即时还原**，并且最多可使用5天，具体取决于用户配置的快照保持期。 将数据传输到保管库会在保管库中创建恢复点，以实现长期保留。 仅在快照阶段完成后才会开始将数据传输到保管库。

  ![备份作业状态](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

后端上运行了两**个子任务**，一个用于前端备份作业，可以按如下所示从 "**备份作业**详细信息" 边栏选项卡中进行检查：

  ![备份作业状态](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

根据磁盘的大小、每个磁盘的变动和几个其他因素，将**数据传输到保管库**阶段可能需要数天时间才能完成。

作业状态可能因以下情况而有所不同：

**快照** | **将数据传输到保管库** | **作业状态**
--- | --- | ---
已完成 | 正在进行 | 正在进行
已完成 | 已跳过 | 已完成
已完成 | 已完成 | 已完成
已完成 | 已失败 | 已完成，但出现警告
已失败 | 已失败 | 已失败

现在，有了此功能，对于同一 VM，两个备份可以并行运行，但在任一阶段（快照、将数据传输到保管库）中，只有一个子任务可运行。 在方案中，正在进行的备份作业将通过此分离功能避免下一天的备份失败。 如果在较早的备份作业处于 "正在进行" 状态，则在将**数据传输到保管库**时，后续日期的备份可能已完成。
在保管库中创建的增量恢复点将捕获在保管库中创建的上一个恢复点的所有改动。 用户不会产生成本影响。

## <a name="optional-steps-install-agentallow-outbound"></a>可选步骤（安装代理/允许出站）

### <a name="install-the-vm-agent"></a>安装 VM 代理

Azure 备份通过为在计算机上运行的 Azure VM 代理安装一个扩展来备份 Azure VM。 如果 VM 是从 Azure Marketplace 映像创建的，则代理已安装并正在运行。 如果创建自定义 VM，或迁移本地计算机，则可能需要手动安装代理，如表中所述。

**VM** | **详细信息**
--- | ---
**Windows** | 1.[下载并安装](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)代理 MSI 文件。<br/><br/> 2. 在计算机上安装具有管理员权限。<br/><br/> 3. 验证安装。 在 VM 上的*C:\WindowsAzure\Packages*中，右键单击 " **waappagent.exe** " > "**属性**"。 在 "**详细信息**" 选项卡上，**产品版本**应为2.6.1198.718 或更高版本。<br/><br/> 如果要更新代理，请确保没有正在运行的备份操作，然后[重新安装代理](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
**Linux** | 使用分发包存储库中的 RPM 或 DEB 包进行安装。 这是安装和升级 Azure Linux 代理的首选方法。 所有[认可的分发版提供商](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)会将 Azure Linux 代理包集成到其映像和存储库。 [GitHub](https://github.com/Azure/WALinuxAgent) 上提供了该代理，但我们不建议从此处安装。<br/><br/> 如果要更新代理，请确保没有正在运行的备份操作，并更新二进制文件。

### <a name="explicitly-allow-outbound-access"></a>显式允许出站访问

VM 上运行的备份扩展需要对 Azure 公共 IP 地址的出站访问权限。

* 通常，无需显式允许 Azure VM 的出站网络访问权限即可与 Azure 备份进行通信。
* 如果在连接 Vm 时遇到问题，或者如果你在尝试连接时看到错误**ExtensionSnapshotFailedNoNetwork** ，则应显式允许访问，以便备份扩展可以与 AZURE 公共 IP 地址进行通信，以便进行备份通信。 访问方法在下表中进行了总结。

**选项** | **Action** | **详细信息**
--- | --- | ---
**设置 NSG 规则** | 允许[Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。<br/><br/> 你可以添加一条允许使用[服务标记](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure)访问 Azure 备份服务的规则，而不是允许和管理每个地址范围。 | [详细了解](../virtual-network/security-overview.md#service-tags)服务标记。<br/><br/> 服务标记简化了访问管理，不会产生额外的成本。
**部署代理** | 部署 HTTP 代理服务器来路由流量。 | 允许访问整个 Azure，而不只是存储。<br/><br/> 允许对存储 URL 进行精细控制。<br/><br/> 对 VM 进行单点 Internet 访问。<br/><br/> 代理不产生额外的成本。
**设置 Azure 防火墙** | 使用 Azure 备份服务的 FQDN 标记允许流量通过 VM 上的 Azure 防火墙 | 如果已在 VNet 子网中设置 Azure 防火墙，则使用简单。<br/><br/> 不能创建自己的 FQDN 标记，也不能在标记中修改 Fqdn。<br/><br/> 如果 Azure Vm 具有托管磁盘，可能需要在防火墙上打开其他端口（8443）。

#### <a name="establish-network-connectivity"></a>建立网络连接

与 NSG、代理或防火墙建立连接

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>设置一个 NSG 规则以允许对 Azure 进行出站访问

如果 NSG 管理 VM 访问权限，则允许将备份存储的出站访问权限发送到所需的范围和端口。

1. 在 "VM 属性" >**网络**"中，选择"**添加出站端口规则**"。
2. 在 "**添加出站安全规则**" 中，选择**高级**。
3. 在“源”中，选择“VirtualNetwork”。
4. 在 "**源端口范围**" 中，输入星号（*）以允许来自任何端口的出站访问。
5. 在“目标”中，选择“服务标记”。 从列表中选择“Storage.region”。 区域是保管库以及要备份的 Vm 所在的位置。
6. 在“目标端口范围”中选择端口。
    * 使用未加密存储帐户的非托管磁盘的 VM：80
    * 使用带有加密存储帐户的非托管磁盘的 VM：443（默认设置）
    * 使用托管磁盘的 VM：8443。
7. 在“协议”中，选择“TCP”。
8. 在“优先级”中，为此规则指定一个优先级值，该值必须小于任何具有更高优先级的拒绝规则。

   如果有拒绝访问的规则，则新的允许规则必须较高。 例如，如果为 **Deny_All** 规则设置的优先级为 1000，则必须将新规则的优先级设置为 1000 以下。
9. 提供规则的名称和描述，然后选择 **"确定"** 。

可以将 NSG 规则应用于多个 VM 来允许出站访问。 此视频可引导你完成该过程。

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

##### <a name="route-backup-traffic-through-a-proxy"></a>通过代理路由备份流量

可以通过代理路由备份流量，然后允许代理访问所需的 Azure 区域。 将代理 VM 配置为允许以下各项：

* Azure VM 应该通过代理路由所有发往公共 Internet 的 HTTP 流量。
* 代理应允许来自相应虚拟网络中的 Vm 的传入流量。
* NSG **NSF-lockdown** 需要使用一个规则允许来自代理 VM 的出站 Internet 流量。

###### <a name="set-up-the-proxy"></a>设置代理

如果你没有系统帐户代理，请按如下所述设置一个：

1. 下载 [PsExec](https://technet.microsoft.com/sysinternals/bb897553)。
2. 运行 **PsExec.exe -i -s cmd.exe**，以便在系统帐户下运行命令提示符。
3. 在系统上下文中运行浏览器。 例如，使用 **%Programfiles%\internet explorer\ Explorer\iexplore.exe** For Internet Explorer。  
4. 定义代理设置。
   * 在 Linux 计算机上：
     * 将以下代码行添加到 **/etc/environment** 文件：
       * **http_proxy = http：\//proxy IP 地址：代理端口**
     * 将以下代码行添加到 **/etc/waagent.conf** 文件：
         * **HttpProxy.Host=proxy IP address**
         * **HttpProxy.Port=proxy port**
   * 在 Windows 计算机上的浏览器设置中，指定要使用代理。 如果当前在用户帐户中使用代理，则可以使用此脚本在系统帐户级别应用该设置。

       ```powershell
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>在代理上允许传入连接

在代理设置中允许传入连接。

1. 在 Windows 防火墙中，打开 "**高级安全 Windows 防火墙**"。
2. 右键单击“入站规则” > “新建规则”。
3. 在 "**规则类型**" 中，选择 "**自定义** ** > "** 。
4. 在“程序”中，选择“所有程序” > “下一步”。
5. 在 "**协议和端口**" 中：
   * 将类型设置为**TCP**。
   * 将 "**本地端口**" 设置为 "**特定端口**"。
   * 将**远程端口**设置为**所有端口**。

6. 完成向导并指定规则名称。

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>为代理的 NSG 添加例外规则

在 NSG **NSF-lockdown** 中，允许从 10.0.0.5 上的任何端口流向端口 80 (HTTP) 或 443 (HTTPS) 上的任何 Internet 地址的流量。

下面是一个允许流量的 PowerShell 脚本示例。
可以指定 IP 地址范围（`-DestinationPortRange`），也可以使用 "存储区域服务" 标记，而不是允许出站到所有公共 internet 地址。

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>允许使用 FQDN 标记进行防火墙访问

可以设置 Azure 防火墙以允许对 Azure 备份进行网络流量的出站访问。

* [了解](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)如何部署 Azure 防火墙。
* [了解](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN 标记。

>[!NOTE]
> Azure 备份现在支持使用 Azure 虚拟机备份解决方案进行选择性磁盘备份和还原。
>
>如今，Azure 备份支持使用虚拟机备份解决方案，将 VM 中的所有磁盘（操作系统和数据）备份到一起。 使用排除磁盘功能，你可以选择从 VM 的多个数据磁盘中备份一个或多个数据磁盘。 这为备份和还原需求提供高效且经济高效的解决方案。 每个恢复点都包含备份操作中包含的磁盘数据，在还原操作过程中，您还可以使用该数据的一个子集从给定的恢复点还原。 这适用于从快照和保管库还原。
>
> 此解决方案在以下情况下特别有用：
>  
>1. 你的关键数据只需在一个磁盘中备份，并且你不想备份其他附加到 VM 的磁盘。 这可最大程度地减少备份存储成本。  
>2. VM 数据的一部分有其他备份解决方案。 例如，使用不同的工作负荷备份解决方案来备份数据库或数据，并希望使用 Azure VM 级别备份来实现磁盘和数据的其余部分，以利用可用的最佳功能构建高效且可靠的系统。
>
>若要注册预览版，请在 AskAzureBackupTeam@microsoft.com 写信

## <a name="next-steps"></a>后续步骤

* 排查[AZURE vm 代理](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)或[azure vm 备份](backup-azure-vms-troubleshoot.md)的任何问题。
* [还原](backup-azure-arm-restore-vms.md)Azure Vm。
