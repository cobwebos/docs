<!--author=SharS last changed: 9/17/15-->

在此过程中，你将：

1. [准备运行的维护程序可执行文件](#to-prepare-to-run-the-maintainer)。
2. [为立即删除孤立的 Blob 准备内容数据库和回收站](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs)。
3. [运行 Maintainer.exe](#to-run-the-maintainer)。
4. [恢复内容数据库和回收站设置](#to-revert-the-content-database-and-recycle-bin-settings)。

#### <a name="to-prepare-to-run-the-maintainer"></a>若要准备运行 Maintainer
1. 在 Web 前端服务器上，打开以管理员身份的 SharePoint 2013 Management Shell。
2. 导航到的文件夹*启动驱动器*: files\microsoft SQL 远程 Blob 存储 10.50\Maintainer\.
3. 重命名**Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config**到**web.config**。
4. 使用`aspnet_regiis -pdf connectionStrings`web.config 文件进行解密。
5. 在解密的 web.config 文件中，在`connectionStrings`节点，添加你的 SQL server 实例和内容数据库名称的连接字符串。 请参见以下示例：
   
    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=&quot;Remote Blob Storage Maintainer for WSS_Content&quot;" providerName="System.Data.SqlClient" />`
6. 使用`aspnet_regiis –pef connectionStrings`若要重新加密 web.config 文件。 
7. 将 web.config 重命名为 Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config。 

#### <a name="to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs"></a>若要准备内容数据库和回收站可以立即删除孤立的 Blob
1. 在 SQL 服务器上，在 SQL Management Studio 中运行以下更新查询为目标的内容数据库： 
   
       `use WSS_Content`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`
   
       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`
2. 在 web 前端服务器上，在**管理中心**，编辑**Web 应用程序常规设置**为所需的内容数据库来临时禁用回收站。 此操作还将清空回收站的任何相关站点集合。 若要执行此操作，请单击**管理中心** -> **应用程序管理** -> **Web 应用程序 （管理 web 应用程序）** -> **SharePoint-80** -> **常规应用程序设置**。 设置**回收站状态**到**OFF**。
   
    ![Web 应用程序常规设置](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### <a name="to-run-the-maintainer"></a>若要运行该维护组件
* 在 web 前端服务器上，在 SharePoint 2013 Management Shell，运行该维护组件，如下所示：
  
      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`
  
  > [!NOTE]
  > 仅`GarbageCollection`此时 for StorSimple 支持操作。 此外请注意，对 Microsoft.Data.SqlRemoteBlobs.Maintainer.exe 发出的参数区分大小写。 
  > 
  > 

#### <a name="to-revert-the-content-database-and-recycle-bin-settings"></a>若要还原的内容数据库和回收站设置
1. 在 SQL 服务器上，在 SQL Management Studio 中运行以下更新查询为目标的内容数据库：
   
      `use WSS_Content`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`
   
      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`
2. 在 web 前端服务器上，在**管理中心**，编辑**Web 应用程序常规设置**为所需的内容数据库，若要重新启用回收站。 若要执行此操作，请单击**管理中心** -> **应用程序管理** -> **Web 应用程序 （管理 web 应用程序）** -> **SharePoint-80** -> **常规应用程序设置**。 将回收 Bin 状态设置为**ON**。

