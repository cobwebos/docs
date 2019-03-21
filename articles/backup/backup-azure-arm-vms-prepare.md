---
title: 使用 Azure 备份将 Azure VM 备份到恢复服务保管库中
description: 介绍了如何使用 Azure 备份将 Azure VM 备份到恢复服务保管库中
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: c6d6e380cded18a089f624f90d998477a89293be
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259035"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>将 Azure VM 备份到恢复服务保管库中

本文介绍了如何通过在恢复服务保管库中部署并启用备份，使用 [Azure 备份](backup-overview.md)来备份 Azure VM。

在本文中，学习如何：

> [!div class="checklist"]
> * 验证受支持的方案和先决条件。
> * 准备 Azure VM。 在需要时安装 Azure VM 代理，并验证 VM 的出站访问。
> * 创建保管库。
> * 为保管库设置存储
> * 发现 VM，配置备份设置和策略。
> * 为 Azure VM 启用备份


> [!NOTE]
   > 本文介绍如何通过设置保管库和选择要备份的 VM 来备份 Azure VM。 若要备份多个 VM，则本文的内容会有所帮助。 也可以直接从 VM 设置来[备份 Azure VM](backup-azure-vms-first-look-arm.md)。

## <a name="before-you-start"></a>开始之前

Azure 备份通过为在计算机上运行的 Azure VM 代理安装一个扩展来备份 Azure VM。

1. [查看](backup-architecture.md#architecture-direct-backup-of-azure-vms) Azure VM 备份体系结构。
[了解](backup-azure-vms-introduction.md) Azure VM 备份和备份扩展。
2. 查看 Azure VM 备份的[支持矩阵](backup-support-matrix-iaas.md)。
3. 准备 Azure VM。 安装 VM 代理（如果尚未安装），并验证要备份的 VM 的出站访问。


## <a name="prepare-azure-vms"></a>准备 Azure VM

在需要时安装 VM 代理，并验证来自 VM 的出站访问。

### <a name="install-the-vm-agent"></a>安装 VM 代理
如果需要，请按如下所述安装该代理。

**VM** | **详细信息**
--- | ---
**Windows VM** | [下载并安装](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)代理 MSI 文件。 使用管理员权限在计算机上进行安装。<br/><br/> 若要验证安装，请在 VM 上的“C:\WindowsAzure\Packages”中，右键单击“WaAppAgent.exe”并选择“属性”>“详细信息”选项卡。“产品版本”应为 2.6.1198.718 或更高。<br/><br/> 如果要更新代理，请确保没有备份操作正在运行，并[重新安装代理](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。
**Linux VM** | 使用分发包存储库中的 RPM 或 DEB 包进行安装是安装和升级 Azure Linux 代理的首选方法。 所有[认可的分发版提供商](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)会将 Azure Linux 代理包集成到其映像和存储库。 [GitHub](https://github.com/Azure/WALinuxAgent) 上提供了该代理，但我们不建议从此处安装。<br/><br/> 如果要更新代理，请确保没有备份操作正在运行，并更新二进制文件。


### <a name="establish-network-connectivity"></a>建立网络连接

VM 上运行的备份扩展必须能够对 Azure 公共 IP 地址进行出站访问。

- Azure VM 与 Azure 备份服务通信不需要显式的出站网络访问权限。
- 但是，当尝试进行连接时，某些较旧的虚拟机可能会遇到问题并失败，并且会出现 **ExtensionSnapshotFailedNoNetwork** 错误。 在这种情况下，请使用下列选项之一，使备份扩展能够与用于备份流量的 Azure 公共 IP 地址进行通信。

   **选项** | **Action** | **优点** | **缺点**
   --- | --- | --- | ---
   **设置 NSG 规则** | 允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。<br/><br/>  可以添加一个允许使用[服务标记](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure)访问 Azure 备份服务的规则，而无需分别允许并管理每个地址范围。 [详细了解](../virtual-network/security-overview.md#service-tags)服务标记。 | 无额外成本。 使用服务标记简化管理
   **部署代理** | 部署 HTTP 代理服务器来路由流量。 | 允许访问整个 Azure，而不只是存储。 允许对存储 URL 进行精细控制。<br/><br/> 对 VM 进行单点 Internet 访问。<br/><br/> 代理不产生额外的成本。<br/><br/>
   **设置 Azure 防火墙** | 使用 Azure 备份服务的 FQDN 标记允许流量通过 VM 上的 Azure 防火墙。|  如果在 VNet 子网中设置了 Azure 防火墙，则 FQDN 标记很容易使用 | 无法创建自己的 FQDN 标记，无法修改标记中的 FQDN。<br/><br/> 如果使用 Azure 托管磁盘，可能需要在防火墙上打开另一个端口 (8443)。

#### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>设置一个 NSG 规则以允许对 Azure 进行出站访问

如果对 Azure VM 的访问由 NSG 管理，请允许通过所需的范围和端口对备份存储进行出站访问。

1. 在“VM”>“网络”中，单击“添加出站端口规则”。
2. 在“添加出站安全规则”中，单击“高级”。
3. 在“源”中，选择“VirtualNetwork”。
4. 在“源端口范围”中键入一个星号 (*)，以允许从任何端口进行出站访问。
5. 在“目标”中，选择“服务标记”。 从列表中选择“Storage.region”。 区域是保管库和要备份的 VM 所在的区域。
6. 在“目标端口范围”中选择端口。
    - 使用未加密存储帐户的非托管 VM：80
    - 使用加密存储帐户的非托管 VM：443（默认设置）
    - 托管 VM：8443。
7. 在“协议”中，选择“TCP”。
8. 在“优先级”中，为此规则指定一个优先级值，该值必须小于任何具有更高优先级的拒绝规则。 如果某个规则拒绝访问，则新的允许规则的优先级必须更高。 例如，如果为 **Deny_All** 规则设置的优先级为 1000，则必须将新规则的优先级设置为 1000 以下。
9. 提供规则的名称和说明，然后单击“确定”。

可以将 NSG 规则应用于多个 VM 来允许出站访问。

此视频可引导你完成该过程。

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]


#### <a name="route-backup-traffic-through-a-proxy"></a>通过代理路由备份流量

可以通过代理路由备份流量，然后允许代理访问所需的 Azure 区域。
应将代理 VM 配置为允许以下操作：

- Azure VM 应该通过代理路由所有发往公共 Internet 的 HTTP 流量。
- 代理应该允许适用虚拟网络 (VNet) 中的 VM 传入的流量。
- NSG **NSF-lockdown** 需要使用一个规则允许来自代理 VM 的出站 Internet 流量。

##### <a name="set-up-the-proxy"></a>设置代理
如果你没有系统帐户代理，请按如下所述设置一个：

1. 下载 [PsExec](https://technet.microsoft.com/sysinternals/bb897553)。

2. 运行 **PsExec.exe -i -s cmd.exe**，以便在系统帐户下运行命令提示符。
3. 在系统上下文中运行浏览器。 例如：对于 Internet Explorer，请运行 **%PROGRAMFILES%\Internet Explorer\iexplore.exe**。  
4. 定义代理设置。
   - 在 Linux 计算机上：
     - 将以下代码行添加到 **/etc/environment** 文件：
       - **http_proxy = http: \/ /代理 IP 地址： 代理端口**
     - 将以下代码行添加到 **/etc/waagent.conf** 文件：
         - **HttpProxy.Host=proxy IP address**
         - **HttpProxy.Port=proxy port**
   - 在 Windows 计算机上的浏览器设置中，指定要使用代理。 如果当前在用户帐户中使用代理，则可以使用此脚本在系统帐户级别应用该设置。
       ```powershell
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

##### <a name="allow-incoming-connections-on-the-proxy"></a>在代理上允许传入连接

在代理设置中允许传入连接。

- 例如，打开“高级安全 Windows 防火墙”。
    - 右键单击“入站规则” > “新建规则”。
    - 在“规则类型”中，选择“自定义” > “下一步”。
    - 在“程序”中，选择“所有程序” > “下一步”。
    - 在“协议和端口”中，将类型设置为“TCP”，将“本地端口”设置为“特定端口”，将“远程端口”设置为“所有端口”。
    - 完成向导并指定规则名称。

##### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>为代理的 NSG 添加例外规则

在 NSG **NSF-lockdown** 中，允许从 10.0.0.5 上的任何端口流向端口 80 (HTTP) 或 443 (HTTPS) 上的任何 Internet 地址的流量。

- 下面是一个允许流量的 PowerShell 脚本示例。
- 如果不想要允许对所有公共 Internet 地址进行出站访问，可以指定 IP 地址范围 (-DestinationPortRange)，或使用 storage.region 服务标记。   

    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```

### <a name="allow-firewall-access-with-fqdn-tag"></a>使用 FQDN 标记允许通过防火墙访问

可以设置 Azure 防火墙，以允许网络流量对 Azure 备份进行出站访问。

- [了解](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)如何部署 Azure 防火墙。
- [了解](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN 标记。

## <a name="set-up-storage-replication"></a>设置存储复制

默认情况下，保管库采用[异地冗余存储 (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。 我们建议对主要备份使用 GRS，但也可以使用更经济的[本地冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

Azure 备份会自动处理存储在保管库。 需要指定如何复制该存储。
按如下所述修改存储复制：

1. 在“恢复服务保管库”边栏选项卡中，单击新保管库。 下**设置**部分中，单击**属性**。
2. 在中**属性**下**备份配置**，单击**更新**。

3. 选择存储复制类型，然后单击**保存**。

      ![设置新保管库的存储配置](./media/backup-try-azure-backup-in-10-mins/full-blade.png)


## <a name="configure-a-backup-policy"></a>配置备份策略

发现订阅中的 VM 并配置备份。

1. 在保管库中选择“概述”，然后单击“+备份”

   ![“备份”按钮](./media/backup-azure-arm-vms-prepare/backup-button.png)

   此时会打开“备份”和“备份目标”窗格。

2. 在“备份目标”> “工作负荷在哪里运行?”中，选择“Azure”。 在“要备份哪些内容?”中，选择“虚拟机” >  “确定”。 这会在保管库中注册 VM 扩展。

   ![“备份”和“备份目标”窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   此步骤向保管库注册 VM 扩展。 随后将会关闭“备份目标”窗格，并打开“备份策略”窗格。

3. 在“备份策略”中，选择要与保管库关联的策略。 然后单击“确定”。
    - 默认策略的详细信息会在下拉菜单下列出。
    - 单击“新建”以创建策略。 [详细了解](backup-azure-arm-vms-prepare.md#configure-a-backup-policy)如何定义策略。

      ![“备份”和“备份策略”窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)

4. 在“选择虚拟机”窗格中，选择要使用指定备份策略的 VM，然后单击“确定”。

   - 随后将验证选定的 VM。
   - 只能选择与保管库位于同一区域中的 VM。 只能在单个保管库中备份 VM。

     ![“选择虚拟机”窗格](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. 在“备份”中，选择“启用备份”。

   - 这会将策略部署到保管库和 VM，并在 Azure VM 上运行的 VM 代理中安装备份扩展。
   - 此步骤不会创建 VM 的初始恢复点。

     ![“启用备份”按钮](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

启用备份后：

- 初始备份将根据备份计划运行。
- 无论 VM 是否在运行，备份服务都会安装备份扩展。
    - VM 运行时，很有可能会获得应用程序一致的恢复点。
    -  但是，即使 VM 已关闭且无法安装扩展，也仍会备份 VM。 此类 VM 称为“脱机 VM”。 在这种情况下，恢复点将是 *崩溃一致性*恢复点。
    请注意，Azure 备份不支持根据 Azure VM 备份的夏令时时差自动调整时钟。 请根据需要手动修改备份策略。

## <a name="run-the-initial-backup"></a>运行初始备份

初始备份将根据计划运行，除非你立即手动运行它。 可以如下所述来手动运行初始备份：

1. 在保管库菜单中，单击“备份项”。
2. 在“备份项”中，单击“Azure 虚拟机”。
3. 在“备份项”列表中，单击省略号“...”。
4. 单击“立即备份”。
5. 在“立即备份”中，使用日历控件选择应当保留恢复点的最后一天，然后单击“确定”。
6. 监视门户通知。 可以在保管库仪表板 >“备份作业” > “进行中”监视作业进度。 创建初始备份可能需要一些时间，具体取决于 VM 的大小。



## <a name="next-steps"></a>后续步骤

- 排查 [Azure VM 代理](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)或 [Azure VM 备份](backup-azure-vms-troubleshoot.md)出现的任何问题。
- [备份 Azure VM](backup-azure-vms-first-look-arm.md)
