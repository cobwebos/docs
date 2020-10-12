---
title: 在 Azure Stack 上安装 Azure 备份服务器
description: 本文介绍如何使用 Azure 备份服务器保护或备份 Azure Stack 中的工作负荷。
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 7153e2ff03a4f78ee1cc92ca04054fb2955d11a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90970235"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>在 Azure Stack 上安装 Azure 备份服务器

本文介绍如何在 Azure Stack 上安装 Azure 备份服务器。 使用 Azure 备份服务器可以保护 Azure Stack 中运行的基础结构即服务 (IaaS) 工作负荷，例如虚拟机。 使用 Azure 备份服务器保护工作负荷的优势是可以从单个控制台管理所有工作负荷的保护。

> [!NOTE]
> 若要了解安全功能，请参阅 [Azure 备份安全功能文档](backup-azure-security-feature.md)。
>

## <a name="azure-backup-server-protection-matrix"></a>Azure 备份服务器保护矩阵

Azure 备份服务器保护以下 Azure Stack 虚拟机工作负荷。

| 受保护的数据源 | 保护和恢复 |
| --------------------- | ----------------------- |
| Windows Server 半年频道 - Datacenter/Enterprise/Standard | 卷、文件、文件夹 |
| Windows Server 2016 - Datacenter/Enterprise/Standard | 卷、文件、文件夹 |
| Windows Server 2012 R2 - Datacenter/Enterprise/Standard | 卷、文件、文件夹 |
| Windows Server 2012 - Datacenter/Enterprise/Standard | 卷、文件、文件夹 |
| Windows Server 2008 R2 - Datacenter/Enterprise/Standard | 卷、文件、文件夹 |
| SQL Server 2016 | 数据库 |
| SQL Server 2014 | 数据库 |
| SQL Server 2012 SP1 | 数据库 |
| SharePoint 2016 | 场、数据库、前端、Web 服务器 |
| SharePoint 2013 | 场、数据库、前端、Web 服务器 |
| SharePoint 2010 | 场、数据库、前端、Web 服务器 |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Azure 备份服务器环境的先决条件

在 Azure Stack 环境中安装 Azure 备份服务器时，请考虑本部分中的建议。 Azure 备份服务器安装程序会检查环境中是否安装必备组件，但在安装之前做好准备可以节省时间。

### <a name="determining-size-of-virtual-machine"></a>确定虚拟机大小

若要在 Azure Stack 虚拟机上运行 Azure 备份服务器，请使用 A2 大小或更大大小。 为了帮助选择虚拟机大小，请下载 [Azure Stack VM 大小计算器](https://www.microsoft.com/download/details.aspx?id=56832)。

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Azure Stack 虚拟机上的虚拟网络

Azure Stack 工作负荷中使用的所有虚拟机都必须属于同一 Azure 虚拟网络和 Azure 订阅。

### <a name="azure-backup-server-vm-performance"></a>Azure 备份服务器 VM 性能

如果与其他虚拟机共享，存储帐户大小和 IOPS 限制会影响 Azure 备份服务器 VM 的性能。 出于这个原因，应该对 Azure 备份服务器虚拟机使用单独的存储帐户。 在 Azure 备份服务器上运行的 Azure 备份代理需要临时存储，以便：

- 供自己使用（一个缓存位置），
- 存储从云中还原的数据（本地临时区域）

### <a name="configuring-azure-backup-temporary-disk-storage"></a>配置 Azure 备份临时磁盘存储

每个 Azure Stack 虚拟机都附带临时磁盘存储，该磁盘存储可作为卷 `D:\` 供用户使用。 可将 Azure 备份所需的本地临时区域配置为位于 `D:\` 中，并且可将缓存位置置于 `C:\` 上。 这样，就不需要从附加到 Azure 备份服务器虚拟机的数据磁盘划分出任何存储。

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>将备份数据存储在本地磁盘和 Azure 中

Azure备份服务器将备份数据存储在附加到虚拟机的 Azure 磁盘上，以便进行操作恢复。 将磁盘和存储空间附加到虚拟机后，Azure 备份服务器将为你管理存储。 备份数据存储量取决于附加到每个 [Azure Stack 虚拟机](/azure-stack/user/azure-stack-storage-overview)的磁盘的数量和大小。 Azure Stack VM 的每个大小都有可以附加到虚拟机的最大磁盘数。 例如，A2 是四个磁盘。 A3 是八个磁盘。 A4 是 16 个磁盘。 同样，磁盘的大小和数量决定了整个备份存储池。

> [!IMPORTANT]
> **不**应在 Azure 备份服务器附加的磁盘上保留操作恢复（备份）数据超过 5 天。
>

在 Azure 中存储备份数据会缩减 Azure Stack 上的备份基础结构。 如果数据的存在已超过五天，应将其存储在 Azure 中。

若要将备份数据存储在 Azure 中，请创建或使用恢复服务保管库。 准备备份 Azure 备份服务器工作负荷时，请[配置恢复服务保管库](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault)。 配置后，每次运行备份作业时，都会在保管库中创建恢复点。 每个恢复服务保管库最多可容纳 9999 个恢复点。 根据创建的恢复点的数量和保留的时间，可以保留多年的备份数据。 例如，可以每月创建恢复点，并将其保留五年。

### <a name="scaling-deployment"></a>扩展部署

如果想要扩展部署，可以使用以下选项：

- 纵向扩展 - 将 Azure 备份服务器虚拟机的大小从 A 系列增加到 D 系列，并[根据 Azure Stack 虚拟机说明](/azure-stack/user/azure-stack-manage-vm-disks)增加本地存储。
- 卸载数据 - 将旧数据发送到 Azure，并仅保留附加到 Azure 备份服务器的存储上的最新数据。
- 横向扩展 - 添加更多 Azure 备份服务器来保护工作负荷。

### <a name="net-framework"></a>.NET framework

必须在虚拟机上安装 .NET Framework 3.5 SP1 或更高版本。

### <a name="joining-a-domain"></a>加入域

Azure 备份服务器虚拟机必须加入域。 拥有管理员特权的域用户必须在虚拟机上安装 Azure 备份服务器。

## <a name="using-an-iaas-vm-in-azure-stack"></a>在 Azure Stack 中使用 IaaS VM

为 Azure 备份服务器选择服务器时，请从 Windows Server 2012 R2 Datacenter 或 Windows Server 2016 Datacenter 库映像着手。 [在 Azure 门户中创建第一个 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)一文提供了使用建议的虚拟机的入门教程。 建议服务器虚拟机 (VM) 最低要求应为：包含 2 核、3.5 GB RAM 的 A2 标准。

使用 Azure 备份服务器保护工作负荷有许多细微差异需要注意。 [MABS 的保护矩阵](./backup-mabs-protection-matrix.md)帮助解释了这些细微差异。 部署计算机前，请先阅读完本文。

> [!NOTE]
> Azure 备份服务器设计为在专用的单一用途虚拟机上运行。 无法在上安装 Azure 备份服务器：
>
> - 作为域控制器运行的计算机
> - 安装了应用程序服务器角色的计算机
> - 运行 Exchange Server 的计算机
> - 作为群集节点的计算机

请始终将 Azure 备份服务器加入域。 如果需要将 Azure 备份服务器移到其他域，请先安装 Azure 备份服务器，然后将其加入新域。 部署 Azure 备份服务器后，无法将其移到新域。

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>设置存储复制

使用恢复服务保管库存储复制选项可在异地冗余存储与本地冗余存储之间进行选择。 默认情况下，恢复服务保管库使用异地冗余存储。 如果此保管库是主保管库，请保留异地冗余存储这一存储选项。 如果需要更便宜的选项，请选择 "本地冗余存储"。 有关详细信息，请参阅[Azure 存储复制概述](../storage/common/storage-redundancy.md)中的[异地冗余](../storage/common/storage-redundancy.md#geo-redundant-storage)、[本地冗余](../storage/common/storage-redundancy.md#locally-redundant-storage)和[区域冗余](../storage/common/storage-redundancy.md#zone-redundant-storage)存储选项。

若要编辑存储复制设置，请执行以下操作：

1. 选择保管库以打开保管库仪表板和“设置”菜单。 如果 " **设置** " 菜单未打开，请在保管库仪表板中选择 " **所有设置** "。
2. 在 "**设置**" 菜单上，选择 "**备份基础结构**  >  **备份配置**"，打开 "**备份配置**" 菜单。 在“备份配置”菜单中，选择保管库的存储复制选项。

    ![备份保管库列表](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>下载 Azure 备份服务器安装程序

可通过两种方式下载 Azure 备份服务器安装程序。 从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=55269)下载 Azure 备份服务器安装程序。 在配置恢复服务保管库时，还可以下载 Azure 备份服务器安装程序。 以下步骤引导你在配置恢复服务保管库时从 Azure 门户下载安装程序。

1. 在 Azure 门户中，从 Azure Stack 虚拟机[登录到自己的 Azure 订阅](https://portal.azure.com/)。
2. 在左侧菜单中，选择“所有服务”。

    ![在主菜单中选择“所有服务”选项](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. 在“所有服务”对话框中，键入“恢复服务”。 开始键入时，会根据输入筛选资源列表。 出现“恢复服务保管库”时，请选择它。

    ![在“所有服务”对话框中键入“恢复服务”](./media/backup-mabs-install-azure-stack/all-services.png)

    此时会显示订阅中的恢复服务保管库列表。

4. 在恢复服务保管库列表中，选择保管库以打开其仪表板。

    ![选择保管库以打开仪表板](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. 在保管库的 "入门" 菜单中，选择 " **备份** " 打开 "入门向导"。

    ![备份入门](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    此时会打开备份菜单。

    ![Backup-goals-default-opened](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. 在备份菜单中，从“工作负荷的运行位置”菜单中选择“本地”。  从“希望备份什么内容?”下拉菜单中，选择想要使用 Azure 备份服务器保护的工作负荷。 如果不确定要选择的工作负荷，请选择 " **Hyper-v 虚拟机** "，然后选择 " **准备基础结构**"。

    ![用作目标的“本地”和“工作负荷”](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    此时会打开“准备基础结构”菜单。

7. 在 " **准备基础结构** " 菜单中，选择 " **下载** " 以打开网页以下载 Azure 备份服务器安装文件。

    ![快速启动向导更改](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    此时会打开存放了 Azure 备份服务器可下载文件的 Microsoft 网页。

8. 在 Microsoft Azure 备份服务器下载 "页上，选择一种语言，然后选择" **下载**"。

    ![下载中心打开](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Azure 备份服务器安装程序由 8 个文件组成 - 1 个安装程序，以及 7 个 .bin 文件。 选中 " **文件名** "，选择所有必需的文件，然后选择 " **下一步**"。 将所有文件下载到同一个文件夹。

    ![下载中心、选定的文件](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    所有安装文件的下载大小超过 3 GB。 通过 10-Mbps 的下载链路下载所有安装文件可能需要长达 60 分钟时间。 文件将下载到指定的下载位置。

## <a name="extract-azure-backup-server-install-files"></a>提取 Azure 备份服务器安装文件

将所有文件下载到 Azure Stack 虚拟机后，请转到下载位置。 安装 Azure 备份服务器的第一阶段是提取文件。

![下载 MABS 安装程序](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. 若要开始安装，请从下载的文件列表中选择 " **MicrosoftAzureBackupserverInstaller.exe**"。

    > [!WARNING]
    > 至少需要有 4GB 的可用空间才能解压缩安装程序文件。
    >

2. 在 Azure 备份服务器向导中，选择 " **下一步** " 继续。

    ![Microsoft Azure 备份安装向导](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. 选择 Azure 备份服务器文件的路径，然后选择 " **下一步**"。

   ![为文件选择目标](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. 验证提取位置，然后选择 " **提取**"。

   ![验证提取位置](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. 向导会提取文件，并为安装过程做好准备。

   ![向导提取文件](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. 提取过程完成后，选择 " **完成**"。 默认已选择“执行 setup.exe”。 选择 " **完成**" 后，Setup.exe 将 Microsoft Azure 备份服务器安装到指定位置。

   ![安装程序提取 Microsoft Azure 备份服务器文件](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>安装软件包

在上一步中，选择 " **完成** " 退出提取阶段，然后启动 Azure 备份服务器安装向导 "。

![Microsoft Azure 备份安装向导启动](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure 备份服务器与 Data Protection Manager 共享代码。 你将在 Azure 备份服务器安装程序中看到对 Data Protection Manager 和 DPM 的引用。 尽管 Azure 备份服务器和 Data Protection Manager 属于不同的产品，但两者密切相关。

1. 若要启动安装向导，请选择 " **Microsoft Azure 备份服务器**"。

   ![选择 Microsoft Azure 备份服务器](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. 在 **欢迎** 屏幕上，选择 " **下一步**"。

    ![Azure 备份服务器-欢迎](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. 在 " **先决条件检查** " 屏幕上，选择 " **检查** " 以确定是否满足 Azure 备份服务器的硬件和软件先决条件。

    ![Azure 备份服务器-必备项检查](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    如果你的环境具有必需的先决条件，你将看到一条消息，指示计算机满足要求。 选择“下一步”。  

    ![Azure 备份服务器 - 已通过先决条件检查](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    如果环境不符合先决条件，屏幕上会指明问题。 Dpmsetup.ini 中还列出了未满足的先决条件。 解决先决条件错误，然后运行“再次检查”。 在满足所有先决条件之前，安装无法继续。

    ![Azure 备份服务器 - 不符合安装先决条件](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure 备份服务器需要 SQL Server。 Azure 备份服务器安装包与相应的 SQL Server 二进制文件捆绑在一起。 可以根据需要使用自己的 SQL 安装。 但是，建议的选项是让安装程序添加新的 SQL Server 实例。 若要确保你的选择适用于你的环境，请选择 " **检查并安装**"。

   > [!NOTE]
   > Azure 备份服务器不能与远程 SQL Server 实例一起使用。 Azure 备份服务器使用的实例必须在本地。
   >

    ![Azure 备份服务器-SQL 设置](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    检查后，如果虚拟机具有安装 Azure 备份服务器所需的先决条件，请选择 " **下一步**"。

    ![满足 Azure 备份服务器要求](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    如果发生失败并出现重启计算机的建议，请重启计算机。 重新启动计算机后，重新启动安装程序，当你转到 " **SQL 设置** " 屏幕时，请选择 " **再次检查**"。

5. 在 " **安装设置**" 中，提供安装 Microsoft Azure 备份 server 文件的位置，然后选择 " **下一步**"。

    ![提供文件安装位置](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    备份到 Azure 需有暂存位置。 确保暂存位置的大小至少为要备份到 Azure 的数据的 5%。 在磁盘保护方面，安装完成之后需要配置独立的磁盘。 有关存储池的详细信息，请参阅 [准备数据存储](/system-center/dpm/plan-long-and-short-term-data-storage)。

6. 在 " **安全设置** " 屏幕上，为受限制的本地用户帐户提供强密码，然后选择 " **下一步**"。

    ![安全设置屏幕](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. 在 **Microsoft 更新选择加入** "屏幕上，选择是否要使用 *Microsoft 更新* 检查更新，然后选择" **下一步**"。

   > [!NOTE]
   > 我们建议让 Windows 更新重定向到 Microsoft 更新，此网站为 Windows 和 Microsoft Azure 备份服务器等其他产品提供了安全更新与重要更新。
   >

    ![Microsoft 更新 Opt-In 屏幕](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. 查看 *设置摘要* ，然后选择 " **安装**"。

    ![设置摘要](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Azure 备份服务器完成安装时，安装程序会立即启动 Microsoft Azure 恢复服务代理安装程序。

9. Microsoft Azure 恢复服务代理安装程序将打开，并检查是否已建立 Internet 连接。 如果 Internet 连接可用，请继续安装。 如果没有连接，请提供代理详细信息以连接到 Internet。 指定代理设置后，选择 " **下一步**"。

    ![代理配置](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. 若要安装 Microsoft Azure 恢复服务代理，请选择 " **安装**"。

    ![代理安装](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Microsoft Azure 恢复服务代理（也称为 Azure 备份代理）会将 Azure 备份服务器配置为恢复服务保管库。 配置后，Azure 备份服务器将始终将数据备份到同一个恢复服务保管库。

11. Microsoft Azure 恢复服务代理完成安装后，选择 " **下一步** " 开始下一阶段：向恢复服务保管库注册 Azure 备份服务器。

    ![代理安装已成功完成](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    安装程序启动“注册服务器向导”。

12. 切换到自己的 Azure 订阅和恢复服务保管库。 在 " **准备基础结构** " 菜单中，选择 " **下载** " 以下载保管库凭据。 如果步骤2中的 " **下载** " 按钮未处于活动状态，请选择 " **已下载" 或 "使用最新 Azure 备份服务器安装** " 来激活该按钮。 保管库凭据将下载到存储下载内容的位置。 请记住此位置，因为下一步骤需要指定它。

    ![下载保管库凭据](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. 在 **保管库标识** 菜单中，选择 " **浏览** " 查找恢复服务保管库凭据。

    ![保管库标识菜单](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    在 " **选择保管库凭据** " 对话框中，请前往下载位置，选择保管库凭据，然后选择 " **打开**"。

    凭据的路径会显示在保管库标识菜单中。 选择 " **下一步** " 以转到 **加密设置**。

14. 在 " **加密设置** " 对话框中，提供用于备份加密的密码和用于存储密码的位置，然后选择 " **下一步**"。

    ![加密设置](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    可以提供自己的通行短语，或使用通行短语生成器来创建一个。 密码是您自己的，Microsoft 不会保存或管理此密码。 若要准备灾难，请将密码保存到可访问的位置。

    选择 " **下一步**" 后，将向恢复服务保管库注册 Azure 备份服务器。 安装程序继续安装 SQL Server 和 Azure 备份服务器。

    ![安装程序将安装 SQL 和 Azure 备份服务器](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. 安装程序完成后， **状态** 显示已成功安装所有软件。

    ![软件已成功安装](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    安装完成后，会在服务器桌面上创建 Azure 备份服务器控制台和 Azure 备份服务器 PowerShell 的图标。

## <a name="add-backup-storage"></a>添加备份存储

第一个备份副本保存在已附加到 Azure 备份服务器计算机的存储中。 有关添加磁盘的详细信息，请参阅[添加新式备份存储](/system-center/dpm/add-storage)。

> [!NOTE]
> 即使你打算将数据发送到 Azure，也需要添加备份存储。 在 Azure 备份服务器体系结构中，恢复服务保管库将保存数据的第二个副本，而本地存储将保存第一个（必需的）备份副本。
>
>

## <a name="network-connectivity"></a>网络连接

Azure 备份服务器需要连接到 Azure 备份服务才能成功运行。 若要验证计算机是否已连接到 Azure，请在 Azure 备份服务器 PowerShell 控制台中使用 ```Get-DPMCloudConnection``` cmdlet。 如果 cmdlet 的输出为 TRUE，则连接存在，否则没有连接。

同时，Azure 订阅必须处于正常运行状态。 若要了解订阅的状态并对其进行管理，请登录到[订阅门户](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。

了解 Azure 连接和 Azure 订阅的状态后，可以使用下表来确定提供的备份/还原功能受到了哪些影响。

| 连接状态 | Azure 订阅 | 备份到 Azure | 备份到磁盘 | 从 Azure 还原 | 从磁盘还原 |
| --- | --- | --- | --- | --- | --- |
| 已连接 |活动 |允许 |允许 |允许 |允许 |
| 已连接 |Expired |已停止 |已停止 |允许 |允许 |
| 已连接 |已取消预配 |已停止 |已停止 |已停止且已删除 Azure 恢复点 |已停止 |
| 连接断开超过 15 天 |活动 |已停止 |已停止 |允许 |允许 |
| 连接断开超过 15 天 |Expired |已停止 |已停止 |允许 |允许 |
| 连接断开超过 15 天 |已取消预配 |已停止 |已停止 |已停止且已删除 Azure 恢复点 |已停止 |

### <a name="recovering-from-loss-of-connectivity"></a>连接断开后进行恢复

如果防火墙或代理阻止访问 Azure，请将防火墙/代理配置文件中的以下域地址添加到允许列表：

- `http://www.msftncsi.com/ncsi.txt`
- \*.Microsoft.com
- \*.WindowsAzure.com
- \*.microsoftonline.com
- \*.windows.net

在 Azure 备份服务器上恢复与 Azure 的连接后，Azure 订阅状态将确定可执行的操作。 服务器变为“已连接”状态后，请使用[“网络连接”](backup-mabs-install-azure-stack.md#network-connectivity)中的表查看可用操作。

### <a name="handling-subscription-states"></a>处理订阅状态

可将 Azure 订阅从“已过期”或“已取消预配”状态更改为“活动”状态。   当订阅状态不 *处于活动*状态时：

- 订阅处于“已取消预配”状态时，会失去功能。 将订阅还原到“活动”状态可以挽回备份/还原功能。 如果以够长的保留期来保存本地磁盘上的备份数据，则可以检索这些备份数据。 但是，一旦订阅进入“已取消预配”状态，Azure 中的备份数据便会丢失且不可检索。
- 订阅处于“已过期”状态时，会失去功能。 当订阅 *过期*时，计划的备份不会运行。

## <a name="troubleshooting"></a>故障排除

如果 Microsoft Azure 备份服务器在安装阶段（或者备份或还原时）失败并出现错误，请参阅[错误代码文档](https://support.microsoft.com/kb/3041338)。
此外，还可以参考 [Azure 备份相关的常见问题](backup-azure-backup-faq.md)

## <a name="next-steps"></a>后续步骤

[为 DPM 准备环境](/system-center/dpm/prepare-environment-for-dpm)一文包含了有关受支持 Azure 备份服务器配置的信息。

可以使用以下文章深入了解如何使用 Microsoft Azure 备份服务器来保护工作负荷。

- [SQL Server 备份](./backup-mabs-sql-azure-stack.md)
- [SharePoint Server 备份](./backup-mabs-sharepoint-azure-stack.md)
- [备用服务器备份](backup-azure-alternate-dpm-server.md)
