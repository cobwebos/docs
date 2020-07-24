---
title: 设置 Azure VMware 解决方案的 Azure 备份服务器
description: 使用 Azure 备份服务器设置 Azure VMware 解决方案环境以备份虚拟机。
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: c45d2030e282057c487d142669ab634cd81f6929
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079530"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>设置 Azure VMware 解决方案的 Azure 备份服务器

Azure 备份服务器是一种强健的企业备份和恢复系统，可用于业务连续性和灾难恢复（BCDR）策略。 在 Azure VMware 解决方案预览期间，只能使用 Azure 备份服务器配置虚拟机（VM）级别的备份。 

Azure 备份服务器可以将备份数据存储到：

- **磁盘**：对于短期存储，Azure 备份服务器将数据备份到磁盘池。
- **Azure**：对于短期和长期存储的本地存储，可以使用 Azure 备份将存储在磁盘池中的 Azure 备份服务器数据备份到 Microsoft Azure 云。

当发生中断且源数据不可用时，可以使用 Azure 备份服务器轻松地将数据还原到源或备用位置。 这样一来，如果原始数据由于计划或意外问题而不可用，则可以轻松地将数据还原到备用位置。

本文将帮助你准备 Azure VMware 解决方案环境，以便通过使用 Azure 备份服务器来备份 Vm。 本文逐步讲解： 

> [!div class="checklist"]
> * 确定要使用的建议 VM 磁盘类型和大小。
> * 创建存储恢复点的恢复服务保管库。
> * 设置恢复服务保管库的存储复制。
> * 将存储添加到 Azure 备份服务器。

## <a name="supported-vmware-features"></a>支持的 VMware 功能

- **无代理备份：** Azure 备份服务器不需要在 vCenter 或 ESXi 服务器上安装代理来备份虚拟机。 相反，只需提供 IP 地址或完全限定的域名（FQDN）和用于在 Azure 备份服务器中对 VMware 服务器进行身份验证的登录凭据。
- **云集成的备份：** Azure 备份服务器保护工作负荷到磁盘和云。 Azure 备份服务器的备份和恢复工作流有助于管理长期保留和非现场备份。
- **检测并保护 vCenter 管理的 vm：** Azure 备份服务器检测并保护在 vCenter 或 ESXi 服务器上部署的 Vm。 Azure 备份服务器还会检测由 vCenter 管理的 Vm，以便可以保护大型部署。
- **文件夹级 start-autoprotection：** vCenter 允许在 vm 文件夹中组织 vm。 Azure 备份服务器检测这些文件夹，你可以使用它来保护文件夹级别的 Vm，其中包括所有子文件夹。 保护文件夹时，Azure 备份服务器不仅保护该文件夹中的 Vm，还保护稍后添加的 Vm。 Azure 备份服务器每日检测新 Vm 并自动保护它们。 当你在递归文件夹中组织 Vm 时，Azure 备份服务器会自动检测并保护递归文件夹中部署的新 Vm。
- **Azure 备份服务器继续保护群集中的 Vmotion vm：** 当 Vm 在群集内 Vmotion 进行负载平衡时，Azure 备份服务器会自动检测并继续 VM 保护。
- **更快恢复所需的文件：** Azure 备份服务器可以从 Windows VM 恢复文件或文件夹，而无需恢复整个 VM。

## <a name="limitations"></a>限制

- 必须安装 Azure 备份服务器 v3 的更新汇总1。
- 在第一次 Azure 备份服务器备份之前，你无法备份用户快照。 Azure 备份服务器完成首次备份后，可以备份用户快照。
- Azure 备份服务器无法使用传递磁盘和物理原始设备映射（pRDMs）来保护 VMware Vm。
- Azure 备份服务器无法检测或保护 VMware vApps。

若要设置 Azure VMware 解决方案的 Azure 备份服务器，必须完成以下步骤：

- 设置先决条件和环境。
- 创建恢复服务保管库。
- 下载并安装 Azure 备份服务器。
- 将存储添加到 Azure 备份服务器。

### <a name="deployment-architecture"></a>部署体系结构

Azure 备份服务器部署为 Azure 基础结构即服务（IaaS） VM 来保护 Azure VMware 解决方案 Vm。

:::image type="content" source="media/avs-backup/deploy-mabs-avs-diagram.png" alt-text="AVS 部署体系结构" border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Azure 备份服务器环境的先决条件

在 Azure 环境中安装 Azure 备份服务器时，请考虑本节中的建议。

### <a name="azure-virtual-network"></a>Azure 虚拟网络

请确保在[Azure 中为 VMware 私有云配置网络](tutorial-configure-networking.md)。

### <a name="determine-the-size-of-the-virtual-machine"></a>确定虚拟机的大小

你必须在之前步骤中创建的虚拟网络中创建 Windows 虚拟机。 选择用于运行 Azure 备份服务器的服务器时，请从 Windows Server 2019 Datacenter 的库映像开始。 本教程[在 Azure 门户中创建你的第一个 Windows 虚拟机](../virtual-machines/windows/quick-create-portal.md)，你可以开始使用 azure 中的建议 VM，即使你从未使用过 azure 也是如此。

下表汇总了每个 Azure 备份服务器虚拟机大小的受保护工作负荷的最大数目。 该信息基于内部性能以及使用工作负荷大小和改动的规范值进行的规模测试。 实际工作负荷大小可以更大，但应由附加到 Azure 备份服务器虚拟机的磁盘容纳。

| 最大保护工作负荷 | 平均工作负荷大小 | 平均工作负荷改动（每天） | 最小存储 IOPS | 建议的磁盘类型/大小      | 建议的 VM 大小 |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | 净改动 5%                   | 2,000             | 标准 HDD （8 TB 或更大的磁盘大小）  | A4V2       |
| 40                      | 150 GB                | 净改动 10%                  | 4,500             | 高级 SSD * （每个磁盘大小为 1 TB 或以上） | DS3_V2     |
| 60                      | 200 GB                | 净改动 10%                  | 10500            | 高级 SSD * （每个磁盘的大小为 8 TB 或以上） | DS3_V2     |

* 若要获取所需的 IOPs，请使用最小建议大小或更大的磁盘。 大小较小的磁盘提供较低的 IOPs。

> [!NOTE]
> Azure 备份服务器设计为在专用的单一用途服务器上运行。 不能在以下计算机上安装 Azure 备份服务器：
> * 作为域控制器运行。
> * 已安装应用程序服务器角色。
> * 是 System Center Operations Manager 的管理服务器。
> * 运行 Exchange Server。
> * 是群集的节点。

### <a name="disks-and-storage"></a>磁盘和存储

Azure 备份服务器需要安装磁盘，其中包括系统文件、安装文件、必备软件、数据库文件和存储池的专用磁盘。

| 要求                      | 建议大小  |
|----------------------------------|-------------------------|
| Azure 备份服务器安装                | 安装位置： 3 GB<br />数据库文件驱动器：900 MB<br />系统驱动器： 1 GB 用于 SQL Server 安装<br /><br />你还需要一些空间，以便 Azure 备份服务器在存档时将文件目录复制到临时安装位置。      |
| 用于存储池的磁盘<br />（使用基本卷，不能在动态磁盘上） | 受保护数据的大小的两到三倍。<br />有关详细的存储计算，请参阅[DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301)。   |

若要了解如何将新的托管数据磁盘附加到现有的 Azure VM，请参阅[使用 Azure 门户将托管数据磁盘附加到 WINDOWS VM](../virtual-machines/windows/attach-managed-disk-portal.md)。

> [!NOTE]
> 单个 Azure 备份服务器的存储池的软限制为 120 TB。

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>将备份数据存储在本地磁盘和 Azure 中

将备份数据存储在 Azure 中会减少 Azure 备份服务器 VM 上的备份基础结构。 对于操作恢复（备份），Azure 备份服务器将备份数据存储在附加到 VM 的 Azure 磁盘上。 磁盘和存储空间附加到 VM 后，Azure 备份服务器会为你管理存储。 备份数据存储量取决于连接到每个 Azure VM 的磁盘数量和大小。 Azure VM 的每个大小都有最多可附加的磁盘数。 例如，A2 为四个磁盘，A3 为8个磁盘，A4 为16个磁盘。 同样，磁盘的大小和数量决定了备份存储池的总容量。

> [!IMPORTANT]
> *不*应在超过5天的 Azure 备份服务器连接的磁盘上保留操作恢复数据。 如果数据超过五天，请将数据存储在恢复服务保管库中。

若要将备份数据存储在 Azure 中，请创建或使用恢复服务保管库。 准备备份 Azure 备份服务器工作负荷时，将[配置恢复服务保管库](#create-a-recovery-services-vault)。 配置完成后，每次运行联机备份作业时，都会在保管库中创建恢复点。 每个恢复服务保管库最多可容纳9999个恢复点。 根据创建的恢复点的数量和保留的时间，可以保留多年的备份数据。 例如，可以创建每月恢复点，并将其保留5年。

> [!IMPORTANT]
> 无论是将备份数据发送到 Azure 还是在本地保留，都必须使用恢复服务保管库注册 Azure 备份服务器。

### <a name="scale-deployment"></a>规模部署

如果想要扩展部署，可以使用以下选项：

- **向上缩放**：将 Azure 备份服务器 VM 的大小从一个序列增加到 DS3 系列，并增加本地存储空间。
- **卸载数据**：将较旧的数据发送到 Azure 并仅保留附加到 Azure 备份服务器计算机上的存储中的最新数据。
- **横向扩展**：添加更多 Azure 备份服务器计算机来保护工作负荷。

### <a name="net-framework"></a>.NET framework

VM 必须安装 .NET Framework 3.5 SP1 或更高版本。

### <a name="join-a-domain"></a>加入域

必须将 Azure 备份服务器 VM 加入到域中，并且必须在 VM 上具有管理员权限的域用户才能安装 Azure 备份服务器。

尽管在预览时不支持，但在 Azure VM 中部署的 Azure 备份服务器可以在 Azure VMware 解决方案中备份 Vm 上的工作负荷。 工作负荷应在同一个域中，以启用备份操作。

## <a name="create-a-recovery-services-vault"></a>创建恢复服务保管库

恢复服务保管库是存储一段时间内创建的恢复点的存储实体。 它还包含与受保护项关联的备份策略。

1. 在 [Azure 门户](https://portal.azure.com/)中登录到自己的订阅。

1. 在左侧菜单中，选择“所有服务”。

   ![选择“所有服务”](../backup/media/backup-create-rs-vault/click-all-services.png)

1. 在 "**所有服务**" 对话框中，输入**恢复服务**，并从列表中选择 "**恢复服务保管库**"。

   ![输入并选择“恢复服务保管库”](../backup/media/backup-create-rs-vault/all-services.png)

   此时会显示订阅中的恢复服务保管库列表。

1. 在“恢复服务保管库”仪表板上，选择“添加” 。

   ![添加恢复服务保管库](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   此时会打开“恢复服务保管库”对话框。

1. 输入 "**名称**"、"**订阅**"、"**资源组**" 和 "**位置**" 的值。

   ![配置恢复服务保管库](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **名称**：输入一个友好名称以标识此保管库。 名称对于 Azure 订阅必须是唯一的。 指定一个名称，该名称至少有两个字符，但不能超过50个字符。 名称必须以字母开头且只能包含字母、数字和连字符。
   - **订阅**：选择要使用的订阅。 如果你仅是一个订阅的成员，则会看到该名称。 如果不确定要使用哪个订阅，请使用默认的（建议的）订阅。 仅当工作或学校帐户与多个 Azure 订阅关联时，才会显示多个选项。
   - **资源组**：使用现有资源组，或创建一个新的资源组。 若要查看订阅中可用的资源组列表，请选择“使用现有资源”****，然后从下拉列表中选择一个资源。 若要创建新资源组，请选择“新建”，然后输入名称。
   - **位置**：为保管库选择地理区域。 若要创建保管库来保护 Azure VMware 解决方案虚拟机，保管库*必须*与 Azure vmware 解决方案私有云位于同一区域。

1. 准备好创建恢复服务保管库后，选择“创建”。

   ![创建恢复服务保管库](../backup/media/backup-create-rs-vault/click-create-button.png)

   创建恢复服务保管库可能需要一段时间。 在门户右上角的 "**通知**" 区域中监视状态通知。 创建保管库后，它会显示在“恢复服务保管库”的列表中。 如果未看到创建的保管库，请选择“刷新”。

   ![刷新备份保管库列表](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>设置存储复制

存储复制选项可让你在异地冗余存储（默认）和本地冗余存储之间进行选择。 异地冗余存储将你的存储帐户中的数据复制到次要区域，这会使你的数据持久保存。 本地冗余存储是一种成本较低的选项，不是持久的。 若要详细了解异地冗余和本地冗余存储选项，请参阅[Azure 存储冗余](../storage/common/storage-redundancy.md)。

> [!IMPORTANT]
> 在保管库中配置备份之前，必须先更改存储复制类型的设置，恢复服务保管库在**本地冗余/异地冗余**。 配置备份后，对其进行修改的选项将被禁用，并且你无法更改存储复制类型。

1. 在 "**恢复服务保管库**" 中，选择新保管库。 

1. 在“设置”下，选择“属性”********。 在 "**备份配置**" 下，选择 "**更新**"。

1. 选择存储复制类型，然后选择“保存”。

   ![设置新保管库的存储配置](../backup/media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="download-and-install-the-software-package"></a>下载并安装软件包

按照本部分中的步骤下载、提取和安装软件包。

### <a name="download-the-software-package"></a>下载软件包

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 如果已打开恢复服务保管库，则继续执行下一步。 如果未打开恢复服务保管库，但正在 Azure 门户，请在主菜单上选择 "**浏览**"。

   1. 在资源列表中，输入“恢复服务”****。

   1. 开始键入时，会根据输入筛选该列表。 出现“**恢复服务保管库**”时，请选择它。

   ![创建恢复服务保管库步骤1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. 在恢复服务保管库列表中选择一个保管库。

   此时会打开选定的保管库仪表板。

   ![打开保管库仪表板](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   默认情况下会打开 "**设置**" 选项。 如果已关闭，请选择 "**设置**" 以将其打开。

   ![打开保管库设置选项](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. 选择 "**备份**" 打开**入门**向导。

   ![入门向导打开备份](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. 在打开的窗口中，执行以下操作：

   1. 从 "**工作负荷的运行位置"** 菜单中选择 **"本地**"。

      :::image type="content" source="media/avs-backup/deploy-mabs-on-premises-workload.png" alt-text="你的工作负载在哪里运行？":::

   1. 从 "**要备份的内容？** " 菜单中，选择要使用 Azure 备份服务器保护的工作负荷。

   1. 选择 "**准备基础结构**"，下载并安装 Azure 备份服务器和保管库凭据。

      :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure.png" alt-text="准备基础结构":::

1. 在打开的 "**准备基础结构**" 窗口中，执行以下操作：

   1. 选择要安装 Azure 备份服务器的**下载**链接。

   1. 下载保管库凭据，方法是选择**已下载的或使用最新的 Azure 备份服务器安装**复选框，然后选择 "**下载**"。 在将 Azure 备份服务器注册到恢复服务保管库期间，请使用保管库凭据。 这些链接会将你转到下载中心，你可以在其中下载软件包。

   :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="准备基础结构-Azure 备份服务器":::

1. 在下载页上，选择 "所有文件"，然后选择 "**下一步**"。

   > [!NOTE]
   > 您必须将所有文件下载到同一个文件夹。 由于文件的下载大小一起大于 3 GB，下载完成可能需要长达60分钟的时间。 

   ![选择下载中心中的文件](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>提取软件包

如果已将软件包下载到其他服务器，请将文件复制到创建的虚拟机以部署 Azure 备份服务器。

> [!WARNING]
> 至少需要有 4GB 的可用空间才能解压缩安装程序文件。

1. 下载所有文件之后，双击**MicrosoftAzureBackupInstaller.exe**打开 " **Microsoft Azure 备份**安装向导"，然后选择 "**下一步**"。

1. 选择要将文件解压缩到的位置，然后选择 "**下一步**"。

1. 选择 "**提取**" 开始解压缩过程。

   ![Microsoft Azure 备份安装向导](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. 提取后，选择要**执行 setup.exe**的选项，然后选择 "**完成**"。

> [!TIP]
> 你还可以从解压缩软件包的文件夹中找到 setup.exe 文件。

### <a name="install-the-software-package"></a>安装软件包

1. 在 "**安装**" 下的安装程序窗口中，选择 " **Microsoft Azure 备份**" 以打开安装向导。

   ![Microsoft Azure 备份安装向导安装按钮](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. 在**欢迎**屏幕上，选择 "**下一步**" 继续执行 "**先决条件检查**" 页。

1. 再次选择 "**检查**" 以确定是否满足 Azure 备份服务器的硬件和软件先决条件。 如果成功满足，则选择 "**下一步**"。

   ![Azure 备份服务器先决条件检查](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Azure 备份服务器安装包与所需的相应 SQL Server 二进制文件捆绑在一起。 当你启动新的 Azure 备份服务器安装时，请选择 "**使用此安装程序选项安装 SQL Server 的新实例**"。 然后选择 "**检查并安装**"。

   ![Azure 备份服务器 SQL Server 检查](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > 如果要使用自己的 SQL Server 实例，则支持的 SQL Server 版本 SQL Server 2014 SP1 或更高版本、2016和2017。 所有 SQL Server 版本应为标准版或企业版 64 位。 Azure 备份服务器不适用于远程 SQL Server 实例。 Azure 备份服务器使用的实例必须在本地。 如果对 Azure 备份服务器使用现有 SQL Server 实例，则安装程序仅支持 SQL Server 的*命名实例*的使用。

   如果出现故障，并建议重新启动计算机，请执行此操作，然后选择 "**再次检查**"。 如果存在任何 SQL Server 配置问题，请根据 SQL Server 准则重新配置 SQL Server。 然后，使用 SQL Server 的现有实例重试安装或升级 Azure 备份服务器。

   **手动配置**

   使用你自己的 SQL Server 实例时，请确保将 builtin\Administrators 添加到数据库的 sysadmin 角色。

   **带有 SQL Server 2017 的 SSRS 配置**

   使用自己 SQL Server 2017 的实例时，必须手动配置 SQL Server 2017 Reporting Services （SSRS）。 在 SSRS 配置后，确保 SSRS 的**IsInitialized**属性设置为**True**。 如果此属性设置为 " **True**"，则 Azure 备份服务器假设已配置 ssrs，并跳过 ssrs 配置。

   若要检查 SSRS 配置状态，请运行以下命令：

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   对 SSRS 配置使用以下值：

   * **服务帐户**：**使用内置帐户**应为**Network Service**。
   * **Web 服务 URL**：应**ReportServer_ \<SQLInstanceName> ****虚拟目录**。
   * **数据库**： **DatabaseName**应为**ReportServer $ \<SQLInstanceName> **。
   * **Web 门户 URL**：应**Reports_ \<SQLInstanceName> ****虚拟目录**。

   [详细了解](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) SSRS 配置。

   > [!NOTE]
   > [Microsoft Online Services 条款](https://www.microsoft.com/licensing/product-licensing/products)（OST）控制用作 Azure 备份服务器数据库的 SQL Server 的许可。 根据 OST，与 Azure 备份服务器捆绑 SQL Server 只能用作 Azure 备份服务器的数据库。

1. 安装成功后，选择 "**下一步**"。

1. 提供安装 Microsoft Azure 备份 Server 文件的位置，然后选择 "**下一步**"。

   > [!NOTE]
   > 备份到 Azure 需要暂存位置。 请确保暂存位置的空间至少为要备份到云的数据的 5%。 对于磁盘保护，需要在安装完成后配置单独的磁盘。 有关存储池的详细信息，请参阅[配置存储池和磁盘存储](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12))。

   ![Microsoft Azure 备份安装程序安装设置](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. 为受限制的本地用户帐户提供强密码，然后选择 "**下一步**"。

   ![Microsoft Azure 备份安装安全设置](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. 选择是否要使用 Microsoft 更新来检查更新，然后选择 "**下一步**"。

   > [!NOTE]
   > 我们建议让 Windows 更新重定向到 Microsoft 更新，此网站为 Windows 和 Azure 备份服务器等其他产品提供了安全更新与重要更新。

   ![Microsoft Azure 备份安装程序 Microsoft 更新选择加入](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. 查看**设置摘要**，然后选择 "**安装**"。

   安装会分阶段进行。 第一阶段安装 Microsoft Azure 恢复服务代理，第二阶段检查是否有 internet 连接。 如果 internet 连接可用，则可以继续安装。 如果不是，则必须提供代理详细信息以连接到 internet。 最后一个阶段检查必备的软件。 如果未安装，任何缺少的软件都将与 Microsoft Azure 恢复服务代理一起安装。

1. 选择 "**浏览**" 以查找保管库凭据以将计算机注册到恢复服务保管库，然后选择 "**下一步**"。

1. 选择密码短语，对在 Azure 和本地之间发送的数据进行加密或解密。

   > [!TIP]
   > 可以自动生成通行短语，或提供自己的通行短语（最少包含 16 个字符）。

1. 输入要保存密码短语的位置，然后选择 "**下一步**" 以注册服务器。

   > [!IMPORTANT]
   > 将通行短语保存到本地服务器之外的安全位置。 强烈建议使用 Azure Key Vault 来存储通行短语。

   Microsoft Azure 恢复服务代理安装完成后，安装步骤会转到 SQL Server 和 Azure 备份服务器组件的安装和配置。

   ![Microsoft Azure 备份安装程序安装](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. 安装步骤完成后，选择 "**关闭**"。

### <a name="install-update-rollup-1"></a>安装更新汇总1

在可以保护工作负荷之前，必须先安装 Azure 备份服务器 v3 的更新汇总1。 若要查看 bug 修复的列表以及 Azure 备份服务器 v3 更新汇总1的安装说明，请参阅知识库文章[4534062](https://support.microsoft.com/en-us/help/4534062/)。

## <a name="add-storage-to-azure-backup-server"></a>将存储添加到 Azure 备份服务器

Azure 备份服务器 v3 支持提供新式备份存储：

-  存储节省了50%。
-  速度快三倍的备份。
-  更高效的存储。
-  可识别工作负荷的存储。

### <a name="volumes-in-azure-backup-server"></a>Azure 备份服务器中的卷

将具有所需存储容量的数据磁盘添加到 Azure 备份服务器虚拟机（如果尚未添加）。

Azure 备份服务器 v3 仅接受存储卷。 添加卷时，Azure 备份服务器会将卷格式化为复原文件系统（ReFS），新式备份存储需要该卷。

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>将卷添加到 Azure 备份服务器磁盘存储

1. 在 "**管理**" 窗格中，重新扫描存储，然后选择 "**添加**"。 

1. 从可用卷中选择要添加到存储池的卷。 

1. 添加可用卷后，为它们提供友好名称以帮助你对其进行管理。 

1. 选择 **"确定"** 将这些卷格式化为 ReFS，以便 Azure 备份服务器可以使用新式备份存储的优点。

![添加可用卷](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="next-steps"></a>后续步骤

继续学习下一教程，了解如何使用 Azure 备份服务器配置 Azure VMware 解决方案上运行的 VMware Vm 的备份。

> [!div class="nextstepaction"]
> [配置 Azure VMware 解决方案 Vm 的备份](backup-avs-vms-with-mabs.md)
