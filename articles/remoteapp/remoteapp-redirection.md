---
title: "在 Azure RemoteApp 中使用重定向 | Microsoft Docs"
description: "了解如何在 RemoteApp 中配置和使用重定向"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 2c8c867f-4907-4f2e-9ccd-2eb82bb5b837
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: cab03e7765dc7116a7f974833871fae652e8363c
ms.lasthandoff: 03/31/2017


---
# <a name="using-redirection-in-azure-remoteapp"></a>在 Azure RemoteApp 中使用重定向
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

通过设备重定向，你的用户可以使用连接到其本地计算机、手机或平板电脑等设备与远程应用进行交互。 例如，如果你已通过 Azure RemoteApp 提供 Skype，那么你的用户需要在其 PC 上安装摄像头才能使用 Skype。 对于打印机、扬声器、显示器和各种 USB 连接的外设，都是如此。

RemoteApp 利用远程桌面协议 (RDP) 和 RemoteFX 来提供重定向。

## <a name="what-redirection-is-enabled-by-default"></a>默认情况下会启用哪种重定向？
使用 RemoteApp 时，默认情况下会启用以下重定向。 括号中的信息显示的是 RDP 设置。

* 在本地计算机上播放声音（**在此计算机上播放**）。 (audiomode:i:0)
* 从本地计算机捕获音频并发送到远程计算机（**从此计算机录制**）。 (audiocapturemode:i:1)
* 打印到本地打印机 (redirectprinters:i:1)
* COM 端口 (redirectcomports:i:1)
* 智能卡设备 (redirectsmartcards:i:1)
* 剪贴板（进行复制和粘贴的能力）(redirectclipboard:i:1)
* 清晰版字体平滑（允许字体 smoothing:i:1）
* 重定向所有受支持的即插即用设备。 (devicestoredirect:s:*)

## <a name="what-other-redirection-is-available"></a>有哪些其他可用的重定向？
默认情况下，会禁用两个重定向选项：

* 驱动器重定向（驱动器映射）︰本地计算机的驱动器成为远程会话中映射的驱动器。 在远程会话中工作时，通过驱动器重定向，你可以从本地驱动器保存或打开文件。
* USB 重定向︰你可以在远程会话中使用连接到本地计算机的 USB 设备。

## <a name="change-your-redirection-settings-in-remoteapp"></a>在 RemoteApp 中更改重定向设置
通过一起使用 Microsoft Azure PowerShell 与 SDK，你可以更改某个集合的设备重定向设置。 在安装了新的 PowerShell 和 SDK 之后，首先将其配置为管理你的订阅，如[如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs) 中所述。

然后使用类似下面的命令来设置自定义 RDP 属性︰

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

（请注意，*' n* 用作各个属性之间的分隔符。）

若要获取已配置的自定义 RDP 属性列表，请运行以下 cmdlet。 请注意，仅自定义属性会显示为输出结果，默认属性则不会显示为输出结果︰  

    Get-AzureRemoteAppCollection -CollectionName <collection name>

设置自定义属性时，每次必须指定所有自定义属性；否则，该设置将恢复为已禁用。   

### <a name="common-examples"></a>常见示例
使用下面的 cmdlet 来启用驱动器重定向︰  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*"

使用此 cmdlet 来启用 USB 和驱动器重定向︰

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

使用此 cmdlet 来禁用剪贴板共享︰  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0"

> [!IMPORTANT]
> 确保在测试更改之前完全注销集合中的所有用户（而不是仅仅断开其连接）。 若要确保完全注销用户，请转到 Azure 门户集合中的“**会话**”选项卡，然后注销已断开连接或已登录的所有用户。 有时可能需要几秒钟，在会话内的资源管理器中才会显示本地驱动器。
> 
> 

## <a name="change-usb-redirection-settings-on-your-windows-client"></a>更改 Windows 客户端上的 USB 重定向设置
如果你想要在连接到 RemoteApp 的计算机上使用 USB 重定向，需要执行以下两个操作。 1 - 你的管理员需要通过使用 Azure PowerShell 在集合级别上启用 USB 重定向。 2 - 在想要使用 USB 重定向的每个设备上，你需要启用一个允许 USB 重定向的组策略。 每个想要使用 USB 重定向的用户都将需要完成此步骤。

> [!NOTE]
> 仅在 Windows 计算机上支持 Azure RemoteApp 的 USB 重定向。
> 
> 

### <a name="enable-usb-redirection-for-the-remoteapp-collection"></a>启用 RemoteApp 集合的 USB 重定向
使用以下 cmdlet 在集合级别上启用 USB 重定向︰

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### <a name="enable-usb-redirection-for-the-client-computer"></a>启用客户端计算机的 USB 重定向
若要在你的计算机上配置 USB 重定向设置，请执行以下操作：

1. 打开本地组策略编辑器 (GPEDIT.MSC)。 （在命令提示符下运行 gpedit.msc。）
2. 打开**计算机配置\策略\管理模板\Windows 组件\远程桌面服务\远程桌面连接客户端\RemoteFX USB 设备重定向**。
3. 双击“**允许此计算机中受支持的其他 RemoteFX USB 设备的 RDP 重定向**”。
4. 选择“**已启用**”，然后选择“**在 RemoteFX USB 重定向访问权限中的管理员和用户**”。
5. 使用管理权限打开命令提示符，然后运行以下命令︰
   
        gpupdate /force
6. 重新启动计算机。

你还可以使用组策略管理工具创建 USB 重定向策略并将其应用到域中的所有计算机︰

1. 以域管理员身份登录到域控制器。
2. 打开组策略管理控制台。 （单击“**开始 > 管理工具 > 组策略管理**”。）
3. 导航到你想要为其创建策略的域或组织单位。
4. 用鼠标右键单击“**默认域策略**”，然后单击“**编辑**”。
5. 打开**计算机配置\策略\管理模板\Windows 组件\远程桌面服务\远程桌面连接客户端\RemoteFX USB 设备重定向**。
6. 双击“**允许此计算机中受支持的其他 RemoteFX USB 设备的 RDP 重定向**”。
7. 选择“**已启用**”，然后选择“**在 RemoteFX USB 重定向访问权限中的管理员和用户**”。
8. 单击 **“确定”**。  


