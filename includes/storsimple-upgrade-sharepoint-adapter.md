<!--author=SharS last changed: 9/17/15-->

### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>将 SharePoint 2010 升级到 SharePoint 2013，然后安装 StorSomple Adapter for SharePoint
> [!IMPORTANT]
> 在完成升级并重新启用 RBS 功能之前，以前已移到外部存储通过 RBS 的任何文件将不可用。 若要限制用户的影响，会执行任何升级或重新安装在计划的维护时段。
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>若要将 SharePoint 2010 升级到 SharePoint 2013，然后安装适配器
1. 在 SharePoint 2010 场中，记下外部化的 Blob 和为其启用 RBS 的内容数据库的 BLOB 存储路径。 
2. 安装和配置新的 SharePoint 2013 场。 
3. 将数据库、 应用程序和网站集从 SharePoint 2010 场移动到新的 SharePoint 2013 场。 有关说明，请转到[到 SharePoint 2013 的升级过程概述](https://technet.microsoft.com/library/cc262483.aspx)。
4. 安装 StorSimple Adapter for SharePoint 上新的场。 转到[安装 StorSimple Adapter for SharePoint](#install-the-storsimple-adapter-for-sharepoint)的过程。
5. 使用你在步骤 1 中记下的信息，为组相同的内容数据库启用 RBS，并提供在 SharePoint 2010 安装中使用的相同 BLOB 存储路径。 转到[配置 RBS](#configure-rbs)的过程。 完成此步骤后，以前外部化的文件应可从新场访问。 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>升级 StorSimple Adapter for SharePoint
> [!IMPORTANT]
> 您应该计划此升级，在计划的维护时段发生原因如下：
> 
> * 以前外部化的内容将不能重新安装适配器之前。
> * 将内容数据库中存储您卸载以前版本的 StorSimple Adapter for SharePoint 之后, 但尚未安装最新版本之前, 上载到网站的任何内容。 你将需要安装新适配器之后，将该内容移动到 StorSimple 设备。 你可以使用 Microsoft` RBS Migrate()` SharePoint 迁移内容附带的 PowerShell cmdlet。 有关详细信息，请参阅[将内容迁移出 RBS](https://technet.microsoft.com/library/ff628255.aspx)。 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>若要升级 StorSimple Adapter for SharePoint
1. 卸载以前版本的 StorSimple Adapter for SharePoint。
   
   > [!NOTE]
   > 这会自动将内容数据库上禁用 RBS。 但是，现有 Blob 将保留在 StorSimple 设备上。 由于 RBS 被禁用，Blob 尚未迁移回到内容数据库，这些 Blob 的任何请求将失败。 
   > 
   > 
2. 安装新的 StorSimple Adapter for SharePoint。 新适配器将自动识别以前启用或禁用 rbs 的内容数据库，并且将使用以前的设置。

