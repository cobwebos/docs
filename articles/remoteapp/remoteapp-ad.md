---
title: "Azure RemoteApp 的 Azure AD 和 Active Directory 要求 | Microsoft Docs"
description: "了解如何设置 Active Directory 才能使用 Azure RemoteApp。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 66366b25-6012-45fa-a4f6-da0ddfe0b486
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 232901ab919c63ea70e52afb845240b41a517c51
ms.lasthandoff: 03/31/2017


---
# <a name="azure-ad--active-directory-requirements-for-azure-remoteapp"></a>Azure RemoteApp 的 Azure AD 和 Active Directory 要求
> [!IMPORTANT]
> Azure RemoteApp 将于 2017 年 8 月 31 日停用。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

对于 Azure RemoteApp 混合集合，或要使用 AD Connect 建立联合的云集合，你必须执行下列操作。

### <a name="connect-azure-ad-and-active-directory"></a>连接 Azure AD 和 Active Directory
如果要连接 Azure AD 租户和本地部署的 Active Directory 环境，请使用 AD Connect。 仅需[单击 4 次](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/)即可连接这两个目录。

注意 - 混合集合需进行目录同步。

### <a name="make-sure-your-domaincom-match"></a>确保“@domain.com”匹配
开始之前，请确保本地林的 UPN 与 Azure AD 域的后缀相匹配。 

在 Azure AD 中设置 UPN 域后缀后，所有登录 Azure RemoteApp 的用户都将以“user@<the suffix you set up>”身份登录。 请确保用户也可以使用相同的 user@suffix 登录到本地域。 在某些情况下，可以在 Azure AD 中设置一个域名的同时，在本地为用户指定不同的域后缀。 在这种情况下，用户将无法通过 Azure RemoteApp 连接到任何已加入域的计算机或资源。

例如，如果在 AAD 中将 UPN 域后缀设置为 contoso.com，但本地/AD 上的某些用户被配置为使用 @contoso.uk 登录，则这些用户将无法正确登录 ARA 集合。 AAD 和 AD 中的用户 UPN 必须相同才能进行登录。

### <a name="create-objects-for-azure-remoteapp"></a>创建 Azure RemoteApp 的对象
还需要创建以下本地 Active Directory 对象：

* 服务帐户，以通过将 RDSH 终结点加入到本地域，提供 RemoteApp 程序的域资源的访问权限。
* 组织单位 (OU)，以包含 RemoteApp 计算机对象。 建议使用的 OU（但不作要求）来隔离将帐户与将用于 RemoteApp 的策略。

创建 RemoteApp 集合时会需要这两个对象，因此，请确保首先执行这些步骤。


