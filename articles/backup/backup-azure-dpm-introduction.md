<properties
	pageTitle="Azure DPM 备份简介 | Microsoft Azure"
	description="使用 Azure 备份服务备份 DPM 服务器的简介"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.date="06/12/2015"
	wacn.date=""/>

# Azure DPM 备份简介

本文介绍如何使用 Microsoft Azure 备份来保护 System Center Data Protection Manager (DPM) 服务器和工作负载。通过阅读本文，你将会了解：

- Azure DPM 服务器备份的工作原理
- 实现顺畅备份体验的先决条件
- 遇到的典型错误以及如何处理它们
- 支持的方案

System Center DPM 备份文件和应用程序数据。备份到 DPM 的数据可以存储在磁带、磁盘上，或者使用 Microsoft Azure Backup 备份到 Azure。DPM 可与 Azure 备份交互，如下所述：

- **部署为物理服务器或本地虚拟机的 DPM** — 如果 DPM 部署为物理服务器或本地 Hyper-V 虚拟机，则你除了磁盘和磁带备份外，还可以将数据备份到 Azure 备份保管库。
- **部署为 Azure 虚拟机的 DPM** — 通过 System Center 2012 R2 Update 3，可以将 DPM 部署为 Azure 虚拟机。如果 DPM 部署为 Azure 虚拟机部署，则你可以将数据备份到附加到 DPM Azure 虚拟机的 Azure 磁盘，也可以通过将数据备份到 Azure 备份保管库来卸载数据存储。

## 为何要备份 DPM 服务器？

使用 Azure 备份来备份 DPM 服务器所带来的业务好处包括：

- 对于本地 DPM 部署，你可以使用 Azure 备份来取代长期的磁带部署。
- 对于 Azure 中的 DPM 部署，Azure 备份可让你卸载 Azure 磁盘中的存储，从而通过将较旧的数据存储在 Azure 备份上，并将较新的数据存储在磁盘上来实现扩展。

## DPM 服务器备份的工作原理
若要备份虚拟机，首先需要获得数据的时间点快照。Azure 备份服务在计划的时间启动备份作业，并触发要拍摄快照的备份扩展。备份扩展会与来宾内部的 VSS 服务进行协调以确保一致性，并会在达成一致后调用 Azure 存储服务的 Blob 快照 API。这样做可以获得虚拟机磁盘的一致性快照，不必关闭虚拟机。

拍摄快照后，数据将由 Azure 备份服务传输到备份保管库中。该服务负责确定并传输自上次备份以来进行了更改的块，使备份存储和网络更高效。数据传输完成后，将会删除快照并创建恢复点。在 Azure 管理门户中，可以查看此恢复点。

>[AZURE.NOTE]Linux 虚拟机只能使用文件一致性备份。

## 先决条件
按如下所述让 Azure 备份做好备份 DPM 数据的准备：

1. **创建备份保管库** — 在 Azure 备份控制台中创建一个保管库。
2. **下载保管库凭据** — 在 Azure 备份中，将你创建的管理证书上载到保管库。
3. **安装 Azure 备份代理并注册服务器** — 通过 Azure 备份，在每个 DPM 服务器上安装代理，并在备份保管库中注册 DPM 服务器。

### 创建备份保管库
若要开始备份 Azure 虚拟机，你需要首先创建备份保管库。保管库是存储所有按时间创建的备份和恢复点的实体。保管库还包含将应用到要备份的虚拟机的备份策略。

1. 登录到[管理门户](http://manage.windowsazure.cn)。
2. 单击“新建”->“数据服务”->“恢复服务”->“备份保管库”，然后选择“快速创建”。如果有多个订阅与你的组织帐户相关联，请选择要与备份保管库关联的正确订阅。在每个 Azure 订阅中，你可以使用多个备份保管库来组织受保护的数据。
3. 在“名称”中，输入一个友好名称以标识此保管库。这必须是每个订阅的唯一名称。
4. 在“区域”中，为保管库选择地理区域。请注意，保管库必须与你要保护的虚拟机位于同一区域中。如果你的虚拟机位于不同的区域中，请在每个区域中创建一个保管库。无需指定存储帐户即可存储备份数据 — 备份保管库和 Azure 备份服务将会自动处理这种情况。
5. 在“订阅”中，输入要将备份保管库用于的 Azure 订阅。
6. 单击“创建保管库”。
    ![创建备份保管库](./media/backup-azure-dpm-introduction/backup_vaultcreate.png)

    创建备份保管库可能需要一段时间。可以在门户底部监视状态通知。
![创建保管库 toast 通知](./media/backup-azure-dpm-introduction/creating-vault.png)

    将出现一条消息来确认保管库已成功创建，并且将在“恢复服务”页上将保管库列出为“活动”保管库。

    ![备份保管库列表](./media/backup-azure-dpm-introduction/backup_vaultslist.png)

7. 单击备份保管库转到“快速启动”页，其中会显示有关备份 DPM 服务器的说明。
    ![“仪表板”页中的虚拟机备份说明](./media/backup-azure-dpm-introduction/vmbackup-instructions.png)

    > [AZURE.NOTE]确保在创建保管库后立即选择适当的存储冗余选项。阅读有关[在备份保管库中设置存储冗余选项](/documentation/articles/backup-azure-backup-create-vault/#azure-backup---storage-redundancy-options)的更多内容。

### 下载保管库凭据
1. 单击“恢复服务”，然后单击备份保管库。在“快速启动”页上，单击“下载保管库凭据”，以下载凭据文件并将其保存到一个安全位置。你不能编辑凭据，因此无需打开该位置。出于安全原因，该文件中的密钥将在 48 小时后过期。

2. 将该文件复制到你要注册到 Azure 备份保管库中的 DPM 服务器可轻松访问的安全位置。在安装 Azure 备份代理时，将需要选择该文件。

### 安装 Azure 备份代理并注册服务器
你将下载代理安装文件，并在包含你要备份的数据的每个 DPM 服务器上运行它。代理存储在 **Azure 下载中心**，其安装过程各不相同。运行安装程序时，将安装代理，并将 DPM 服务器注册到保管库。请注意：

- 你需要在 DPM 服务器上拥有管理权限才能安装代理。
- 若要在多个 DPM 服务器上安装，可将安装程序文件放置在一个共享网络资源上，或使用组策略或管理产品（如 System Center Configuration Manager）来安装此代理。
- 在安装后不需要重新启动 DPM 服务器。

#### 安装备份代理并注册服务器

1. 在 Azure 备份保管库的“快速启动”页的“下载 Azure 备份代理”中，选择“适用于 Windows Server、System Center Data Protection Manager 或 Windows 客户端”。将该应用程序下载到要运行它的 DPM 服务器。
2. 运行安装程序文件 **MARSAgentInstaller.exe**。接受服务条款，然后选择要安装所有缺少的必备软件。
3. 在“安装设置”页上选择“安装文件夹”和“缓存位置”。

    默认安装文件夹为 <system drive>:\Program Files\Azure Backup Agent。在缓存位置中，安装过程将在 **Azure Backup Agent** 文件夹中创建一个名为 **Scratch** 的文件夹。缓存位置必须具有至少 2.5 千兆字节 (GB)（或者需要备份到 Azure 的数据大小的 10%）的可用空间。只有本地系统管理员和 Administrators 组的成员才有权访问缓存目录，以便阻止拒绝服务攻击。

4. 在“代理配置”页上为用于连接到 Azure 的代理设置自定义代理设置。如果你未配置任何设置，则将使用 DPM 服务器上的默认 Internet 访问设置。请注意，如果你使用的是需要进行身份验证的代理服务器，则应在此页上输入详细信息。
5. 在“Microsoft Update 选择加入”页上，我们建议你启用更新。如果已为服务器启用自动更新，请跳过此步骤。请注意，Microsoft 更新设置适用于所有 Microsoft 产品更新，并不是 Azure 备份代理专用的。
6. 将显示“安装”页。安装过程将检查必需的软件是否已安装，并完成安装。安装完成后，你将会收到告知已成功安装 Azure 备份代理的消息。此时，你可以选择检查更新。我们建议你允许进行更新检查。
7. 单击“继续执行注册”以在保管库中注册服务器。
8. 在“保管库标识”页中选择在 Azure 备份保管库中生成的保管库注册文件。
9. 在“加密设置”页中指定通行短语详细信息，或者自动生成通行短语。
10. 单击“生成通行短语”，然后单击“复制到剪贴板”。你将收到一条消息，指出你的通行短语已复制到剪贴板。此时最好是打开记事本，粘贴剪贴板中的通行短语并保存文件，同时打印该文件并将锁定。单击“注册”以便在备份保管库中注册 DPM 服务器。

    > [AZURE.TIP]
11. 单击“注册”。

    注册完成后，DPM 控制台将显示 Azure 备份的可用性。

    Azure 备份将始终使用你指定的密码（字母数字字符串）或自动生成的密码加密源数据。
    >[AZURE.NOTE]请注意，Azure 备份从不保留密码，如果你丢失了密码，将无法还原或恢复数据。我们强烈建议你将密钥保存在外部位置。

当你指定了密码并单击“完成”时，代理将需要几秒钟时间才能将生产服务器注册到备份保管库。向保管库注册完成后，将立即显示摘要“服务器注册”页。

## 要求和限制

- DPM 可作为物理服务器或安装在 System Center 2012 SP1 或 System Center 2012 R2 上的 Hyper-V 虚拟机运行。它也可以作为 System Center 2012 R2（至少包含 DPM 2012 R2 Update Rollup 3）上运行的 Azure 虚拟机，或 System Center 2012 R2（至少包含 Update Rollup 5）上运行的 VMWare 中的 Windows 虚拟机运行。
- 如果你要运行 System Center 2012 SP1 中的 DPM，则应安装 System Center Data Protection Manager SP1 的 Update Rollup 2。只有满足此要求，你才能安装 Azure 备份代理。
- DPM 服务器上应已安装 Windows PowerShell 和 .NET Framework 4.5。
- DPM 可将大多数工作负载备份到 Azure 备份。有关所支持内容的完整列表，请参阅下面的 Azure 备份支持项。
- 使用“复制到磁带”选项无法恢复存储在 Azure 备份中的数据。
- 你需要一个启用了 Azure 备份功能的 Azure 帐户。如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。阅读 [Azure 备份定价](https://azure.microsoft.com/pricing/details/backup/)的相关信息。
- 若要使用 Azure 备份，应在要备份的服务器上安装 Azure 备份代理。服务器必须至少为缓存位置提供 2.5 GB 的本地可用存储空间，但建议为缓存位置提供 15 GB 的本地可用存储空间。
- 数据将存储在 Azure 保管库存储中。你可以备份到 Azure 备份保管库的数据量没有限制，但数据源（例如虚拟机或数据库）的大小不应超过 1.65 TB。

支持将以下文件类型备份到 Azure：

- 加密（仅限完整备份）
- 压缩（支持增量备份）
- 稀疏（支持增量备份）
- 压缩和稀疏（视为稀疏）

以下内容不受支持：

- 不支持区分大小写的文件系统上的服务器。
- 硬链接（跳过）
- 重分析点（跳过）
- 加密和压缩（跳过）
- 加密和稀疏（跳过）
- 压缩流
- 稀疏流

>[AZURE.NOTE]从 System Center 2012 DPM SP1 开始，你可以使用 Microsoft Azure 备份将 DPM 保护的工作负载备份到 Azure。

<!---HONumber=64-->