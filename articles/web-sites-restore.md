<properties linkid="web-sites-restore" urlDisplayName="还原 Microsoft Azure 网站" pageTitle="还原 Microsoft Azure Website" metaKeywords="Azure Websites, Restore, restoring" description="了解如何从备份中还原 Azure 网站。" metaCanonical="" services="web-sites" documentationCenter="" title="Restore a Microsoft Azure Website" authors="timamm"  solutions="" writer="timamm" manager="paulettm" editor="mollybos"  />

# 还原 Microsoft Azure 网站

本文介绍如何通过使用 Azure 网站备份功能来还原您先前备份的网站。有关更多信息，请参阅 [Microsoft Azure 网站备份](/zh-cn/documentation/articles/web-sites-backup/)。

Azure 网站还原功能可根据需求将网站还原到以前的状态，或基于原有网站备份之一来创建新网站。创建与最新版本并行运行的新网站对于 A/B 测试会很有用。

"还原"功能在 Azure 网站门户中的"备份"选项卡上，只能用于"标准"模式。

## 本文内容
- [从以前制作的备份中还原 Azure 网站](#PreviousBackup)
- [直接从存储帐户还原 Azure 网站](#StorageAccount)
- [选择您 Website 还原设置和启动还原操作](#RestoreSettings)
- [查看操作日志](#OperationLogs)


<a name="PreviousBackup"></a>
## 从以前制作的备份中还原 Azure 网站

1. 在**备份**选项卡上，单击门户页底部的命令栏中的**立即还原**。此时将显示**立即还原**对话框。
	
	![Choose backup source][ChooseBackupSource]
	
2. 在**选择备份源**下，选择**以前的备份 Website使用派生自** 的自定义类映射的。
3. 选择要还原的备份的日期，然后单击向右箭头以继续。
4. 遵循本文的后文中[选择你的网站还原设置](#RestoreSettings)部分中的步骤 。

<a name="StorageAccount"></a>
## 直接从存储帐户还原 Azure 网站

1. 在**备份**选项卡上，单击门户页底部的命令栏中的**立即还原**。此时将显示**立即还原**对话框。
	
	![Choose backup source][ChooseBackupSource]
	
2. 在**选择备份源"**下，选择**存储帐户文件**。在此可以直接指定存储帐户文件的 URL，或单击文件夹图标以导航到 blob 存储并指定备份文件。此示例选择文件夹图标。
	
	![Storage Account File][StorageAccountFile]
	
3. 单击文件夹图标，以打开**浏览云存储**对话框。
	
	![Browse Cloud Storage][BrowseCloudStorage]
	

4. 展开要使用的存储帐户的名称，然后选择 **Websitebackups**，它包含你的备份。
5. 选择包含要还原的备份的 zip 文件，然后单击**打开**。
6. 存储帐户文件已选好，并显示在存储帐户框中。单击向右箭头以继续。
	
	![Storage Account File Selected][StorageAccountFileSelected]
	
7. 继续学习随后这一部分，[选择网站还原设置并开始还原操作](#RestoreSettings)。

<a name="RestoreSettings"></a>
## 选择网站还原设置并开始还原操作
1. 在**选择您的网站还原设置**，**还原到**下，选择**当前网站**或**新的网站实例**。
	
	![Choose your Website restore settings][ChooseRestoreSettings]
	
	如果您选择**当前网站**，所选的备份将覆盖现有的网站（破坏性还原）。所选备份创建时间之后对网站所做的所有更改都将被永久删除，而且还原操作是不可撤消的。还原操作期间，将暂时无法使用当前网站，你将会收到相关警告信息。
	
	如果您选择**新的网站实例**，将以您指定的名称在同一区域中创建新网站。（默认情况下，新的名称是 **restored-***old WebsiteName*。） 
	
	你还原的网站所包含的内容和配置将与在门户中为原网站提供的内容和配置相同。它还将包括你在下一步中选择要包括的任何数据库。
2. 如果要连同网站一起还原数据库，请在**包含的数据库**下通过使用**还原到**下的下拉列表选择要将数据库还原到其中的数据库服务器的名称。你还可以选择创建要还原到其中的新数据库服务器，或者选择**不还原**，这是默认设置，这样就不会还原数据库。 
	
	选择了服务器名称后，在**数据库名称**框中为还原指定目标数据库的名称。
	
	如果你的还原包括一个或多个数据库，可选择**自动调整连接字符串**以将备份中存储的连接字符串更新为指向新数据库，或指向数据库服务器，视需要而定。还原完成后，应验证与数据库相关的所有功能都能正常使用。
	
	![Choose database server host][ChooseDBServer]
	
	> [AZURE.NOTE] 不能还原与同一 SQL Server 同名的 SQL 数据库。必须选择其他数据库名称或其他要将该数据库还原到其中的 SQL Server 主机。 
	
	> [AZURE.NOTE] 您可以将同名的 MySQL 数据库还原到同一服务器，但请注意，这将清除出存储在 MySQL 数据库中的现有内容。	
	
3. 如果选择还原现有数据库，需要提供用户名和密码。如果选择还原到新数据库，需要提供新数据库名称：
	
	![Restore to a new SQL database][RestoreToNewSQLDB]
	
	单击向右箭头以继续。	
4. 如果选择创建新数据库，需要在下一个对话框中提供该数据库的凭据和其他初始配置信息。这里的示例显示的是一个新的 SQL 数据库。（用于新 MySQL 数据库的选项会有所不同。）
	
	![New SQL database settings][NewSQLDBConfig]
	
5. 单击复选标记以开始还原操作。操作完成时，在门户网站的列表中将能够看到新网站实例（如果那是你选择的还原选项）。
	
	![Restored Contoso Website][RestoredContosoWebsite]

<a name="OperationLogs"></a>
## 查看操作日志
	
1. 要查看有关网站还原操作成功或失败的详细信息，请转到网站的"仪表板"选项卡。在**速览**部分的**管理服务**下，单击**操作日志**。
	
	![Dashboard - Operation Logs Link][DashboardOperationLogsLink]
	
2. 您会跳转到"管理服务"门户**操作日志**页面，并可在里面的操作日志列表中看到还原操作日志：
	
	![Management Services Operation Logs page][ManagementServicesOperationLogsList]
	
3. 若要查看有关该操作的详细信息，请在列表中选择该操作，然后单击命令栏上的**详细信息**按钮。
	
	![Details Button][DetailsButton]
	
	进行此操作时，**操作详细信息**窗口会打开，显示日志文件的可复制内容：
	
	![Operation Details][OperationDetails]
	

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebsite]: ./media/web-sites-restore/09RestoredContosoWebsite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png
