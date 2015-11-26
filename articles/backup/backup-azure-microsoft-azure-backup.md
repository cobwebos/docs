<properties
  pageTitle="准备将工作负荷备份到 Microsoft Azure | Microsoft Azure"
  description="本文概述在 Microsoft Azure 备份中备份应用程序工作负荷的功能"
  services="backup"
  documentationCenter=""
  authors="SamirMehta"
  manager="shreeshd"
  editor=""/>

<tags
  ms.service="backup"

  ms.date="10/07/2015"
  wacn.date=""/>

# 准备将工作负荷备份到 Microsoft Azure

> [AZURE.SELECTOR]
- [SCDPM](backup-azure-dpm-introduction.md)
- [Azure 备份](backup-azure-microsoft-azure-backup.md)

本文介绍如何准备环境来备份工作负荷，以便将数据备份到 Azure。使用 Microsoft Azure 备份服务器，可以将应用程序工作负荷（例如 Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange 和 Windows 客户端）备份到以下位置以进行保护：

- **磁盘** (D2D)，为第 1 层工作负荷提供较高的 RTO
- **Azure** (D2D2C)，可长期保留

此外，你可以从单个本地用户界面管理各种受保护实体（服务器和客户端）的保护。

>[AZURE.NOTE] Microsoft Azure 备份服务器继承了 Data Protection Manager (DPM) 的工作负荷备份功能。本文提供了其中一些功能的 DPM 文档链接。不过，Microsoft Azure 备份服务器不能在磁带上提供保护，也没有与 System Center 集成。

Microsoft Azure 备份服务器可部署为：

- 独立的物理服务器。
- Hyper-V 虚拟机 - 可以将 Microsoft Azure 备份服务器当作托管在本地 Hyper-V 主机服务器上的虚拟机运行，以备份本地数据。有关此环境的注意事项列表，请参阅[在本地 Hyper-V 服务器上将 DPM 安装为虚拟机](https://technet.microsoft.com/library/dn440941.aspx)。
- VMWare 中的 Windows 虚拟机 - 可以部署 Microsoft Azure 备份服务器来保护 VMWare 中 Windows 虚拟机上运行的 Microsoft 工作负荷。在这种方案中，Microsoft Azure 备份服务器可部署为独立的物理服务器、Hyper-V 虚拟机，或 VMWare 中的 Windows 虚拟机。
- Azure 虚拟机 - 可以将 Microsoft Azure 备份服务器当作 Azure 中的虚拟机运行，以备份作为 Azure 虚拟机运行的云工作负荷。有关这种部署的信息，请参阅[将 DPM 安装为 Azure 虚拟机](https://technet.microsoft.com/library/hh758176.aspx)。

此外，

- Microsoft Azure 备份可以安装在 Windows Server 2008 R2 SP1、2012 和 2012 R2 上。
- Microsoft Azure 备份服务器不能安装在已安装 SCDPM 或 SCDPM RA 代理的计算机上。
- Microsoft Azure 备份服务器不能安装在已安装 Microsoft Azure 备份代理并已将其注册到 Azure 备份保管库的计算机上。



选择要安装 Microsoft Azure 备份的服务器后，你需要：

- 确保符合 Microsoft Azure 备份服务器的先决条件
- 创建备份保管库
- 下载保管库凭据，并且
- 下载 Microsoft Azure 备份服务器安装文件。

下面详细解释了每个步骤。

## 开始之前

目前，Microsoft Azure 备份服务器仅支持英语区域设置。所有提供 Azure 的地区都提供 Microsoft Azure 恢复服务，但 Microsoft Azure Government 数据中心和位于中国的 Microsoft Azure 除外。

若要顺利完成安装，请在安装 Microsoft Azure 备份服务器之前，确保符合以下先决条件。

- 服务器应已建立 Internet 连接。服务器应该能够访问 Microsoft Azure。
- Microsoft Azure 备份的服务器安装要求与 DPM 相同。有关详细信息，请参阅这些[硬件要求](https://technet.microsoft.com/library/dn726764.aspx)。
- Microsoft Azure 备份服务器必须已加入域。
- Microsoft Azure 备份服务器上必须已安装 .Net 3.5、.Net 4.0、.Net 3.5 SP1 功能。有关更多信息，请参阅[有关如何启用 .Net Framework 的详细信息](https://technet.microsoft.com/library/dn482071.aspx)。
- Microsoft Azure 备份服务器上应已安装 Windows Management Framework 4.0。可以在[此处](http://www.microsoft.com/download/details.aspx?id=40855)下载。
- 对于用作 Microsoft Azure 备份服务器数据存储专用空间的磁盘，建议的存储池大小是受保护数据的 1.5 倍。有关详细信息，请参阅 [此主题](https://technet.microsoft.com/library/hh758176.aspx#DPM 服务器) 的“磁盘与存储”部分。

通过执行以下操作，准备 Microsoft Azure 备份服务器以备份数据：

1. **创建备份保管库** — 在 Azure 备份控制台中创建一个保管库。
2. **下载保管库凭据** — 在 Azure 备份中，将你创建的管理证书上载到保管库。
3. **下载 Microsoft Azure 备份服务器** — 可以从备份保管库的“快速启动”页面下载适用于应用程序工作负荷的 Microsoft Azure 备份服务器。

[AZURE.INCLUDE [backup-create-vault](../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../includes/backup-download-credentials.md)]

## 下载 Microsoft Azure 备份服务器
类似于保管库凭据，你可以从备份保管库的**“快速启动”页面**下载适用于应用程序工作负荷的 Microsoft Azure 备份。

1. 单击“用于应用程序工作负荷(磁盘到磁盘再到云)”。

    ![Microsoft Azure 备份欢迎屏幕](./media/backup-azure-microsoft-azure-backup/dpm-venus1.png)

2. 单击“下载”。

    ![下载中心 1](./media/backup-azure-microsoft-azure-backup/downloadcenter1.png)

3. 选择所有文件，然后单击“下一步”。
    ![下载中心 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    >[AZURE.NOTE] 由于所有文件的下载大小合计超过了 3G，在 10Mbps 下载链路上可能需要 60 分钟才能完成下载。

4. 请将所有文件放在同一个文件夹中。

5. 下载 Microsoft Azure 备份下载页面中的所有文件。

## 安装 Microsoft Azure 备份服务器
在启动安装程序之前，请确保符合上一部分中提到的先决条件。

1. 下载所有文件之后，单击“MicrosoftAzureBackupInstaller.exe”。此时将显示“Microsoft Azure 备份安装向导”。

    ![Microsoft Azure 备份安装程序](./media/backup-azure-microsoft-azure-backup/installer-click.png)

2. 提供此服务器上用于解压缩文件的位置，然后单击“下一步”。

    ![Microsoft Azure 备份安装向导](./media/backup-azure-microsoft-azure-backup/extract-to.png)

3. 单击“解压缩”开始解压缩安装文件。

    ![Microsoft Azure 备份安装向导](./media/backup-azure-microsoft-azure-backup/ready-to-extract.png)

4. 选中启动 setup.exe 的对应复选框以开始安装 Microsoft Azure 备份服务器，然后单击“完成”。

    ![Microsoft Azure 备份安装向导](./media/backup-azure-microsoft-azure-backup/launch-setup.png)

5. 单击“Microsoft Azure 备份”以启动安装向导。

    ![Microsoft Azure 备份安装向导](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)

6. 在“欢迎”屏幕中单击“下一步”。

    ![Microsoft Azure 备份欢迎屏幕](./media/backup-azure-microsoft-azure-backup/welcome-screen.png)

7. 单击“检查”，以确定是否符合 Microsoft Azure 备份服务器的硬件和软件先决条件。

    ![Microsoft Azure 备份必备组件 1](./media/backup-azure-microsoft-azure-backup/prereq-screen1.png)

8. 如果完全符合所有先决条件，将有一条消息指出计算机符合要求。单击**“下一步”**。

    ![Microsoft Azure 备份必备组件 2](./media/backup-azure-microsoft-azure-backup/prereq-screen2.png)

9. Microsoft Azure 备份服务器需要 SQL Server Standard。请选择现有的 2014 SQL Server Standard 本地实例，或让向导安装 SQL Server Standard。单击“检查并安装”，确保在服务器上安装所需的 SQL 必备组件。

    ![Microsoft Azure 备份 SQL 1](./media/backup-azure-microsoft-azure-backup/sql-setup1.png)

    如果发生故障并且系统建议重新启动计算机，请按说明操作，然后单击“再次检查”。

    ![SiS 筛选器失败](./media/backup-azure-microsoft-azure-backup/sis-filter.png)

10. 成功安装必备组件后，单击“下一步”。

    ![Microsoft Azure 备份必备组件 2](./media/backup-azure-microsoft-azure-backup/sql-setup3.png)

11. 提供 Microsoft Azure 备份服务器文件的安装位置，然后单击“下一步”。

    ![Microsoft Azure 备份必备组件 2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    >[AZURE.NOTE] 备份到 Azure 需要有暂存位置。请确保暂存位置的空间至少为要备份到云的数据的 5%。在磁盘保护方面，安装完成之后需要配置独立的磁盘。有关存储池的详细信息，请参阅[配置存储池和磁盘存储](https://technet.microsoft.com/library/hh758075.aspx)。

12. 为受限的本地用户帐户提供强密码，然后单击“下一步”。

    ![Microsoft Azure 备份必备组件 2](./media/backup-azure-microsoft-azure-backup/security-screen.png)

    >[AZURE.NOTE] 设置强密码对系统的安全十分重要。强密码的长度至少为六个字符，不包含整个或部分用户帐户名，且至少包含以下四种字符中的三种：大写字符、小写字符、10 进制数字和符号（例如 !、@、#）。

13. 选择是否要使用 *Microsoft Update* 来检查更新，然后单击“下一步”。

    >[AZURE.NOTE] 我们建议让 Windows Update 重定向到 Microsoft Update，此网站为 Windows 和 Microsoft Azure 备份服务器等其他产品提供了安全更新与重要更新。

    ![Microsoft Azure 备份必备组件 2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

14. 复查“设置摘要”，然后单击“安装”。

    ![Microsoft Azure 备份必备组件 2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)

    安装将会分阶段进行。在第一个阶段，代理将安装在服务器上，你需要使用连同此产品一起下载的有效 Azure 备份保管库凭据来注册服务器。然后，向导将检查是否可直接连接到 Internet。如果可以连接到 Internet，则你可以继续安装，否则需要提供代理详细信息以连接到 Internet。

15. 单击“下一步”，启动 Microsoft Azure 恢复服务代理的安装阶段。

    ![Microsoft Azure 备份必备组件 2](./media/backup-azure-microsoft-azure-backup/proxy.png)

    向导将检查必备组件，并安装缺少的所有必备组件。

16. 单击“安装”。

    ![Microsoft Azure 备份必备组件 2](./media/backup-azure-microsoft-azure-backup/mars-prereq.png)

17. 代理安装完成后，请单击“下一步”，向 Azure 备份保管库注册此服务器。

    ![Microsoft Azure 备份必备组件 2](./media/backup-azure-microsoft-azure-backup/mars-successful.png)

18. 提供用于注册服务器的 Azure 备份保管库凭据，然后单击“下一步”。

    ![Microsoft Azure 备份必备组件 2](./media/backup-azure-microsoft-azure-backup/vault-reg-screen.png)

19. 提供通行短语来加密/解密发送到 Azure 的数据，并提供用于存储此通行短语的位置。你可以自动生成通行短语，或提供自己的通行短语（最少包含 16 个字符）。单击**“下一步”**。

    ![Microsoft Azure 备份必备组件 2](./media/backup-azure-microsoft-azure-backup/encrypt1.png)

    >[AZURE.NOTE] 为保持机密性，Microsoft Azure 备份服务器不会将此通行短语随数据一起发送到 Azure。你需要将此通行短语存储在安全位置，因为在从 Azure 还原数据时需要用到它。

    成功完成 Microsoft Azure 备份服务器注册后，安装程序将继续安装和配置 SQL Server Standard 2014（如果已选择）。

    ![Microsoft Azure 备份必备组件 2](./media/backup-azure-microsoft-azure-backup/sql-install-start-screen.png)

    安装完 SQL 后，将安装 Microsoft Azure 备份服务器组件。

    ![Microsoft Azure 备份必备组件 2](./media/backup-azure-microsoft-azure-backup/venus-installation-screen.png)

20. 完成产品安装并创建桌面图标后，请双击该图标以启动该产品。

    ![图标](./media/backup-azure-microsoft-azure-backup/icons.png)

## 将磁盘添加到存储池

若要将工作负荷备份到磁盘和 Azure，首先需要将磁盘添加到存储池。有关添加磁盘的详细信息，请参阅[配置存储池和磁盘存储](https://technet.microsoft.com/library/hh758075.aspx)。

配置存储池后，可以将应用程序工作负荷备份到磁盘和 Azure。

## 故障排除

如果 Microsoft Azure 备份服务器在安装阶段（或备份或还原时）失败并出现错误，请参阅此[错误代码文档](https://support.microsoft.com/kb/3041338)以获取详细信息。
此外，还可以参考 [Azure 备份相关的常见问题](backup-azure-backup-faq.md)

## 常见问题

### 安装和部署

问：用于磁盘备份的 Microsoft Azure 备份服务器磁盘/卷是否支持 NTFS 压缩？
<br>答：连接到 Microsoft Azure 备份服务器的磁盘/卷**不**支持 NTFS。

问：部署后是否可以将 Microsoft Azure 备份服务器转移到新域？
<br>答：**不**支持在部署后将 Microsoft Azure 备份服务器转到新的域。

问：如果 Microsoft Azure 备份服务器安装在域控制器上，它可以保护哪些资源？
<br>答：如果 Microsoft Azure 备份服务器与域控制器一起安装在同一服务器上，只能保护本地数据源。若要让 Microsoft Azure 备份服务器保护其他服务器上的工作负荷，请不要将它与域控制器安装在同一台计算机上。

问：Microsoft Azure 备份服务器是否可以使用远程 SQL Server 实例？
<br>答：Microsoft Azure 备份服务器只能使用本地 SQL Server 实例。

### Microsoft Azure 备份服务器存储池

问：是否可以在 Microsoft Azure 备份服务器存储池中提供虚拟磁盘 (VHD/HDx)？
<br>答：可以。

问：是否可以对 Microsoft Azure 备份服务器存储池使用重复数据删除功能？
<br>答：可以，Microsoft Azure 备份服务器存储池支持重复数据删除功能。这篇 [DPM 博客文章](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx)详述了 Microsoft Azure 备份服务器的用户体验。

问：是否可以对 Microsoft Azure 备份服务器存储池使用 USB 或可移动驱动器？
<br>答：不能。

### Microsoft Azure 备份服务器到 Azure 服务的连接

问：Microsoft Azure 备份服务器到 Azure 服务的连接和 Azure 订阅状态是否会影响备份和还原？
<br>答：Microsoft Azure 备份服务器需要连接到 Azure 服务才能成功运行。同时，Azure 订阅必须处于正常运行状态。

下表描述了 Microsoft Azure 备份服务器在不同连接状态和 Azure 订阅状态下的功能。

| 连接状态 | Azure 订阅 | 备份到 Azure| 备份到磁盘 | 从 Azure 还原 | 从磁盘还原 |
| -------- | ------- | --------------------- | ------------------- | --------------------------- | ----------------------- |
| 已连接 | 活动 | 允许 | 允许 | 允许 | 允许 |
| 已连接 | 已过期 | 已停止 | 已停止 | 允许 | 允许 |
| 一般 | 已取消预配 | 已停止 | 已停止 | 已停止且已删除 Azure 恢复点 | 已停止 |
| 连接断开超过 15 天 | 活动 | 已停止 | 已停止 | 允许 | 允许 |
| 连接断开超过 15 天 | 已过期 | 已停止 | 已停止 | 允许 | 允许 |
| 连接断开超过 15 天 | 已取消预配 | 已停止 | 已停止 | 已停止且已删除 Azure 恢复点 | 已停止 |

问：假设订阅状态持续为“活动”，当 Microsoft Azure 备份服务器连接恢复时会发生什么情况？
<br>答：Microsoft Azure 备份服务器到 Azure 服务的连接恢复正常后，如果订阅状态为“活动”，则允许执行 Microsoft Azure 备份服务器的所有操作。你可以正常备份到这两个磁盘和 Azure。

问：如果订阅状态从“已过期”状态恢复为“活动”状态，将发生什么情况？
<br>答：假设 Microsoft Azure 备份服务器到 Azure 服务的连接正常，在 Microsoft Azure 备份服务器订阅状态恢复为“活动”后，将允许你执行 Microsoft Azure 备份服务器的所有操作。现在，你可以正常备份到这两个磁盘和 Azure。

问：如果订阅状态从“已取消预配”状态恢复为“活动”状态，将发生什么情况？
<br>答：假设 Microsoft Azure 备份服务器到 Azure 服务的连接正常，在 Microsoft Azure 备份服务器订阅状态恢复为“活动”后，将允许你执行 Microsoft Azure 备份服务器的所有操作。但是，Azure 恢复点将被删除且无法还原。如果磁盘备份在有效的保留范围内，则你可以还原磁盘恢复点。

问：我需要创建哪些例外，才能允许 Microsoft Azure 备份服务器连接到公共 Azure 服务？
<br>答：需要在防火墙配置文件中允许以下域地址 - www.msftncsi.com, \*.Microsoft.com, \*.WindowsAzure.com, \*.microsoftonline.com, \*.windows.net。

问：如何验证 Microsoft Azure 备份服务器是否能够连接到 Azure 服务？
<br>答：在 Microsoft Azure 备份服务器控制台中运行以下 PowerShell cmdlet：

```
Get-DPMCloudConnection
Output:
TRUE - Connected to Azure service
FALSE - Not connected to Azure service
```

问：为什么我的订阅状态不是“活动”？ 如何更改为“活动”？
<br>答：请登录[订阅门户](https://account.windowsazure.com/Subscriptions)并管理你的订阅。

### 计费

问：Microsoft Azure 备份服务器的计费模式是什么？
<br>答：将通过受保护实例模型对用户计费。有关详细信息，请参阅[价格](http://azure.microsoft.com/pricing/details/backup/)页上的常见问题。

问：如果我只保护磁盘上的数据，将使用怎样的计费模式？
<br>答：计费模式与受保护实例模型相同。由于是在本地存储上保护这项数据，仅基于磁盘的备份不收取 Azure 存储费用。在此情况下，只对客户收取受保护实例的费用。有关实例的定义和每个实例收费金额的详细信息，请参阅[价格](http://azure.microsoft.com/pricing/details/backup/)页上的常见问题。

问：每个受保护实例收费多少？
<br>答：请参阅[价格](http://azure.microsoft.com/pricing/details/backup/)页。

问：哪里可以找到强调不同应用程序工作负荷及其受保护实例费用的示例？
<br>答：请参阅[价格](http://azure.microsoft.com/pricing/details/backup/)页上的“示例”部分。

问：Microsoft Azure 备份服务器如何对磁盘和云上保护的数据源计费？
<br>答：无论数据是备份到磁盘还是云，Microsoft Azure 备份服务器都根据受保护实例来收费。受保护实例大小根据数据源的前端大小计算。对于 Azure 存储中的备份数据，还会收取 Azure 存储费用。

## 后续步骤

请参阅这些文章，以深入了解如何使用 Microsoft Azure 备份服务器来保护工作负荷。

- [SQL Server 备份](backup-azure-backup-sql.md)
- [SharePoint Server 备份](backup-azure-backup-sharepoint.md)
- [备用服务器备份](backup-azure-alternate-dpm-server.md)

<!---HONumber=82-->