<!--author=SharS last changed: 1/14/2016 -->

> [!NOTE]
> 更改 StorSimple Adapter for SharePoint RBS 配置时，你必须使用属于 Domain Admins 组的用户帐户登录。 此外，你必须在管理中心的同一主机上运行的浏览器访问配置页。
> 
> 

#### <a name="to-configure-rbs"></a>若要配置 RBS
1. 打开 SharePoint 管理中心页中，并浏览到**系统设置**。 
2. 在**Azure StorSimple**部分中，单击**配置 StorSimple 适配器**。
   
    ![配置 StorSimple 适配器](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS1-include.png) 
3. 上**配置 StorSimple 适配器**页：
   
   1. 请确保**启用编辑路径**复选框处于选中状态。
   2. 在文本框中，键入 BLOB 存储区的通用命名约定 (UNC) 路径。
      
      > [!NOTE]
      > BLOB 存储卷必须承载在 StorSimple 设备上配置的 iSCSI 卷上。

   3. 单击**启用**下面每个你想要配置用于远程存储的内容数据库的按钮。
      
      > [!NOTE]
      > BLOB 存储区必须共享并访问所有 web 前端 (WFE) 服务器，并且配置为 SharePoint 服务器场的用户帐户必须具有对共享的访问。
      
      ![启用 RBS 提供程序](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS2-include.png)
      
      如果启用或禁用 RBS 时，你还将看到以下消息。
      
      ![配置 StorSimple 适配器启用禁用](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_ConfigureStorSimpleAdapterEnableDisableMessage-include.png)

   4. 单击**更新**按钮以应用配置。 当你单击**更新**按钮，将所有 WFE 服务器上更新 RBS 配置状态，并且整个场将会启用 RBS。 下面显示的消息。
      
      ![适配器配置消息](./media/storsimple-sharepoint-adapter-configure-rbs/HCS_SSASP_ConfigRBS3-include.png)
      
      > [!NOTE]
      > 如果要为包含大量的数据库 （200 个以上） 的 SharePoint 场配置 RBS，SharePoint 管理中心 web 页可能会超时。 如果发生这种情况，请刷新页面。 这不会影响配置过程。

4. 验证配置：
   
   1. 登录到 SharePoint 管理中心网站，并浏览到**配置 StorSimple 适配器**页。
   2. 检查配置详细信息，以确保它们匹配您输入的设置。 
5. 验证 RBS 工作正常：
   
   1. 将文档上载到 SharePoint。 
   2. 浏览到你配置的 UNC 路径。 请确保已创建 RBS 目录结构和它包含已上载的对象。
6. （可选）你可以使用 Microsoft RBS `Migrate()` SharePoint 将现有 BLOB 内容迁移到 StorSimple 设备附带的 PowerShell cmdlet。 有关详细信息，请参阅[入 （出） 在 SharePoint 2013 中的 RBS 迁移内容][ 6]或[将内容迁移出 RBS (SharePoint Foundation 2010)][7]。
7. （可选）在测试安装时，你可以验证，已将 Blob 移出内容数据库，如下所示： 
   
   1. 启动 SQL Management Studio。
   2. 如下所示运行 ListBlobsInDB_2010.sql 或 ListBlobsInDB_2013.sql 查询。
      
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
      
      如果 RBS 配置正确，NULL 值应出现在任何已上载并通过 RBS 成功外部化的对象的 SizeOfContentInDB 列中。
8. （可选）配置 RBS 并将所有 BLOB 内容都转移到 StorSimple 设备后，你可以将内容数据库都转移到设备。 如果你选择转移内容数据库，我们建议将内容数据库存储配置为主卷设备上。 然后，使用建立 SQL Server 最佳做法，旨在将内容数据库迁移到 StorSimple 设备。 
   
   > [!NOTE]
   > StorSimple 8000 系列 （它不支持 5000 或 7000 系列） 仅支持将内容数据库转移到设备。
   
   如果你存储在 StorSimple 设备上的单独卷中的 Blob 和内容数据库，我们建议你在相同的卷容器中配置它们。 这可确保，它们将一起备份。
   
   > [!WARNING]
   > 如果你尚未启用 RBS，不建议将内容数据库转移到 StorSimple 设备。 这是未经测试的配置。
   
9. 转到下一步：[配置垃圾回收](#configure-garbage-collection)。

[6]: https://technet.microsoft.com/library/ff628254(v=office.15).aspx
[7]: https://technet.microsoft.com/library/ff628255(v=office.14).aspx
