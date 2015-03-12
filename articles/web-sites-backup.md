<properties linkid="web-sites-backup" urlDisplayName="Azure  Websites Backups" pageTitle="Azure 网站备份" metaKeywords="Azure  Websites, Backups" description="了解如何创建 Azure 网站的  备份。" metaCanonical="" services="web-sites" documentationCenter="" title="Azure  Websites Backups" authors="timamm" solutions="" manager="paulettm" editor="mollybos" />

# Azure 网站备份

利用 Azure 网站备份和还原功能，你可以轻松地手动或自动创建网站备份。你可以将网站还原到以前的状态，或者基于原始网站的备份之一创建新网站。 


有关从备份中还原 Azure 网站的信息，请参阅[还原 Azure 网站](/zh-cn/documentation/articles/web-sites-restore/)。

## 本文内容

- [自动轻松备份（视频）](#video)
- [备份的内容](#whatsbackedup)
- [要求和限制](#requirements)
- [创建手动备份](#manualbackup)
- [配置自动化的备份](#automatedbackups)
- [如何存储备份](#aboutbackups)
- [说明](#notes)
- [后续步骤](#nextsteps)
	- [有关存储帐户的详细信息](#moreaboutstorage)

<a name="video"></a>
## 自动轻松备份（视频）

Eduardo Laureano 和 Scott Hanselman 在此视频中介绍了 Azure 网站备份。（持续时间：11:43）  

> [AZURE.VIDEO azure-websites-automatic-and-easy-backup]

<a name="whatsbackedup"></a>
## 备份的内容 
Azure 网站备份以下信息：

* 网站配置
* 网站文件内容
* 任何连接到你的网站的 SQL Server 或 MySQL 数据库（你可以选择备份中要包括哪些数据库）

这些信息会备份到你指定的 Azure 存储帐户。 

> [AZURE.NOTE] 每个备份都是你的网站的完整脱机副本，而不是增量更新。

<a name="requirements"></a>
## 要求和限制

* 备份和还原功能要求网站为"标准"级别。有关使用"标准"级别缩放网站的详细信息，请参阅[如何缩放网站](/zh-cn/documentation/articles/web-sites-scale/)。 

* 备份和还原功能要求 Azure 存储帐户与要备份的网站必须同属一个订阅。如果你还没有存储帐户，可以创建一个，方法是：单击 Azure 门户左窗格中的"存储"按钮（网格图标），然后选择底部命令栏中的"新建"。有关 Azure 存储帐户的详细信息，请参阅本文末尾的[链接](#moreaboutstorage) 。

<a name="manualbackup"></a>
## 创建手动备份

1. 在网站的 Azure 门户中，选择"备份"选项卡。
	
	![Backups page][ChooseBackupsPage]
	
2. 选择网站要备份到其中的存储帐户。该存储帐户必须与要备份的网站同属一个订阅。
	
	![Choose storage account][ChooseStorageAccount]
	
3. 在"包含的数据库"选项中，选择要备份的连接到你的网站的数据库 (SQL Server)。 
	
	![Choose databases to include][IncludedDatabases]

	> [AZURE.NOTE] 	若要使数据库显示在此列表中，其连接字符串必须位于门户中"配置"选项卡的"连接字符串"部分。
	
4. 在命令栏上，单击"立即备份"。
	
	![BackUpNow button][BackUpNow]
	
	在备份过程中你将看到进度消息：
	
	![Backup progress message][BackupProgress]
	
你可以随时进行手动备份。在预览版期间，每 24 小时内的手动备份不能超过 2 个（此限制可能会变更）。  

<a name="automatedbackups"></a>
## 配置自动化的备份

1. 在"备份"页面上，将"自动化的备份"设置为"开"。
	
	![Enable automated backups][SetAutomatedBackupOn]
	
2. 选择网站要备份到其中的存储帐户。该存储帐户必须与要备份的网站同属一个订阅。
	
	![Choose storage account][ChooseStorageAccount]
	
3. 在"频率"框中，指定你希望多久进行一次自动化的备份。（在预览版期间，天数是唯一可用的时间单位。）
	
	![Choose backup frequency][Frequency]
	
	天数必须介于 1 到 90 之间（含 1 和 90，从每天 1 次到每 90 天一次）。
	
4. 使用"开始日期"选项指定你希望自动化的备份开始的日期和时间。 
	
	![Choose start date][StartDate]
	
	时间以半小时为增量。
	
	![Choose start time][StartTime]
	
	> [AZURE.NOTE] Azure 存储备份时间时采用的是 UTC 格式，但显示时根据的是用于显示门户的计算机上的系统时间。
	
5. 在"包含的数据库"部分中，选择要备份的连接到你的网站的数据库<!-- or MySQL-->(SQL Server)。若要使数据库显示在此列表中，其连接字符串必须位于门户中"配置"选项卡的"连接字符串"部分。
	
	![Choose databases to include][IncludedDatabases]
	
	> [AZURE.NOTE] 如果你选择在备份中包含一个或多个数据库，并且指定了小于 7 天的频率，就会收到频繁备份会增加数据库成本的警告。
	
6. 在命令栏中，单击"保存"按钮以保存你的配置更改（或者，如果你决定不保存，请选择"放弃"）。
	
	![Save button][SaveIcon]

<a name="aboutbackups"></a>
## 如何存储备份

在完成了一个或多个备份后，在你的存储帐户的"容器"选项卡中可以看到这些备份。你的备份将在名为"websitebackups"的容器中。每个备份都由一个 .zip 文件和一个 .xml 文件组成，前者包含备份的数据，后者包含 .zip 文件内容的清单。 

.zip 和 .xml 备份文件名中包含网站的名称，后面跟有下划线和备份生成时间的时间戳。时间戳包含 YYYYMMDD（数字，无空格）格式的日期加上 UTC 格式的 24 小时制时间（例如，fabrikam_201402152300.zip）。如果你想在不实际执行网站还原的情况下访问备份，可将这些文件的内容解压缩，然后进行浏览。

以 zip 格式存储的 XML 文件在 *backupdescription* > *databases* > *databasebackupdescription* > *filename* 下指示数据库文件名。

数据库备份文件本身存储在 .zip 文件的根目录中。对于 SQL 数据库，这是 BACPAC 文件（无文件扩展名），并且可以导入。若要基于 BACPAC 导出创建新的 SQL 数据库，可以遵照文章[导入 BACPAC 文件以创建新的用户数据库](http://technet.microsoft.com/library/hh710052.aspx)中的步骤。

有关使用 Azure 管理门户还原 Azure 网站（包括数据库）的信息，请参阅[还原 Microsoft Azure 网站](/zh-cn/documentation/articles/web-sites-restore/)。

> [AZURE.NOTE] 改动"websitebackups"容器中的任何文件都会导致备份无效，进而无法还原。

<a name="notes"></a>
## 说明

* 一定要在网站的"配置"选项卡上为你的每个数据库正确设置连接字符串，这样"备份和还原"功能才会包括这些数据库。
* 在预览版期间，你负责管理保存到你的存储帐户的备份的内容。如果你从存储帐户删除一个备份，且未在其他位置制作副本，则无法在以后还原该备份。 
* 虽然你可以将多个网站备份到同一存储帐户，但是，为了易于维护，还是为每个网站都单独创建存储帐户比较好。
* 在预览版期间，备份和还原操作只能通过 Azure 管理门户完成。

<a name="nextsteps"></a>
## 后续步骤
有关从备份中还原 Azure 网站的信息，请参阅[还原 Azure 网站](/zh-cn/documentation/articles/web-sites-restore/)。

若要开始使用 Azure，请参阅 [Microsoft Azure 免费试用版](/zh-cn/pricing/1rmb-trial/)。


<a name="moreaboutstorage"></a>
### 有关存储帐户的详细信息

[什么是存储帐户？](/zh-cn/documentation/articles/storage-whatis-account/)

[如何：创建存储帐户](/zh-cn/documentation/articles/storage-create-storage-account/)

[如何监视存储帐户](/zh-cn/documentation/articles/storage-monitor-storage-account/)

[了解 Microsoft Azure 存储计费](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
