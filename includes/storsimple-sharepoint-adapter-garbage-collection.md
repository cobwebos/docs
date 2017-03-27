<!--author=SharS last changed: 9/17/15-->

在此过程中，需要执行以下操作：

1. [准备运行 Maintainer 可执行文件](#to-prepare-to-run-the-maintainer)。
2. [为立即删除孤立 BLOB 准备好内容数据库和回收站。](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs)
3. [运行 Maintainer.exe](#to-run-the-maintainer)。
4. [还原内容数据库和回收站设置](#to-revert-the-content-database-and-recycle-bin-settings)。

#### <a name="to-prepare-to-run-the-maintainer"></a>准备运行 Maintainer
1. 在 Web 前端服务器上，以管理员身份打开 SharePoint 2013 命令行管理程序。
2. 导航到文件夹*引导盘*:\Program Files\Microsoft SQL Remote Blob Storage 10.50\Maintainer\.
3. 将 **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** 重命名为 **web.config**。
4. 使用 `aspnet_regiis -pdf connectionStrings` 解密 web.config 文件。
5. 在解密的 web.config 文件中的 `connectionStrings` 节点下，为 SQL Server 实例和内容数据库名称添加连接字符串。 请参阅以下示例。
   
    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. 使用 `aspnet_regiis –pef connectionStrings` 重新加密 web.config 文件。 
7. 将 web.config 重命名为 Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config。 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>为立即删除孤立 BLOB 准备好内容数据库和回收站
1. 在 SQL Server 上的 SQL Management Studio 中，针对目标内容数据库运行以下更新查询： 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. 在 Web 前端服务器上的“管理中心”下，编辑所需内容数据库的“Web 应用程序常规设置”以暂时禁用回收站。 此操作同时会清空所有相关站点集合的回收站。 为此，请单击“管理中心” -> “应用程序管理” -> “Web 应用程序(管理 Web 应用程序)” -> “SharePoint - 80” -> “常规应用程序设置”。 将“回收站状态”设置为“关闭”。
   
    ![Web 应用程序常规设置](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>运行 Maintainer
* 在 Web 前端服务器上的 SharePoint 2013 命令行管理程序中运行 Maintainer，如下所示：
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > StorSimple 目前仅支持 `GarbageCollection` 操作。 另请注意，为 Microsoft.Data.SqlRemoteBlobs.Maintainer.exe 发出的参数区分大小写。 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>还原内容数据库和回收站设置
1. 在 SQL Server 上的 SQL Management Studio 中，针对目标内容数据库运行以下更新查询：
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. 在 Web 前端服务器上的“管理中心”中，编辑所需内容数据库的“Web 应用程序常规设置”以重新启用回收站。 为此，请单击“管理中心” -> “应用程序管理” -> “Web 应用程序(管理 Web 应用程序)” -> “SharePoint - 80” -> “常规应用程序设置”。 将“回收站状态”设置为“打开”。



<!--HONumber=Nov16_HO3-->


