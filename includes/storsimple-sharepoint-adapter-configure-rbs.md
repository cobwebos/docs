<!--author=SharS last changed: 1/14/2016 -->

> [!NOTE]
> 更改适用于 StorSimple Adapter for SharePoint RBS 配置时，必须使用属于“域管理员”组的用户帐户登录。 此外，必须从浏览器（在管理中心所在的同一台主机上运行）访问配置页。
> 
> 

#### <a name="to-configure-rbs"></a>配置 RBS
1. 打开“SharePoint 管理中心”页，导航到“系统设置”。 
2. 在“Azure StorSimple”部分中，单击“配置 StorSimple Adapter”。
   
    ![配置 StorSimple Adapter](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. 在“配置 StorSimple Adapter”页上：
   
   1. 确保选中“启用编辑路径”复选框。
   2. 在文本框中，键入 BLOB 存储的通用命名约定 (UNC) 路径。
      
      > [!NOTE]
      > 必须在 StorSimple 设备上配置的 iSCSI 卷中托管 BLOB 存储卷。

   3. 单击想要为其配置远程存储的每个内容数据库下面的“启用”按钮。
      
      > [!NOTE]
      > BLOB 存储必须由所有 Web 前端 (WFE) 服务器共享和访问，为 SharePoint 服务器场配置的用户帐户必须有权访问该共享。
      
      ![启用 RBS 提供程序](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      启用或禁用 RBS 时，还会看到以下消息。
      
      ![配置 StorSimple Adapter - 启用和禁用](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. 单击“更新”按钮以应用配置。 单击“更新”按钮时，所有 WFE 服务器上的 RBS 配置状态将会更新，整个服务器场将启用 RBS。 此时将显示以下消息。
      
      ![适配器配置消息](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > 如果要配置 RBS 的 SharePoint 场包含大量的数据库（超过 200 个），SharePoint 管理中心网页可能会超时。 如果发生这种情况，请刷新页面。 这不会影响配置过程。

4. 验证配置：
   
   1. 登录到 SharePoint 管理中心网站，然后浏览到“配置 StorSimple Adapter”页。
   2. 检查配置详细信息，确保与输入的设置一致。 
5. 验证 RBS 是否正常工作：
   
   1. 将一个文档上载到 SharePoint。 
   2. 浏览到配置的 UNC 路径。 确保 RBS 目录结构已创建，并且其中包含上载的对象。
6. （可选）可以使用 SharePoint 附带的 Microsoft RBS `Migrate()` PowerShell cmdlet，将现有 BLOB 内容迁移到 StorSimple 设备。 有关详细信息，请参阅 [Migrate content into or out of RBS in SharePoint 2013]（在 SharePoint 2013 中将内容移入或移出 RBS）[6] 或 [Migrate content into or out of RBS (SharePoint Foundation 2010)]（将内容移入或移出 RBS (SharePoint Foundation 2010)）[7]。
7. （可选）进行测试安装时，可以验证是否已将 BLOB 移出内容数据库，如下所示： 
   
   1. 启动 SQL Management Studio。
   2. 运行 ListBlobsInDB_2010.sql 或 ListBlobsInDB_2013.sql 查询，如下所示。
      
      ```
      **ListBlobsInDB_2013.sql**
      
        USE WSS_Content
        GO
      
        SELECT DocStreams.DocId,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               DocStreams.RbsId,
               TimeLastModified
      
        FROM DocStreams
      
             INNER JOIN AllDocs ON DocStreams.DocId = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      
      **ListBlobsInDB_2010.sql**
      
        USE WSS_Content
        GO
      
        SELECT AllDocStreams.Id,
      
               LeafName AS Name,
               Content,
               AllDocs.Size AS OrigSizeOfContent,
               LEN(CAST(Content AS VARBINARY(MAX))) AS SizeOfContentInDB,
               RbsId,
               TimeLastModified
        FROM AllDocStreams
      
             INNER JOIN AllDocs ON AllDocStreams.Id = AllDocs.Id
        ORDER BY TimeLastModified DESC
        GO
      ```
      
      如果 RBS 配置正确，任何已上载并通过 RBS 成功外部化的对象的 SizeOfContentInDB 列中会显示 NULL 值。
8. （可选）配置 RBS 并将所有 BLOB 内容转移到 Azure StorSimple 设备后，可将内容数据库转移到设备。 如果选择转移内容数据库，我们建议在设备上将内容数据库存储配置为主卷。 然后，根据制定的 SQL 迁移最佳实践，将内容数据库转移到 Azure StorSimple 设备。 
   
   > [!NOTE]
   > 只有 StorSimple 8000 系列支持将内容数据库转移到设备（5000 或 7000 系列不支持）。
   
   如果将 BLOB 和内容数据库存储在 StorSimple 设备上的单独卷中，我们建议在同一个卷容器中配置这些卷。 这可以确保统一备份这些内容。
   
   > [!WARNING]
   > 如果尚未启用 RBS，则不建议将内容数据库转移到 StorSimple 设备。 这种配置未经测试。
   
9. 转到下一步：[配置垃圾回收](#configure-garbage-collection)。

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
