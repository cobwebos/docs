---
title: "Azure RemoteApp 模板映像中的内容是什么？ | Microsoft Docs"
description: "了解 Azure RemoteApp 包含的模板映像。"
services: remoteapp
documentationcenter: 
author: lizap
manager: mbaldwin
ms.assetid: 7f8442b2-81da-421e-a453-aa53ba2066b7
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: elizapo
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b589fb3b1cdbf1f14ece6adf43e1eb0313ff09df


---
# <a name="what-is-in-the-azure-remoteapp-template-images"></a>Azure RemoteApp 模板映像中的内容是什么？
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

Azure RemoteApp 订阅包括三个模板映像：

* Windows Server 2012
* Microsoft Office 365 ProPlus（需要 Office 365 订阅）
* Microsoft Office 2013 Professional Plus（仅试用版）

> [!IMPORTANT]
> Azure RemoteApp 订阅授予访问映像中软件的权限（需要单独订阅的 Office 365 ProPlus 和不能在生产中使用的 Office 2013 除外）。 这意味着可以与用户共享模板映像上的程序或应用。 例如，如果创建使用 Windows Server 2012 R2 映像的集合，则可以为用户发布通过 RemoteApp 访问的 System Center Endpoint Protection。
> 
> 查看 [RemoteApp 许可详细信息](remoteapp-licensing.md) 了解详细信息。 并参阅 [将 Office 与 Azure RemoteApp 一起使用](remoteapp-o365.md) 了解 Office 许可相关信息。
> 
> 

继续阅读了解每个映像所包含内容的详细信息。

## <a name="windows-server-2012-r2-the-vanilla-image"></a>Windows Server 2012 R2（“普通映像”）
此映像基于 Microsoft Windows Server 2012 R2 Datacenter 操作系统，安装有以下角色和功能，以便满足 Azure RemoteApp 模板映像的要求：

* .NET Framework 4.5, 3.5.1, 3.5
* 桌面体验
* 墨迹和手写服务
* 媒体基础
* 远程桌面会话主机
* Windows PowerShell 4.0
* Windows PowerShell ISE
* WoW64 支持

此映像还安装有以下应用程序：

* Adobe Flash Player
* Microsoft Silverlight
* Microsoft System Center 2012 Endpoint Protection
* Microsoft Windows Media Player

## <a name="microsoft-office-365-proplus-subscription-required"></a>Microsoft Office 365 ProPlus （需要订阅）
Office 365 是最常用的应用程序，因此创建了可以使用它的“自定义”映像。

此映像是普通映像的扩展，除了 Windows Server 2012 R2 映像中所述的组件外，还安装有以下 Microsoft Office 365 ProPlus 组件：

* Access
* Excel
* Lync
* OneNote
* OneDrive for Business（请注意，同步代理不支持与 Azure RemoteApp 一起使用）
* Outlook
* PowerPoint
* Word
* Microsoft Office 校对工具

此映像还包括 Visio Pro 和 Project Pro。

以及以下应用程序：

* SQL Native Client
* ODBC 驱动程序
* SQL Server Data Mining Client
* MasterDataServices Client
* Microsoft Publisher
* PowerQuery
* PowerMap

仅具有 Office 365 ProPlus 计划的用户可使用 Office 365 ProPlus 应用的完整功能。 有关 Office 365 订阅计划的详细信息，请参阅 [Office 365 服务计划](http://technet.microsoft.com/library/office-365-plan-options.aspx)。 仍有疑问？ 请查看 [Office 365 + RemoteApp](remoteapp-o365.md) 信息。 另请参阅新文章 [如何将 Office 365 订阅与 Azure RemoteApp 一起使用](remoteapp-officesubscription.md)。

请注意，需要分别许可 Office 365 ProPlus、Visio Pro 和 Project Pro - 它们都有自己的许可证。

## <a name="microsoft-office-2013-professional-plus-trial-only"></a>Microsoft Office 2013 Professional Plus（仅试用版）
在免费试用期间，可以使用 Office 2013 映像测试服务。

此映像是普通映像的扩展，除了 Windows Server 2012 R2 映像中所述的组件外，还安装有以下 Microsoft Office 2013 Professional Plus 组件：

* Access
* Excel
* Lync
* OneNote
* OneDrive for Business（请注意，同步代理不支持与 Azure RemoteApp 一起使用）
* Outlook
* PowerPoint
* Project
* Visio
* Word
* Microsoft Office 校对工具

> [!IMPORTANT]
> **法律信息：** 此映像不包括 Microsoft Office 许可证且 *不能用于生产*。 Office 2013 Professional Plus 映像仅适用于试用版。 如果想要将 Azure RemoteApp 中的 Office 应用用于生产，需要使用 Office 365 ProPlus 映像。 有关授予 Office 许可的详细信息，请参阅 [将 Office 365 与 Azure RemoteApp 一起使用](remoteapp-o365.md)
> 
> 




<!--HONumber=Nov16_HO2-->


