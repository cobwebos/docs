---
title: "在 Azure 中备份应用"
description: "了解如何在 Azure 应用服务中创建应用备份。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.openlocfilehash: 435370a8758d439a5fcce2e04efd11b4aaaf0357
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2017
---
# <a name="back-up-your-app-in-azure"></a>在 Azure 中备份应用
利用 [Azure App Service](app-service-web-overview.md) 中的备份和还原功能，可以轻松地手动或按计划创建应用备份。 通过覆盖现有应用或还原为另一应用可将应用还原为先前状态的快照。 

有关从备份中还原应用的信息，请参阅[在 Azure 中还原应用](web-sites-restore.md)。

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>备份的内容
应用服务可将以下信息备份到你配置应用使用的 Azure 存储帐户和容器。 

* 应用配置
* 文件内容
* 连接到应用的数据库

备份功能支持以下数据库解决方案： 
   - [SQL 数据库](https://azure.microsoft.com/en-us/services/sql-database/)
   - [用于 MySQL 的 Azure 数据库（预览版）](https://azure.microsoft.com/en-us/services/mysql)
   - [用于 PostgreSQL 的 Azure 数据库（预览版）](https://azure.microsoft.com/en-us/services/postgres)
   - [MySQL 应用内产品](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  每个备份都是应用的完整脱机副本，而不是增量更新。
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>要求和限制
* 备份和还原功能要求应用服务计划处于标准层或高级层。 有关缩放应用服务计划以使用更高层的详细信息，请参阅[增加 Azure 中的应用](web-sites-scale.md)。  
  与标准层相比，高级层每日允许更多备份量。
* 在与要备份的应用相同的订阅中，需要有一个 Azure 存储帐户和容器。 有关 Azure 存储帐户的详细信息，请参阅本文结尾处的[链接](#moreaboutstorage)。
* 最多可备份 10 GB 的应用和数据库内容。 如果备份大小超过此限制，会出错。

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>创建手动备份
1. 在 [Azure 门户](https://portal.azure.com)中，导航到应用的页面，然后选择“备份”。 将显示“备份”页。
   
    ![“备份”页面][ChooseBackupsPage]
   
   > [!NOTE]
   > 若显示以下消息，请单击该消息升级 应用服务计划，才能继续备份。
   > 有关详细信息，请参阅[增加 Azure 中的应用](web-sites-scale.md)。  
   > ![选择存储帐户](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. 在“备份”页中，单击“配置”
![单击“配置”](./media/web-sites-backup/ClickConfigure1.png)
3. 在“备份配置”页中，单击“存储: 未配置”来配置存储帐户。
   
    ![选择存储帐户][ChooseStorageAccount]
4. 选择“存储帐户”和“容器”来选择备份目标。 该存储帐户必须与要备份的应用属于同一订阅。 也可在各自的页面中新建存储帐户或容器。 完成后，单击“选择”。
   
    ![选择存储帐户](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. 在仍处于打开状态的“备份配置”页中，可配置“备份数据库”，然后选择备份要包含的数据库（SQL 数据库或 MySQL），然后单击“确定”。  
   
    ![选择存储帐户](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > 若要使数据库显示在此列表中，其连接字符串必须位于应用中“应用程序设置”页的“连接字符串”部分中。
   > 
   > 
6. 在“备份配置”页中，单击“保存”。    
7. 在“备份”页中，单击“备份”。
   
    ![BackUpNow 按钮][BackUpNow]
   
    备份过程中会显示进度消息。

配置存储帐户和容器后，可随时启动手动备份。  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>配置自动执行的备份
1. 在“备份配置”页中，将“计划备份”设置为“开”。 
   
    ![选择存储帐户](./media/web-sites-backup/05ScheduleBackup1.png)
2. 此时将显示备份计划选项，将“计划备份”设置为“开”，然后按需配置备份计划，并单击“确定”。
   
    ![启用自动化的备份][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>配置部分备份
有时你不想备份应用中的所有内容。 以下是一些示例：

* [设置每周备份](web-sites-backup.md#configure-automated-backups)应用，其中包含永远不会更改的静态内容，例如旧的博客文章或图像。
* 应用的内容超过 10 GB（这是一次可以备份的最大量）。
* 不想备份日志文件。

使用部分备份可以精确选择需要备份的文件。

### <a name="exclude-files-from-your-backup"></a>从备份中排除文件
假定应用中包含的日志文件和静态映像已经过备份且不会发生更改。 这种情况下，可排除这些文件夹和文件，以免其存储在将来的备份中。 若要从备份中排除文件和文件夹，请在应用的 `D:\home\site\wwwroot` 文件夹中创建一个 `_backup.filter` 文件。 指定要在此文件中排除的文件和文件夹列表。 

访问文件的一种简单方式是使用 Kudu。 单击 Web 应用的“高级工具”->“转到”设置，访问 Kudu。

![使用门户的 Kudu][kudu-portal]

确定要从备份中排除的文件夹。  例如，筛选出突出显示的文件夹和文件。

![映像文件夹][ImagesFolder]

创建名为 `_backup.filter` 的文件并将上述列表放在文件中，但删除 `D:\home`。 每行列出一个目录或文件。 文件的内容应为：
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

使用 [ftp](app-service-deploy-ftp.md) 或任何其他方法，将 `_backup.filter` 文件上传到站点的 `D:\home\site\wwwroot\` 目录。 如果需要，可使用 Kudu `DebugConsole` 直接创建此文件，并在其中插入内容。

采用通常使用的相同方式运行备份，即[手动](#create-a-manual-backup)或[自动](#configure-automated-backups)。 现在，`_backup.filter` 中指定的任何文件和文件夹已从计划启动或手动启动的将来备份中排除。 

> [!NOTE]
> 采用与[还原定期备份](web-sites-restore.md)相同的方式，还原站点的部分备份。 还原过程会执行正确的操作。
> 
> 还原完整备份后，站点上的所有内容都被替换为备份中的任何内容。 如果文件在站点上但不在备份中，则会将其删除。 但是，还原部分备份时，位于其中一个方块列表目录或任何方块列表文件中的任何内容都保持不变。
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>如何存储备份
对应用进行了一次或多次备份后，可在存储帐户的“容器”页中看到备份以及应用。 在存储帐户中，每个备份都由一个 `.zip` 文件和一个 `.xml` 文件组成，前者包含备份数据，后者包含 `.zip` 文件内容的清单。 如果想要在无需实际执行应用还原的情况下访问备份，则可以解压缩并浏览这些文件。

应用的数据库备份存储在 .zip 文件的根目录中。 对于 SQL 数据库，这是 BACPAC 文件（无文件扩展名），并且可以导入。 若要基于 BACPAC 导出创建 SQL 数据库，请参阅[导入 BACPAC 文件以创建新的用户数据库](http://technet.microsoft.com/library/hh710052.aspx)。

> [!WARNING]
> 改动 **websitebackups** 容器中的任何文件都导致备份无效，进而无法还原。
> 
> 

## <a name="automate-with-scripts"></a>使用脚本自动化

可以在 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/overview) 中使用脚本自动备份管理。

相关示例如下所示：

- [Azure CLI 示例](app-service-cli-samples.md)
- [Azure PowerShell 示例](app-service-powershell-samples.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>后续步骤
有关从备份中还原应用的信息，请参阅[在 Azure 中还原应用](web-sites-restore.md)。 


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage1.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount-1.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow1.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn1.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
[kudu-portal]:./media/web-sites-backup/kudu-portal.PNG

