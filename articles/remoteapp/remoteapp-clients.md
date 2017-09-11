---
title: "从任何设备访问你的应用 |Microsoft Docs"
description: "了解 Azure RemoteApp 支持哪些客户端，以及如何访问你的应用。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: fb7bd17d-7aa8-43fd-9278-f96e0e9308e4
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: c07773be6e4a2274287920de9420f4c8b96f58e1
ms.contentlocale: zh-cn
ms.lasthandoff: 03/31/2017


---
# <a name="accessing-your-apps-in-azure-remoteapp"></a>在 Azure RemoteApp 中访问应用
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

Azure RemoteApp 的优点之一是，可以从任何设备访问应用。 另外一个更大的优点是，你可以在一个设备上开始工作，然后无缝转换到另一台设备上，并从中断处继续工作。 若要开始，你需要下载适合你设备的客户端并登录到服务。

在本主题中，我们将检查当前支持的客户端，并查看在我向你展示如何从每个客户端登录到 RemoteApp 之前，如何下载它们。

## <a name="supported-clients"></a>支持的客户端
如果你的设备运行以下操作系统之一，那么你可以使用下面的步骤访问 RemoteApp：

* Windows 10 
* Windows 8.1
* Windows 8
* Windows 7 Service Pack 1
* Windows Phone 8.1
* iOS
* Mac OS X
* Android

 瘦客户端会怎么样？ 支持以下 Windows 嵌入式瘦客户端：

* Windows Embedded Standard 7
* Windows Embedded 8 Standard
* Windows Embedded 8.1 Industry Pro
* Windows 10 IoT Enterprise

## <a name="downloading-the-client"></a>下载客户端
无论你使用何种平台，都可以在[远程桌面客户端下载](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx)页面上找到访问 RemoteApp 所需的客户端。

单击不同的链接将直接开始下载客户端，或者将向你发送该平台在应用商店中的客户端下载页面。 按照屏幕上的说明安装客户端。

在设备上安装客户端并启动它之后，立即跳转到下面的相应部分，以了解如何从该客户端登录到 RemoteApp。

## <a name="android"></a>Android
从 Google Play 应用商店安装 Microsoft 远程桌面应用之后，就立即可以在“**远程桌面**”下方你的应用列表中找到它。

1. 启动应用会将你带到空连接中心，除非你已经使用过该应用。 要开始使用 Azure RemoteApp，请按添加按钮 **""+""**，并单击“**Azure RemoteApp**”。    
   
     ![空连接中心](./media/remoteapp-clients/Android1.png)
2. 你需要使用电子邮件地址登录才能访问该服务。 单击“**入门**”。
   
    ![登录提示](./media/remoteapp-clients/Android2.png)
3. 在下一页上，键入你的**电子邮件地址**并单击“ **继续**”。 这将使用 Azure Active Directory 开始登录过程。
   
    ![第一个 Azure Active Directory 页面](./media/remoteapp-clients/Android3.png)
4. 按照屏幕上的说明使用你的 Microsoft 帐户（以前称为“LiveID”） 或组织 ID 登录。 登录之后，可能会立即出现一个页面，其中会列出所有已收到的邀请。 如果你受到邀请，请选择你信任的邀请，然后单击“**完成**”。    
   
    ![邀请页面](./media/remoteapp-clients/Android4.png)
5. 在接受你的邀请之后，你有权访问的应用列表将被下载到你的设备并在连接中心可用。 单击其中一个应用以开始使用它。
   
    ![包含订阅源的连接中心](./media/remoteapp-clients/Android5.png)
6. 如果你还未收到邀请，也可以试用该服务。 要进行试用，请在出现提示时单击“**转至免费试用版**”。
   
    ![演示订阅源提示](./media/remoteapp-clients/Android6.png)
7. 这将使你能够访问一组基本应用，从而开始使用 RemoteApp。
   
    ![Azure RemoteApp 的演示订阅源](./media/remoteapp-clients/Android7.png)

## <a name="ios"></a>iOS
从 App store 安装 Microsoft 远程桌面应用之后，就立即可以在“**RD 客户端**”下方你的应用列表中找到它。

1. 启动应用会将你带到空连接中心，除非你已经使用过该应用。 要开始使用 Azure RemoteApp，请点击添加按钮 **""+""** 并点击“**添加 Azure RemoteApp**”。
   
    ![空连接中心](./media/remoteapp-clients/IOS1.png)
2. 你需要使用你的电子邮件地址登录才能访问该服务，要启动该过程，请键入你的**电子邮件地址**并单击“**继续**”。
   
    ![登录提示](./media/remoteapp-clients/picture1.png)
3. 按照屏幕上的说明使用你的 Microsoft 帐户 (LiveID) 或组织 ID 登录。 登录之后，可能会立即出现一个页面，其中会列出所有已收到的邀请。 如果你受到邀请，请选择你信任的邀请，然后单击“**完成**”。
   
    ![邀请页面](./media/remoteapp-clients/IOS3.png)
4. 在接受你的邀请之后，你有权访问的应用列表将被下载到你的设备并在连接中心可用。 单击其中一个应用以启动它并开始使用它。
   
    ![包含订阅源的连接中心](./media/remoteapp-clients/IOS4.png)
5. 如果你还未收到邀请，也可以试用该服务。 要进行试用，请在出现提示时单击“**转至免费试用版**”。
   
    ![演示订阅源提示](./media/remoteapp-clients/IOS5.png)
6. 这将使你能够访问一组基本应用，从而开始使用 RemoteApp。
   
    ![Azure RemoteApp 的演示订阅源](./media/remoteapp-clients/IOS6.png)

## <a name="mac-os-x"></a>Mac OS X
从 App store 安装 Microsoft 远程桌面应用之后，就立即可以在“**Microsoft 远程桌面**”下方你的应用列表中找到它。

1. 启动应用会将你带到空连接中心，除非你已经使用过该应用。 要开始使用 Azure RemoteApp，请单击“**Azure RemoteApp**”按钮。
   
    ![空连接中心](./media/remoteapp-clients/Mac1.png)
2. 你需要使用你的电子邮件地址登录才能访问该服务，要启动该过程，请单击“**入门**”。
   
    ![登录提示](./media/remoteapp-clients/Mac2.png)
3. 在下一页上，键入你的**电子邮件地址**并单击“ **继续**”。 这将使用 Azure Active Directory 开始登录过程。
   
    ![第一个 Azure Active Directory 页面](./media/remoteapp-clients/picture2.png)
4. 按照屏幕上的说明使用你的 Microsoft 帐户 (LiveID) 或组织 ID 登录。 登录之后，可能会立即出现一个页面，其中会列出所有已收到的邀请。 如果你受到邀请，请选择你信任的邀请，然后关闭对话框。
   
    ![邀请页面](./media/remoteapp-clients/Mac4.png)
5. 在接受你的邀请之后，你有权访问的应用列表将被下载到你的设备并在连接中心可用。 双击其中一个应用以启动它并开始使用它。
   
    ![包含订阅源的连接中心](./media/remoteapp-clients/Mac5.png)
6. 如果你还未收到邀请，也可以试用该服务。 要进行试用，请在出现提示时单击“ **转至免费试用版**”。
   
    ![演示订阅源提示](./media/remoteapp-clients/Mac6.png)
7. 这将使你能够访问一组基本应用，从而开始使用 RemoteApp。
   
    ![Azure RemoteApp 的演示订阅源](./media/remoteapp-clients/Mac7.png)

## <a name="windows-all-supported-versions-except-windows-phone"></a>Windows（除了 Windows Phone 之外所有支持的版本）
客户端在完成安装之后会自动启动，但是，当你稍后需要再次访问它时，可以在名称 **Azure RemoteApp** 下面你的应用列表中找到它。

1. 启动客户端之后，你看到的第一个页面是 Azure RemoteApp 的欢迎使用页面。 若要继续，请单击“**入门**”。
   
    ![Azure RemoteApp 客户端的欢迎页面](./media/remoteapp-clients/Windows1.png)
2. 下一个页面会使用 Azure Active Directory 开始 Azure RemoteApp 的登录过程。 如果你过去曾使用过 Microsoft 服务，此过程应该看起来非常熟悉。 通过键入你的**电子邮件地址**，然后单击“**继续**”开始登录过程。
   
    ![第一个 Azure Active Directory 提示](./media/remoteapp-clients/Windows2.png)
3. 按照屏幕上的说明使用你的 Microsoft 帐户 (LiveID) 或组织 ID 登录。 登录之后，可能会立即出现一个页面，其中会列出所有已收到的邀请。 如果你受到邀请，请选择你信任的邀请，然后单击“**完成**”。
   
    ![Azure RemoteApp 客户端的“邀请”页面](./media/remoteapp-clients/Windows3.png)
4. 在接受你的邀请之后，你有权访问的应用列表将被下载到你的设备并在连接中心可用。 双击其中一个应用以启动它并开始使用它。
   
    ![Azure RemoteApp 客户端的连接中心](./media/remoteapp-clients/Windows4.png)
5. 如果还没有人向你发送邀请，不必担心，我们会向你发送邀请！ 你仍有权访问演示集合，因此你可以测试该服务。
   
    ![Azure RemoteApp 的演示订阅源](./media/remoteapp-clients/Windows5.png)

## <a name="windows-phone-81"></a>Windows Phone 8.1
从 Windows Phone 8.1 应用商店安装 Microsoft 远程桌面应用之后，就立即可以在“**远程桌面**”下方你的应用列表中找到它。

1. 启动应用会直接将你带到空连接中心，除非你已经使用过该应用。 要开始使用 Azure RemoteApp，请单击位于屏幕底部的添加按钮**""+""**。
   
    ![空连接中心](./media/remoteapp-clients/WinPhone1.png)
2. 然后，单击“**Azure RemoteApp**”。
   
    ![添加项目页面](./media/remoteapp-clients/WinPhone2.png)
3. 你需要使用你的电子邮件地址登录才能访问该服务，要启动该过程，请单击“**连接**”。
   
    ![登录提示](./media/remoteapp-clients/WinPhone3.png)
4. 在下一页上，键入你的**电子邮件地址**并单击“ **继续**”。 这将使用 Azure Active Directory 开始登录过程。
   
    ![第一个 Azure Active Directory 页面](./media/remoteapp-clients/WinPhone4.png)
5. 按照屏幕上的说明使用你的 Microsoft 帐户 (LiveID) 或组织 ID 登录。 登录之后，可能会立即出现一个页面，其中会列出所有已收到的邀请。 如果你受到邀请，请选择你信任的邀请，然后单击“**保存**”。
   
    ![邀请页面](./media/remoteapp-clients/WinPhone5.png)
6. 在接受你的邀请之后，你有权访问的应用列表将被下载到你的设备并在连接中心可用。 单击其中一个应用以启动它并开始使用它。
   
    ![包含订阅源的连接中心](./media/remoteapp-clients/WinPhone6.png)
7. 如果你还未收到邀请，也可以试用该服务。 要试用该服务，请在出现提示时单击“**是**”。
   
    ![演示订阅源提示](./media/remoteapp-clients/WinPhone7.png)
8. 这将使你能够访问一组基本应用，从而开始使用 RemoteApp。
   
    ![Azure RemoteApp 的演示订阅源](./media/remoteapp-clients/WinPhone8.png)


