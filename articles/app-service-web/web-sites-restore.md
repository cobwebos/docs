---
title: "在 Azure 中还原应用"
description: "了解如何从备份还原应用。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin;aelnably
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: 61396e8a3521c25eaf49c66a4948e520377d342a
ms.contentlocale: zh-cn
ms.lasthandoff: 06/09/2017


---
# <a name="restore-an-app-in-azure"></a>在 Azure 中还原应用
本文将演示如何在 [Azure 应用服务](../app-service/app-service-value-prop-what-is.md) 中还原已事先备份的应用（请参阅[在 Azure 中备份应用](web-sites-backup.md)）。 可以根据需要将应用及其链接的数据库还原到以前的状态，或者基于原始应用的备份之一创建新的应用。 Azure 应用服务支持用于备份和还原的以下数据库：
- [SQL 数据库](https://azure.microsoft.com/en-us/services/sql-database/)
- [用于 MySQL 的 Azure 数据库（预览版）](https://azure.microsoft.com/en-us/services/mysql)
- [用于 PostgreSQL 的 Azure 数据库（预览版）](https://azure.microsoft.com/en-us/services/postgres)
- [ClearDB MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SuccessBricksInc.ClearDBMySQLDatabase?tab=Overview)
- [MySQL 应用内产品](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

从备份还原适用于在**标准**和**高级**层中运行的应用。 有关向上缩放应用的信息，请参阅[在 Azure 中向上缩放应用](web-sites-scale.md)。 相比于**标准**层，**高级**层允许执行更多的每日备份量。

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>从现有备份还原应用
1. 在 Azure 门户中应用的“设置”边栏选项卡上，单击“备份”以显示“备份”边栏选项卡。 然后，单击“还原”。
   
    ![选择“立即还原”][ChooseRestoreNow]
2. 在“还原”边栏选项卡中，首先选择备份源。
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    “应用备份”选项显示当前应用的所有现有备份，使你能够轻松地选择一个。
    “存储”选项使你能够从任何现有 Azure 存储帐户和订阅中的容器中选择任何备份 ZIP 文件。
    如果正在尝试还原其他应用的备份，请使用“存储”选项。
3. 然后，在“还原目标”中指定应用还原的目标。
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > 如果选择“覆盖”，将清除并覆盖当前应用中所有的现有数据。 在单击“确定”之前，请确保该操作正是想要执行的操作。
   > 
   > 
   
    可选择“现有应用”将应用备份还原到同一资源组中的其他应用。 使用此选项之前，应已使用应用备份中定义的镜像数据库配置在资源组中创建了其他应用。 还可以创建“新”应用来将内容还原到其中。

4. 单击 **“确定”**。

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>从存储帐户中下载或删除备份
1. 在 Azure 门户的主“浏览”边栏选项卡中，选择“存储帐户”。 将显示现有存储帐户的列表。
2. 选择包含要下载或删除的备份的存储帐户。将显示该存储帐户的边栏选项卡。
3. 在存储帐户边栏选项卡中，选择所需的容器
   
    ![查看容器][ViewContainers]
4. 选择要下载或删除的备份文件。
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. 单击“下载”或“删除”，具体取决于要执行的操作。  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>监视还原操作
若要查看有关应用还原操作成功与否的详细信息，请导航到 Azure 门户中的“活动日志”边栏选项卡。  
 

向下滚动以查找所需的还原操作，然后单击以选中。

“详细信息”边栏选项卡将显示与还原操作相关的可用信息。

## <a name="next-steps"></a>后续步骤
可使用 REST API 备份和还原应用服务应用（请参阅[使用 REST 备份和还原应用服务应用](websites-csm-backup.md)）。


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png

