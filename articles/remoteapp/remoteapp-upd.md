---
title: "Azure RemoteApp 如何保存用户数据和设置？ | Microsoft Docs"
description: "了解 Azure RemoteApp 如何使用用户配置文件磁盘保存用户数据。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: e125af62-5c1a-4186-a238-52f537f7bb34
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: dd281ead7d2c06af98ea0baea31d897b5be0d9d7
ms.openlocfilehash: 04d8031a4c5f06d56afc1d08d4a4fa5fb50f8a25


---
# <a name="how-does-azure-remoteapp-save-user-data-and-settings"></a>Azure RemoteApp 如何保存用户数据和设置？
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

Azure RemoteApp 跨设备和会话保存用户标识和自定义项。 此用户数据存储在一个每用户每集合磁盘中，该磁盘称为用户配置文件磁盘 (UPD)。 该磁盘跟随用户，无论用户在什么位置登录，都可以确保用户获得一致的体验。

用户配置文件磁盘对于用户是完全透明的，用户可以照常将文档保存到其“文档”文件夹（看起来就像是在本地驱动器的文件夹上）并更改其应用设置。 同时，从任何设备连接到 Azure RemoteApp 时，都会保留所有个人设置。 用户所看到的全部就是其处于同一位置的数据。

每个 UPD 有 50 GB 的永久存储空间，并且既包含用户数据，也包含应用程序设置。 

请继续阅读关于用户配置文件数据的详细信息。

> [!NOTE]
> 是否需要禁用 UPD？ 你现在可以禁用 UPD。查看 Pavithra 的博客文章[在 Azure RemoteApp 中禁用用户配置文件磁盘](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/)，了解详细信息。
> 
> 

## <a name="how-can-an-admin-get-to-the-data"></a>管理员如何可以访问数据？
如果你需要访问你的某个用户的数据（需要进行灾难恢复，或者如果用户离开公司），请与 Azure 支持部门联系并提供集合和用户标识的订阅信息。 Azure RemoteApp 团队将为你提供一个指向 VHD 的 URL。 下载该 VHD 并检索所需的任何文档或文件。 注意该 VHD 的大小为 50 GB，因此，下载将需要一些时间。

## <a name="is-the-data-backed-up"></a>数据是否已备份？
是的，我们按地理位置保存了用户数据的备份。 该数据是只读的，如果主数据中心停止服务，那么可以像访问常规数据那样访问该数据（与 Azure RemoteApp 联系以了解如何访问常规数据）。 数据会实时复制到备份位置，我们不会保留不同版本的副本。 因此，在发生数据损坏时，我们无法将其还原到某个先前的已知良好版本，但是，如果主数据中心停止服务，你将能够从另一个位置获取用户数据。

## <a name="how-do-users-see-the-upd-on-the-server-side"></a>用户如何在服务器端看到 UPD？
每个用户都将在服务器上有其自己的目录，该目录映射到其 UPD：c:\Users\username。

## <a name="whats-the-best-way-to-use-outlook-and-upd"></a>使用 Outlook 和 UPD 的最佳方式是什么？
Azure RemoteApp 会保存各个会话之间的 Outlook 状态（邮箱、PST）。 要启用此功能，我们需要将 PST 存储在用户配置文件数据中 (c:\users\<username>)。 这是数据的默认位置，因此，只要你不更改该位置，数据将在会话之间一直保持。

我们还建议你在 Outlook 中使用“已缓存”模式并使用“服务器/联机”模式进行搜索。

请查看[此文章](remoteapp-outlook.md)了解关于使用 Outlook 和 Azure RemoteApp 的详细信息。

## <a name="what-about-redirection"></a>重定向会如何呢？
你可以通过设置[重定向](remoteapp-redirection.md)将 Azure RemoteApp 配置为让用户访问本地设备。 然后，本地设备将能够访问 UPD 上的数据。

## <a name="can-i-use-my-upd-as-a-network-share"></a>我是否可以将我的 UPD 用作网络共享？
否。 不能将 UPD 用作网络共享。 仅当用户主动连接到 Azure RemoteApp 时，UPD 才对用户可用。

## <a name="if-i-delete-a-user-from-a-collection-is-their-upd-deleted"></a>如果我将用户从某个集合中删除，是否也会删除其 UPD？
不会删除，当你删除用户时，我们不会自动删除 UPD，在你删除集合之前，我们会一直存储该数据。 在你删除集合之后 90 天，我们会删除所有 UPD。 

如果你需要从某个集合删除 UPD，请与 Azure RemoteApp 联系，我们可以从服务器端删除 UPD。

## <a name="can-i-access-my-users-upds-either-current-or-deleted-users"></a>我是否可以访问我的用户的 UPD（无论是当前用户还是已删除的用户）？
可以，如果你与 [Azure RemoteApp](mailto:remoteappforum@microsoft.com) 联系，我们可以为你设置一个 URL 来访问该数据。 你大约有 10 个小时可以从 UPD 下载数据或文件，之后访问就会过期。

## <a name="are-upds-available-offline"></a>UPD 是否可脱机使用？
目前，在上面所述的 10 小时访问窗口期之外的时间，我们不提供对 UPD 的脱机访问。 这意味着我们当前无法为你提供足够长的访问时间来完成更复杂的任务，比如在 UPD 上运行防病毒软件或访问数据以进行审核。

## <a name="do-registry-key-settings-persist"></a>注册表项设置是否会保持？
是的，写入 HKEY_Current_User 的任何内容都是 UPD 的组成部分。

## <a name="can-i-disable-upds-for-a-collection"></a>我是否可以针对某个集合禁用 UPD？
是的，你可以请求 Azure RemoteApp 针对某个订阅禁用 UPD，但不要自行这样做。 这意味着对于订阅中的所有集合，都将禁用 UPD。

你可能希望在遇到以下情形之一时禁用 UPD： 

* 你需要对用户数据的完整访问和控制权限（用于审核和审阅目的，比如金融机构）。
* 你在本地有一个第三方用户配置文件管理解决方案，并希望在已加入域的 Azure RemoteApp 部署中继续使用它们。 这将需要把配置文件代理加载到黄金映像中。 
* 你不需要任何本地数据存储，或者你将所有数据置于云或文件共享中并且想要使用 Azure RemoteApp 在本地控制数据的保存。

有关详细信息，请参阅[在 Azure RemoteApp 中禁用用户配置文件磁盘](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/)。

## <a name="can-i-restrict-users-from-saving-data-to-the-system-drive"></a>我是否可以限制用户将数据保存到系统驱动器中？
可以，但是你需要在创建集合之前在模板映像中进行设置。 使用以下步骤阻止对系统驱动器的访问︰

1. 在模板映像上运行 **gpedit.msc**。
2. 导航到“**用户配置 > 管理模板 > Windows 组件 > 资源管理器**”。
3. 选择以下选项：
   * **隐藏“我的电脑”中的这些指定的驱动器**
   * **防止从“我的电脑”访问驱动器**

## <a name="can-i-seed-upds-i-want-to-put-some-data-in-the-upd-thats-available-the-first-time-the-user-signs-in"></a>我是否可以设定 UPD 的种子？ 我希望将一些数据置于 UPD 中，使其在用户首次登录时可用。
可以，当你创建模板映像时，可以将信息添加到默认配置文件中。 然后，会将该信息添加到 UPD 中。

## <a name="can-i-change-the-size-of-the-upd-depending-on-how-much-data-i-want-to-store"></a>我是否可以根据我希望存储的数据量更改 UPD 的大小？
不可以，所有 UPD 的存储空间都是 50 GB。 如果你希望存储不同数量的数据，请尝试执行以下操作：

1. 针对该集合禁用 UPD。
2. 设置文件共享以便用户访问。
3. 通过使用某个启动脚本加载文件共享。 有关 Azure RemoteApp 中的启动脚本的详细信息，请参阅以下内容。
4. 指导用户将所有数据保存到文件共享中。

## <a name="how-do-i-run-a-startup-script-in-azure-remoteapp"></a>我应该如何在 Azure RemoteApp 中运行启动脚本？
如果你想要运行启动脚本，请先在你打算用于集合的模板映像中创建一个计划任务。 （请在运行 sysprep *之前*执行此操作。） 

![创建系统任务](./media/remoteapp-upd/upd1.png)

![创建在用户登录时运行的系统任务](./media/remoteapp-upd/upd2.png)

在“**常规**”选项卡上，请确保将“安全”下的“**用户帐户**”更改为“BUILTIN\Users”。

![将该用户帐户更改为某个组](./media/remoteapp-upd/upd4.png)

计划任务将使用用户的凭据启动你的启动脚本。 安排任务在每次用户登录时运行。

![将任务的触发器设置为“在登录时”](./media/remoteapp-upd/upd3.png)

你也可以使用[基于组策略的启动脚本](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx)。 

## <a name="what-about-placing-a-startup-script-in-the-start-menu-would-that-work"></a>将启动脚本放置到“开始”菜单中怎么样？ 这样是否可以工作？
换句话说，我是否可以创建一个运行某个配置窗口脚本的 .bat 文件并将其保存到 c:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp 文件夹中，然后让该脚本在用户启动 RemoteApp 会话时运行？

不可以，Azure RemoteApp 不支持这样做，Azure RemoteApp 使用 RDSH，而 RDSH 也不支持“开始”菜单中的启动脚本。

## <a name="can-i-use-mstscexe-the-remote-desktop-program-to-configure-logon-scripts"></a>我是否可以使用 mstsc.exe（远程桌面程序）来配置登录脚本？
不可以，Azure RemoteApp 不支持这样做。

## <a name="can-i-store-data-on-the-vm-locally"></a>我是否可以将数据存储在虚拟机本地？
不可以，除了存储在 UPD 中，存储在虚拟机上任意位置的数据都将丢失。 很有可能用户在下次登录 Azure RemoteApp 时得到的不是同一个虚拟机。 我们并不保持用户-虚拟机间的持久性，因此，用户将不会登录到同一虚拟机，所以数据将会丢失。 此外，在我们更新集合时，现有的虚拟机将替换一组新的虚拟机，这意味着存储在虚拟机本身上的任何数据都将丢失。 推荐将数据存储在 UPD 中，像 Azure 文件一样存储在共享存储空间中，存储在 VNET 内部的文件服务器中，或者使用像 DropBox 一样的云存储系统存储在云中。

## <a name="how-do-i-mount-an-azure-file-share-on-a-vm-using-powershell"></a>我如何可以使用 PowerShell 将某个 Azure 文件共享装载到一个虚拟机上？
你可以使用 Net-PSDrive cmdlet 装载驱动器，如下所示：

    New-PSDrive -Name <drive-name> -PSProvider FileSystem -Root \\<storage-account-name>.file.core.windows.net\<share-name> -Credential :<storage-account-name>


你还可以通过运行以下各项保存你的凭据：

    cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>


这样你可以跳过 New-PSDrive cmdlet 中的 -Credential 参数。




<!--HONumber=Jan17_HO2-->


