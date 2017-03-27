<!--author=SharS last changed: 9/17/15-->

### <a name="upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-storsomple-adapter-for-sharepoint"></a>将 SharePoint 2010 升级到 SharePoint 2013，然后安装 StorSomple Adapter for SharePoint
> [!IMPORTANT]
> 以前通过 RBS 转到外部存储的任何文件，必须等到升级完成并重新启用 RBS 功能之后才能使用。 为了限制用户受影响的程度，请在规划的维护期间执行任何升级或重新安装。
> 
> 

#### <a name="to-upgrade-sharepoint-2010-to-sharepoint-2013-and-then-install-the-adapter"></a>将 SharePoint 2010 升级为 SharePoint 2013，然后安装适配器
1. 在 SharePoint 2010 场中，记下外部化 BLOB 的 BLOB 存储路径及其中启用 RBS 的内容数据库。 
2. 安装和配置新的 SharePoint 2013 场。 
3. 将数据库、应用程序和站点集合从 SharePoint 2010 场移到新的 SharePoint 2013 场。 有关说明，请转到 [SharePoint 2013 升级过程概述](https://technet.microsoft.com/library/cc262483.aspx)。
4. 在新场中安装 StorSimple Adapter for SharePoint 转到[安装 StorSimple Adapter for SharePoint](#install-the-storsimple-adapter-for-sharepoint) 了解过程。
5. 使用在步骤 1 记下的信息，为同一组内容数据库启用 RBS 并提供 BLOB 存储路径，需与 SharePoint 2010 安装中使用的路径相同。 转到[配置 RBS](#configure-rbs) 了解过程。 完成此步骤之后，以前已外部化的文件必须能够从新场访问。 

### <a name="upgrade-the-storsimple-adapter-for-sharepoint"></a>升级 StorSimple Adapter for SharePoint
> [!IMPORTANT]
> 出于以下原因，应该将此升级安排在规划的维护时段：
> 
> * 在重新安装适配器之前，以前已外部化的内容将无法使用。
> * 在卸载旧版 StorSimple Adapter for SharePoint 之后、安装新版之前，任何上载到站点的内容都将存储在内容数据库中。 安装新适配器之后，需将该内容移到 StorSimple 设备。 可以使用 SharePoint 随附的 Microsoft` RBS Migrate()` PowerShell cmdlet 来迁移内容。 有关详细信息，请参阅[将内容移入或移出 RBS](https://technet.microsoft.com/library/ff628255.aspx)。 
> 
> 

#### <a name="to-upgrade-the-storsimple-adapter-for-sharepoint"></a>升级 StorSimple Adapter for SharePoint
1. 卸载旧版 StorSimple Adapter for SharePoint。
   
   > [!NOTE]
   > 这会自动禁用内容数据库上的 RBS。 但是，现有 BLOB 将保留在 StorSimple 设备上。 由于已禁用 RBS 且 BLOB 尚未迁移回到内容数据库，对这些 BLOB 的任何请求都会失败。 
   > 
   > 
2. 安装新的 StorSimple Adapter for SharePoint。 新适配器将自动识别以前已为 RBS 启用或禁用的内容数据库，并使用以前的设置。



<!--HONumber=Nov16_HO3-->


