---
title: "在 Teams 中访问 Azure AD 应用代理应用 | Microsoft Docs"
description: "使用 Azure AD 应用程序通过 Microsoft Teams 代理访问本地应用程序。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 24e22d7314de536714a825cd7035d2cec2112278
ms.contentlocale: zh-cn
ms.lasthandoff: 07/14/2017

---

# <a name="access-your-on-premises-applications-through-microsoft-teams"></a>通过 Microsoft Teams 访问本地应用程序

无论身在何处，使用 Azure Active Directory 应用程序代理可单一登录到本地应用程序，Microsoft Teams 将这些功能集为一体，可简化协作。 将两者集成意味着用户可在任何情况下与队友高效合作。 

用户可以[使用标记](https://support.office.com/article/Video-Using-Tabs-7350a03e-017a-4a00-a6ae-1c9fe8c497b3?ui=en-US&rs=en-US&ad=US)将云应用添加到其 Teams 通道，但如果他们所使用的 SharePoint 站点或规划工具在本地托管会发生什么情况？ 可以使用应用程序代理来解决该问题。 用户可以使用一直以来远程访问其应用所使用的相同外部 URL，通过应用程序代理将发布的应用添加到其通道。 并且应用程序代理通过 Azure Active Directory 进行身份验证，所以具有相同的单一登录体验。


## <a name="install-the-application-proxy-connector-and-publish-your-app"></a>安装应用程序代理连接器并发布应用

如果尚未执行，请[为租户配置应用程序代理并安装连接器](active-directory-application-proxy-enable.md)。 然后[发布进行远程访问的本地应用程序](application-proxy-publish-azure-portal.md)。 发布应用时，请记下外部 URL，因为最终用户将应用添加到 Teams 时需要该信息。

如果已发布应用，但忘记了其外部 URL，请在 [Azure 门户](https://portal.azure.com)中查找。 登录，然后导航到“Azure Active Directory” > “企业应用程序” > “所有应用程序”>选择应用>“应用程序代理”。

## <a name="add-your-app-to-teams"></a>将应用添加到 Teams 中

通过应用程序代理发布应用后，让用户知道他们可以直接在 Teams 通道中将该应用添加为选项卡。 让用户按照以下三个步骤进行操作：

1. 导航到想要添加此应用的 Teams 通道，并选择“+”添加选项卡。

   ![选择“添加”选项卡](./media/application-proxy-teams/add-tab.png)

2. 从选项卡选项中选择“网站”。

   ![添加网站](./media/application-proxy-teams/website.png)

3. 为选项卡命名，并将 URL 设置为应用程序代理外部 URL。 

   ![配置选项卡名称和 URL](./media/application-proxy-teams/tab-name-url.png)

团队中的某位成员添加选项卡后，通道中的每位用户都可看到该选项卡。 有权访问该应用的所有用户都可以使用其用于 Microsoft Teams 的凭据进行单一登录访问。 无权访问该应用的所有用户可以在 Teams 看到该选项卡，但需要获得对本地应用和该应用的 Azure 门户已发布版本的访问权限才不会被阻止。 

## <a name="next-steps"></a>后续步骤

- 了解如何使用应用程序代理[发布本地 SharePoint 站点](application-proxy-enable-remote-access-sharepoint.md)。
- 将应用配置为使用[自定义域](active-directory-application-proxy-custom-domains.md)作为其外部 URL。 

