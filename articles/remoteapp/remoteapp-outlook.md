---
title: "在 Azure RemoteApp 中使用 Outlook | Microsoft Docs"
description: "了解在 Azure RemoteApp 中如何配置和使用 Outlook | Microsoft Azure"
services: remoteapp
documentationcenter: 
author: pavithir
manager: mbaldwin
ms.assetid: cb2a498f-9539-4522-a874-542114926a61
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a6d4fbdf0e552f50673092183e893841ec0c5aa4
ms.lasthandoff: 03/31/2017


---
# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>在 Azure RemoteApp 中使用 Microsoft Outlook
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

Azure RemoteApp 支持 Microsoft Outlook O365。 请阅读有关 [Office 如何在 Azure RemoteApp 中运行](remoteapp-officesubscription.md)了解详细信息。 在 Azure RemoteApp 使用 Outlook 时，建议使用以下设置。

## <a name="cached-mode"></a>缓存模式
在 Azure RemoteApp 中使用 Outlook 时，建议将其配置为缓存模式。 当你配置 Outlook 2013 帐户以使用缓存 Exchange 模式时，Outlook 2013 将通过用户的 Microsoft Exchange 邮箱副本运行，该副本连同脱机通讯薄 (OAB) 一起存储在用户计算机上的脱机数据文件（OST 文件）中。 缓存的邮箱和 OAB 将从 O365 服务定期更新。 请阅读有关 [缓存和联机模式之间的差异](https://technet.microsoft.com/library/jj683103.aspx)了解详细信息。

用户可在帐户设置期间，或通过更改帐户设置，选择“缓存 Exchange 模式”或“联机模式”。 你也可以使用 Office 自定义工具 (OCT) 或组策略部署一种模式或另一种模式。  

请阅读 [启用缓存模式的分步说明](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc)了解详细信息。

## <a name="search"></a>搜索
在 Azure RemoteApp 中，Outlook 中的搜索功能有其局限性。 因为 Azure RemoteApp 使用共用的 VM 来处理用户会话。 搜索索引将取决于会因不同 VM 而变化的计算机 ID。 用户每次登录 Azure RemoteApp 时，可能会将其定向到新的 VM。 这意味着，如果我们启用本地搜索，每当计算机 ID 更改时（用户在不同的 VM 上时），索引器就会运行。 根据 OST 文件的大小，索引器可能需要很长时间才能完成并且耗尽其他应用程序所需的资源。 搜索起来不仅速度慢，而且可能无法生成结果。 使用联机模式帐户配置文件可以解决此问题，但由于缺乏本地缓存，整体性能将会受到影响（有关缓存和联机模式之间差异的详细信息，请前往上面的链接）。 遗憾的是，在 Outlook 2013 中不能禁用索引/本地搜索并且不能在默认情况下启用联机搜索。


