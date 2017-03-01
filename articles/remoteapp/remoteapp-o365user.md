---
title: "如何将 Azure RemoteApp 和 Office 365 用户帐户一起使用 | Microsoft Docs"
description: "了解如何将 Azure RemoteApp 和 Office 365 用户帐户一起使用"
services: remoteapp
documentationcenter: 
author: piotrci
manager: mbaldwin
ms.assetid: aba70fd3-60b0-4b44-9321-1ab18760ccd5
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 8744c5a133738fc60b86e7abd4b0d4b1cb28314c


---
# <a name="how-to-use-azure-remoteapp-with-office-365-user-accounts"></a>如何将 Azure RemoteApp 和 Office 365 用户帐户一起使用
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

如果你有 Office 365 订阅，则会有一个存储用于访问 Office 365 服务的用户名和密码的 Azure Active Directory。 例如，用户激活 Office 365 ProPlus 后，对 Azure AD 进行身份验证以检查许可证。 多数客户想要使用相同的目录来使用 Azure RemoteApp。

如果你正在部署 Azure RemoteApp，则很可能在使用关联到不同 Azure AD 的 Azure 订阅。 为了使用 Office 365 目录，则需要将 Azure 订阅移动到该目录。

有关如何部署 Office 365 客户端应用程序的信息，请参阅 [如何将 Office 365 订阅和 Azure RemoteApp 一起使用](remoteapp-officesubscription.md)。

## <a name="phase-1-register-your-free-office-365-azure-active-directory-subscription"></a>阶段 1：注册免费的 Office 365 Azure Active Directory 订阅
如果正在使用 Azure 经典门户，则使用 [注册免费的 Azure Active Directory 订阅](https://technet.microsoft.com/library/dn832618.aspx) 中的步骤来通过 Azure 管理门户获取 Azure AD 的管理访问权限。 此过程结束后，应该可以登录到 Azure 门户并在此处看到你的目录 – 此时无法查看更多内容，因为正在和 Azure RemoteApp 一起使用的完整的 Azure 订阅位于不同的目录中。

记住在此步骤中创建的管理员帐户的名称和密码 –在阶段 2 中将需要使用他们。

如果正在使用 Azure 门户，则查看 [如何使用 Office 365 门户注册和激活免费的 Azure Active Directory](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/)。

## <a name="phase-2-change-the-azure-ad-associated-with-your-azure-subscription"></a>阶段 2：更改与你的 Azure 订阅关联的 Azure AD。
我们要将 Azure 订阅从其当前目录更改到我们在阶段 1 中使用的 Office 365 目录。

按照 [在 Azure RemoteApp 中更改 Azure Active Directory 租户](remoteapp-changetenant.md) 中所述的说明操作。 特别注意以下步骤：

* 步骤 #1：如果已在此订阅中部署 Azure RemoteApp (ARA)，请务必首先将所有 ARA 集合中的所有 Azure AD 用户帐户移除，然后再尝试其他操作。 或者，可以考虑删除所有的现有集合。
* 步骤 #2：这是关键步骤。 需要在订阅上以服务管理员身份使用 Microsoft 帐户（例如 @outlook.com)；这是因为不能将已有的 Azure AD 中的任意用户帐户附加到订阅 – 如果这样做了，则无法将其移动到不同的 Azure AD。
* 步骤 #4：添加已有目录时，系统将要求你使用该目录的管理员帐户登录。 请确保使用阶段 1 中的管理员帐户。
* 步骤 #5：将订阅的父级目录更改到你的 Office 365 目录。 最终结果应该是，你的订阅在“设置 -> 订阅”中列出了 Office 365 目录。 
  ![更改订阅的父级目录](./media/remoteapp-o365user/settings.png)

此时你的 Azure RemoteApp 订阅已关联到你的 Office 365 Azure AD； 你可以将现有的 Office 365 用户帐户和 Azure RemoteApp 一起使用！




<!--HONumber=Dec16_HO2-->


