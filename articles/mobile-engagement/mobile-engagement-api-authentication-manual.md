---
title: "使用 Mobile Engagement REST API 进行身份验证 - 手动设置"
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
ms.openlocfilehash: 3b678acbae225c28223a2ee76e5be2462a529362
ms.sourcegitcommit: d6984ef8cc057423ff81efb4645af9d0b902f843
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>使用 Mobile Engagement REST API 进行身份验证 - 手动设置
此文档是[使用 Mobile Engagement REST API 进行身份验证](mobile-engagement-api-authentication.md)的附录文档。 请务必先阅读它来获取相关内容。
它描述一种使用 Azure 门户为 Mobile Engagement REST API 进行一次性身份验证设置的替代方法。

> [!NOTE]
> 下面的说明均基于此 [Active Directory 指南](../azure-resource-manager/resource-group-create-service-principal-portal.md)，并且是根据 Mobile Engagement API 身份验证的要求进行定制的。 因此，如果想详细了解以下步骤，请参考这些说明。

1. 通过 [Azure 门户](https://portal.azure.com/)登录 Azure 帐户。
2. 在左侧窗格中选择“**Active Directory**”。

     ![选择“Active Directory”][1]

3. 若要查看目录中的应用程序，请单击“应用注册”。

     ![查看应用程序][3]

4. 单击“新建应用程序注册”。

     ![添加应用程序][4]

5. 填写应用程序的名称，将应用程序的类型保留为“Web 应用/API”，并单击下一步按钮。 可以为“登录 URL”提供任何虚拟 URL：这些 URL 不用于此方案并且不对这些 URL 进行验证。
6. 完成后，你便获得具有你提供的名称的 Azure AD 应用。 该应用是 **AD\_APP\_NAME**，请记下它。

     ![应用程序名称][8]

7. 单击应用名称。
8. 找到“应用程序 ID”，记下它，它是要用作 API 调用的**客户端 \_ID** 的客户端 ID。

     ![配置应用][10]

9. 找到右侧的“密钥”部分。

     ![配置应用][11]

10. 创建新密钥并立即将其复制保存供以后使用。 它永远不会再显示。

     ![配置应用][12]

    > [!IMPORTANT]
    > 此密钥会在指定的持续时间结束时过期，因此，请确保在到期之前续订该密钥，否则 API 身份验证将不再起作用。 如果认为此密钥已遭到破坏，还可以删除它并重新创建一个。
    >
    >
11. 单击页面顶部的“终结点”按钮并复制 **OAUTH 2.0 令牌终结点**。

    ![][14]

16. 此终结点将采用以下形式，其中 URL 中的 GUID 是 **TENANT_ID**，因此请记下它：`https://login.microsoftonline.com/<GUID>/oauth2/token`
17. 现在我们将继续配置此应用上的权限。 为此，需要打开 [Azure 门户](https://portal.azure.com)。 
18. 单击“**资源组**”并查找 **Mobile Engagement** 资源组。

    ![][15]

19. 单击 **Mobile Engagement** 资源组，并导航到此处的“设置”部分。

    ![][16]

20. 单击“设置”部分中的“用户”，并单击“添加”以添加用户。

    ![][17]

21. 单击“选择角色”。

    ![][18]

22. 单击“所有者”。

    ![][19]

23. 在搜索框中搜索应用程序 **AD\_APP\_NAME** 的名称。 默认情况下此处看不到该项。 找到此名称之后，立即选择它，并单击此部分底部的“选择”。

    ![][20]

24. 在“添加访问权限”部分中，它会显示为“1 个用户，0 个组”。 单击此部分上的“确定”以确认更改。

    ![][21]

现在已完成所需的 Azure AD 配置，并且已准备好调用 API 了。

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



