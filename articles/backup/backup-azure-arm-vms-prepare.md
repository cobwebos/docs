---
title: 准备使用 Azure 备份来备份 Azure VM
description: 介绍如何准备好使用 Azure 备份服务来备份 Azure VM
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: raynew
ms.openlocfilehash: ee7a9c407a26f9334a854c98793db8fc01244e2a
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994668"
---
# <a name="prepare-to-back-up-azure-vms"></a>准备备份 Azure VM

本文介绍如何准备好使用 [Azure 备份](backup-introduction-to-azure-backup.md)恢复服务保管库来备份 Azure VM。 准备备份的过程包括：


> [!div class="checklist"]
> * 在开始之前，查看支持的方案和限制。
> * 检查先决条件，包括 Azure VM 要求和网络连接。
> * 创建保管库。
> * 选择存储复制方式。
> * 发现 VM，配置备份设置和策略。
> * 为选定的 VM 启用备份


> [!NOTE]
   > 本文介绍如何通过设置保管库和选择要备份的 VM 来备份 Azure VM。 若要备份多个 VM，则本文的内容会有所帮助。 也可以直接从 Azure VM 的设置来备份该 VM。 [了解详细信息](backup-azure-vms-first-look-arm.md)

## <a name="before-you-start"></a>开始之前

1. 获取 Azure VM 的 Azure 备份[概述](backup-azure-vms-introduction.md)。
2. 查看下面的支持详细信息和限制。

   **支持/限制** | **详细信息**
   --- | ---
   **Windows OS** | Windows Server 2008 R2 64 位或更高版本。<br/><br/> Windows Client 7 64 位或更高版本。
   **Linux OS** | 可以备份 [Azure 支持的](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 64 位 Linux 分发版，但 CoreOS Linux 除外。<br/><br/> 查看[支持文件还原](backup-azure-restore-files-from-vm.md#for-linux-os)的 Linux 操作系统。<br/><br/> 只要 VM 上装有 VM 代理且支持 Python，其他 Linux 分发版也能正常运行。 但是，Microsoft 不会为这些分发版提供支持。
   **区域** | 可以在所有[支持的区域](https://azure.microsoft.com/regions/#services)中备份 Azure VM。 如果某个区域不受支持，则创建保管库时无法选择该区域。<br/><br/> 无法跨 Azure 区域进行备份和还原。 只能在单个区域中执行此类操作。
   **数据磁盘限制** | 无法备份包含 16 个以上数据磁盘的 VM。
   **共享存储** | 我们不建议使用 CSV 或横向扩展文件服务器备份 VM。 CSV 写入器可能会失败。
   **Linux 加密** | 不支持备份通过 Linux 统一密钥设置 (LUKS) 加密的 Linux VM。
   **VM 一致性** | Azure 备份不支持多 VM 一致性。
   **网络** | 备份的数据不包括附加到 VM 的网络装载驱动器。<br/><br/>
   **快照** | 不支持在启用了写入加速器的磁盘上创建快照。 这会阻止 Azure 备份为所有 VM 磁盘创建应用一致性快照。
   **PowerShell** | 有些操作只能使用 PowerShell 来完成：<br/><br/> - 还原内部/外部负载均衡器管理的 VM，或者具有多个保留 IP 地址或适配器的 VM。 [了解详细信息](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations)<br/><br/> - 还原采用多域控制器配置的域控制器 VM。 [了解详细信息](backup-azure-arm-restore-vms.md#restore-domain-controller-vms)。
   **系统时间** | Azure 备份不支持根据 Azure VM 备份的夏令时时差自动调整时钟。 请根据需要手动修改备份策略。
   **存储帐户** | 如果使用网络受限的存储帐户，请确保启用“允许受信任的 Microsoft 服务访问此存储帐户”，使 Azure 备份服务能够访问该帐户。 网络受限的存储帐户不支持项级恢复。<br/><br/> 在存储帐户中，请确保“防火墙和虚拟网络”设置允许从“所有网络”进行访问。


## <a name="prerequisites"></a>先决条件

- 必须在要备份的 Azure VM 所在的同一区域中创建保管库。
- 开始之前，请检查 Azure VM 区域。
    - 如果多个区域中都有 VM，请在每个区域中创建一个保管库。
    - 无需指定存储帐户即可存储备份数据。 保管库和 Azure 备份服务会自动处理这种情况。
- 确认要备份的 Azure VM 上是否安装了 VM 代理。



### <a name="install-the-vm-agent"></a>安装 VM 代理

为了启用备份，Azure 备份会将一个备份扩展（“VM 快照”或“VM 快照 Linux”）安装到 Azure VM 上运行的 VM 代理。
    -  Azure VM 代理默认安装在任何通过 Azure 市场映像部署的 Windows VM 上。 通过门户、PowerShell、CLI 或 Azure 资源管理器模板部署 Azure 市场映像时，也会安装 Azure VM 代理。
    - 如果从本地迁移了 VM，则不会安装该代理，需要在为 VM 启用备份之前安装它。

如果需要，请按如下所述安装该代理。

**VM** | **详细信息**
--- | ---
**Windows VM** | 在计算机上使用管理员权限[下载并安装](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)该代理。<br/><br/> 若要验证安装，请在 VM 上的“C:\WindowsAzure\Packages”中，右键单击“WaAppAgent.exe”并选择“属性”>“详细信息”选项卡。“产品版本”应为 2.6.1198.718 或更高。
**Linux VM** | 使用分发包存储库中的 RPM 或 DEB 包进行安装是安装和升级 Azure Linux 代理的首选方法。 所有[认可的分发版提供商](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)会将 Azure Linux 代理包集成到其映像和存储库。 [GitHub](https://github.com/Azure/WALinuxAgent) 上提供了该代理，但我们不建议从此处安装。
如果在备份 Azure VM 时遇到问题，请使用下表检查是否已在虚拟机上正确安装 Azure VM 代理。 该表提供了适用于 Windows 和 Linux VM 的 VM 代理的其他信息。

### <a name="establish-network-connectivity"></a>建立网络连接

VM 上运行的备份扩展必须能够对 Azure 公共 IP 地址进行出站访问。 若要进行访问，可以：


- **NSG 规则**：允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 可以添加一个允许使用[服务标记](../virtual-network/security-overview.md#service-tags)访问 Azure 备份服务的规则，而无需单独允许每个地址范围并持续对其进行管理。
- **代理**：部署 HTTP 代理服务器来路由流量。
- **Azure 防火墙**：使用 Azure 备份服务的 FQDN 标记允许流量通过 VM 上的 Azure 防火墙。

在选项之间做出抉择时，请考虑到每个选项的利弊。

**选项** | **优点** | **缺点**
--- | --- | ---
**NSG** | 无额外成本。 使用服务标记简化管理 | 允许访问整个 Azure，而不只是存储。 |
**HTTP 代理** | 允许对存储 URL 进行精细控制。<br/><br/> 对 VM 进行单点 Internet 访问。<br/><br/> 代理不产生额外的成本。
**FQDN 标记** | 如果在 VNet 子网中设置了 Azure 防火墙，则 FQDN 标记很容易使用 | 无法创建自己的 FQDN 标记，无法修改标记中的 FQDN。



如果使用 Azure 托管磁盘，可能需要在防火墙上打开另一个端口 (8443)。



### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>设置一个 NSG 规则以允许对 Azure 进行出站访问

如果对 Azure VM 的访问由 NSG 管理，请允许通过所需的范围和端口对备份存储进行出站访问。



1. 在“VM”>“网络”中，单击“添加出站端口规则”。
- 如果某个规则拒绝访问，则新的允许规则的优先级必须更高。 例如，如果为 **Deny_All** 规则设置的优先级为 1000，则必须将新规则的优先级设置为 1000 以下。
2. 在“添加出站安全规则”中，单击“高级”。
3. 在“源”中，选择“VirtualNetwork”。
4. 在“源端口范围”中键入一个星号 (*)，以允许从任何端口进行出站访问。
5. 在“目标”中，选择“服务标记”。 在列表中选择“存储”。<region> 区域是保管库和要备份的 VM 所在的区域。
6. 在“目标端口范围”中选择端口。

    - 使用非托管磁盘和未加密存储帐户的 VM：80
    - 使用非托管磁盘和加密存储帐户的 VM：443（默认设置）
    - 托管 VM：8443。
1. 在“协议”中，选择“TCP”。
2. 在“优先级”中，为此规则分配一个优先级值，该值必须小于任何具有更高优先级的拒绝规则。
3. 提供规则的名称和说明，然后单击“确定”。

可将 NSG 规则应用到多个 VM，以允许 Azure 备份对 Azure 进行出站访问。

此视频可引导你完成该过程。

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]



### <a name="route-backup-traffic-through-a-proxy"></a>通过代理路由备份流量

可以通过代理路由备份流量，然后允许代理访问所需的 Azure 区域。

应将代理 VM 配置为允许以下操作：

- Azure VM 应该通过代理路由所有发往公共 Internet 的 HTTP 流量。
- 代理应该允许适用虚拟网络 (VNet) 中的 VM 传入的流量。
- NSG **NSF-lockdown** 需要使用一个规则允许来自代理 VM 的出站 Internet 流量。

下面是设置代理的方式。 此处使用了示例值。 你应该将这些值替换为自己的值。

#### <a name="set-up-a-system-account-proxy"></a>设置系统帐户代理
如果你没有系统帐户代理，请按如下所述设置一个：

1. 下载 [PsExec](https://technet.microsoft.com/sysinternals/bb897553)。
2. 运行 **PsExec.exe -i -s cmd.exe**，以便在系统帐户下运行命令提示符。
3. 在系统上下文中运行浏览器。 例如：对于 Internet Explorer，请运行 **PROGRAMFILES%\Internet Explorer\iexplore.exe**。  
4. 定义代理设置。
    - 在 Linux 计算机上：
        - 将以下代码行添加到 **/etc/environment** 文件：
            - **http_proxy=http://proxy IP address:proxy port**
        - 将以下代码行添加到 **/etc/waagent.conf** 文件：
            - **HttpProxy.Host=proxy IP address**
            - **HttpProxy.Port=proxy port**
    - 在 Windows 计算机上的浏览器设置中，指定要使用代理。 如果当前在用户帐户中使用代理，则可以使用此脚本在系统帐户级别应用该设置。
        ```
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
       $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
       Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

        ```

#### <a name="allow-incoming-connections-on-the-proxy"></a>在代理上允许传入连接

1. 在代理设置中允许传入连接。
2. 例如，打开“高级安全 Windows 防火墙”。
    - 右键单击“入站规则” > “新建规则”。
    - 在“规则类型”中，选择“自定义” > “下一步”。
    - 在“程序”中，选择“所有程序” > “下一步”。
    - 在“协议和端口”中，将类型设置为“TCP”，将“本地端口”设置为“特定端口”，将“远程端口”设置为“所有端口”。
    - 完成向导并指定规则名称。

#### <a name="add-an-exception-rule-to-the-nsg"></a>向 NSG 添加例外规则

在 NSG **NSF-lockdown** 中，允许从 10.0.0.5 上的任何端口流向端口 80 (HTTP) 或 443 (HTTPS) 上的任何 Internet 地址的流量。

- 下面是一个允许流量的 PowerShell 脚本示例。
- 如果不想要允许对所有公共 Internet 地址进行出站访问，可以指定 IP 地址范围 (-DestinationPortRange)，或使用 storage.region 服务标记。   

    ```
    Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
    Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
    ```
### <a name="allow-firewall-access-with-fqdn-tag"></a>使用 FQDN 标记允许通过防火墙访问

可以设置 Azure 防火墙，以允许网络流量对 Azure 备份进行出站访问。

- [了解](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)如何部署 Azure 防火墙。
- [了解](https://docs.microsoft.com/azure/firewall/fqdn-tags) FQDN 标记。


## <a name="create-a-vault"></a>创建保管库

用于备份的恢复服务保管库可以存储备份以及在不同时间创建的恢复点，并可以存储与备份的计算机相关联的备份策略。 按如下所述创建保管库：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在“中心”菜单中选择“浏览”，然后键入“恢复服务”。 开始键入时，会根据输入筛选资源列表。 选择“恢复服务保管库”。

    ![在框中键入内容，并在结果中选择“恢复服务保管库”](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    此时会显示恢复服务保管库列表。
3. 在“恢复服务保管库”菜单中，选择“添加”。

    ![创建恢复服务保管库步骤 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    此时会打开“恢复服务保管库”窗格。 此窗格中会提示输入“名称”、“订阅”、“资源组”和“位置”的信息。

    ![“恢复服务保管库”窗格](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. 在“名称”中，输入一个友好名称以标识此保管库。
    - 名称对于 Azure 订阅需要是唯一的。
    - 该名称可以包含 2 到 50 个字符。
    - 名称必须以字母开头，只能包含字母、数字和连字符。
5. 选择“订阅”查看可用订阅列表。 如果不确定要使用哪个订阅，请使用默认的（或建议的）订阅。 仅当工作或学校帐户与多个 Azure 订阅关联时，才会显示多个选项。
6. 选择“资源组”查看可用资源组列表，或选择“新建”创建新的资源组。 [详细了解](../azure-resource-manager/resource-group-overview.md)资源组。
7. 选择“位置”，为保管库选择地理区域。 保管库必须与要备份的 VM 位于同一区域。
8. 选择“创建”。
    - 创建保管库可能需要一段时间。
    - 可以在门户的右上区域中监视状态通知。
    ![备份保管库列表](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

创建保管库后，它会显示在“恢复服务保管库”的列表中。 如果未看到创建的保管库，请选择“刷新”。

## <a name="set-up-storage-replication"></a>设置存储复制

默认情况下，保管库采用[异地冗余存储 (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)。 我们建议对主要备份使用 GRS，但也可以使用更经济的[本地冗余存储](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。 

按如下所述修改存储复制：

1. 在保管库中选择“备份基础结构”，然后单击“备份配置”

   ![备份保管库列表](./media/backup-azure-arm-vms-prepare/full-blade.png)

2. 在“备份配置”中，根据需要修改存储冗余方法，然后选择“保存”。


## <a name="configure-backup"></a>配置备份

发现订阅中的 VM 并配置备份。

1. 在保管库中选择“概述”，然后单击“+备份”

   ![“备份”按钮](./media/backup-azure-arm-vms-prepare/backup-button.png)

   此时会打开“备份”和“备份目标”窗格。

2. 在“备份目标”> “工作负荷在哪里运行?”中，选择“Azure”。 在“要备份哪些内容?”中，选择“虚拟机” >  “确定”。 这会在保管库中注册 VM 扩展。

   ![“备份”和“备份目标”窗格](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   此步骤向保管库注册 VM 扩展。 随后将会关闭“备份目标”窗格，并打开“备份策略”窗格。

3. 在“备份策略”中，选择要与保管库关联的策略。 然后单击“确定”。
    - 默认策略的详细信息会在下拉菜单下列出。
    - 单击“新建”以创建策略。 [详细了解](backup-azure-vms-first-look-arm.md#defining-a-backup-policy)如何定义策略。

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

- 备份策略将会根据备份计划运行。
- 无论 VM 是否在运行，备份服务都会安装备份扩展。
    - VM 运行时，很有可能会获得应用程序一致的恢复点。
    -  但是，即使 VM 已关闭且无法安装扩展，也仍会备份 VM。 此类 VM 称为“脱机 VM”。 在这种情况下，恢复点将是 *崩溃一致性*恢复点。
- 若要立即为 VM 生成按需备份，请在“备份项”中，单击 VM 旁边的省略号 (...)，然后选择“立即备份”。


## <a name="next-steps"></a>后续步骤

- 排查 [Azure VM 代理](/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)或 [Azure VM 备份](backup-azure-vms-troubleshoot.md)出现的任何问题。
- [备份 Azure VM](backup-azure-vms-first-look-arm.md)
