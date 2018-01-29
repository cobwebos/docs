---
title: "使用 Mobile Engagement REST API 进行身份验证：手动设置"
description: "描述如何为 Mobile Engagement REST API 手动设置身份验证"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0b4a999c6778040e71f862d3a010b6635e84b26e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis-manual-setup"></a>使用 Mobile Engagement REST API 进行身份验证：手动设置
此文档是[使用 Mobile Engagement REST API 进行身份验证](mobile-engagement-api-authentication.md)的一个附录。 请务必先阅读该文章来了解上下文。 它还介绍了一种使用 Azure 门户为 Mobile Engagement REST API 进行一次性身份验证设置的替代方法。

> [!NOTE]
> 下面的说明基于[此 Active Directory 指南](../azure-resource-manager/resource-group-create-service-principal-portal.md)。 它们针对 Mobile Engagement API 的身份验证要求进行了自定义。 如果想详细了解以下步骤，请参考这些说明。

1. 通过 [Azure 门户](https://portal.azure.com/)登录到 Azure 帐户。
2. 在左侧窗格中选择“**Active Directory**”。

   ![选择 Active Directory][1]

3. 若要查看目录中的应用程序，请选择“应用注册”。

   ![查看应用程序][3]

4. 选择“新建应用程序注册”。

   ![添加应用程序][4]

5. 填写应用程序的名称。 将应用程序类型保留为“Web 应用/API”，然后选择“下一步”按钮。 可以为“登录 URL”提供任何虚拟 URL。 此方案中不会使用它们并且不会对 URL 本身进行验证。

   完成后，将获得具有你提供的名称的 Azure Active Directory (Azure AD) 应用。 它是你的 **AD\_APP\_NAME**，请务必记下它。

   ![应用程序名称][8]

7. 选择应用名称。

8. 找到“应用程序 ID”并记下它。 它是将用作 API 调用的 **CLIENT\_ID** 的客户端 ID。

   ![查找应用程序 ID][10]

9. 找到右侧的“密钥”部分。

   ![“密钥”部分][11]

10. 创建一个新密钥，然后立即复制它。 它不会再次显示。

    ![包含密钥详细信息的密钥窗格][12]

    > [!IMPORTANT]
    > 此密钥在经过指定的持续时间后将过期。 请确保在过期时续订此密钥，否则，API 身份验证将不再工作。 如果认为此密钥的安全已受到危害，可以将其删除并重新创建。
    >
    
11. 选择页面顶部的“终结点”按钮。 然后复制 **OAUTH 2.0 令牌终结点**。

    ![复制终结点][14]

16. 此终结点采用以下形式，其中，URL 中的 GUID 是你的 **TENANT_ID**：`https://login.microsoftonline.com/<GUID>/oauth2/token`

17. 接下来，配置此应用上的权限。 若要开始此过程，请转到 [Azure 门户](https://portal.azure.com)。

18. 选择“资源组”，然后查找 **MobileEngagement** 资源组。

    ![查找 MobileEngagement][15]

19. 选择 **MobileEngagement** 资源组，然后选择“所有设置”。

    ![浏览到 MobileEngagement 的设置][16]

20. 在“设置”部分中选择“用户”。 然后，若要添加用户，请选择“添加”。

    ![添加用户][17]

21. 单击“选择角色”。

    ![选择角色][18]

22. 选择“所有者”。

    ![选择“所有者”作为角色][19]

23. 在搜索框中搜索你的应用程序的名称 **AD\_APP\_NAME**。 此名称默认情况下不显示在此处。 找到此名称后，请选择它。 然后在此部分的底部单击“选择”。

    ![选择名称][20]

24. 在“添加访问权限”部分中，它显示为“1 个用户，0 个组”。 若要确认更改，请选择“确定”。

    ![确认添加的用户][21]

现在，你已完成了所需的 Azure AD 配置，并且已经做好了调用 API 的一切准备工作。

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



