---
title: "将用户添加到 Azure RemoteApp 集合 | Microsoft Docs"
description: "了解如何将用户添加到 Azure RemoteApp 集合"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 6b751fd2-2b11-499f-a2eb-12cb47f3129b
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a84aa7e3ac7de717c2a628abcf6f5f11b05411e8
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-add-a-user-to-your-azure-remoteapp-collection"></a>如何将用户添加到 Azure RemoteApp 集合
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

你必须先授予用户访问你的集合的权限，他们才能在 Azure RemoteApp 中看到和使用你的应用。 此操作非常简单︰在“**用户访问**”选项卡上，输入用户的帐户信息，然后单击复选标记。

你需要哪个帐户信息？ 这取决于你创建的集合的类型（云或混合）以及是否在该集合中使用 Office 365 ProPlus。

## <a name="supported-user-identities"></a>支持的用户标识
不同的集合类型（云与混合）支持使用不同的用户标识访问应用程序。  

对于某个 RemoteApp 混合集合，你需要在本地设置一个 Active Directory 域基础结构并设置一个带有目录集成功能（还可选择带有单一登录功能）的 Azure Active Directory 租户。 此外，你需要在本地目录中创建一些 Active Directory 对象。  

对于 RemoteApp 的云端收藏，可以向任何具有 Azure Active Directory 支持标识的用户授予对 RemoteApp 的用户访问权限，以包括 Microsoft 帐户。  请参阅下表。

Office 365 用户是 Azure Active Directory 用户。 如果他们有 Azure Active Directory 混合、目录同步帐户，则可以在 RemoteApp 混合部署中授予他们用户访问权限。   

在要了解在你的集合中支持哪个标识以及 Active Directory 要求有哪些时，可以将此表用作一个快速参考。

| 用户帐户 | 云 | 混合 |
| --- | --- | --- |
| Microsoft 帐户 |是 |否 |
| Azure Active Directory (Azure AD) | | |
| 仅 Azure AD 云 |是 |否 |
| ADsync，带密码同步 |是 |是 |
| ADsync，不带密码同步 |是 |否 |
| ADsync，带 AD FS |是 |是 |
| [第三方 Azure 支持标识提供者](https://msdn.microsoft.com/library/azure/jj679342.aspx)  （例如 Ping） |是 |是 |
| Multi-Factor Authentication |是 |是 |

要了解如何配置 Active Directory for RemoteApp，请查看[详细信息](remoteapp-ad.md)。

> [!NOTE]
> Azure Active Directory 用户必须来自与订阅关联的租户。 （可以在门户的“**设置**”选项卡上查看和修改订阅。 有关详细信息，请参阅[更改 RemoteApp 使用的 Azure Active Directory 租户](remoteapp-changetenant.md)。）
> 
> 

## <a name="office-365-proplus-user-account-information"></a>Office 365 ProPlus 用户帐户信息
如果你在集合中使用 Office 365 ProPlus 模板映像，*或者*如果你创建了使用 Office 365 的自定义映像，则仅允许你添加将 Office 365 订阅作为订阅的默认域的 Azure Active Directory 用户。 有关详细信息，请参阅[将 Office 365 与 Azure RemoteApp 一起使用](remoteapp-o365.md)。


