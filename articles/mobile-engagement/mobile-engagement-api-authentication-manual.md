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
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 458140eb9491950a8cbd04a2702da5d7ac637129


---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>使用 Mobile Engagement REST API 进行身份验证 - 手动设置
这是[使用 Mobile Engagement REST API 进行身份验证](mobile-engagement-api-authentication.md)一个附录文档。 请务必先阅读它来获取相关内容。 此处描述一种使用 Azure 门户为 Mobile Engagement REST API 进行一次性身份验证设置的替代方法。 

> [!NOTE]
> 下面的说明均基于此 [Active Directory 指南](../resource-group-create-service-principal-portal.md)，并且是根据 Mobile Engagement API 身份验证的要求进行定制的。 因此，如果你想详细了解以下步骤，请参考这些说明。 
> 
> 

1. 通过[经典门户](https://manage.windowsazure.com/)登录到你的 Azure 帐户。
2. 在左侧窗格中选择“**Active Directory**”。
   
     ![选择“Active Directory”][1]
3. 在 Azure 门户中，选择“**默认 Active Directory**”。 
   
     ![选择目录][2]
   
   > [!IMPORTANT]
   > 仅当在你帐户的默认 Active Directory 中工作时，此方法才起作用；如果你是在某个在你的帐户中创建的 Active Directory 中工作，此方法将不起作用。 
   > 
   > 
4. 若要查看目录中的应用程序，请单击“**应用程序”**。
   
     ![查看应用程序][3]
5. 单击“**添加**”。 
   
     ![添加应用程序][4]
6. 单击“**添加我的组织正在开发的应用程序**”。
   
     ![新应用程序][5]
7. 填写应用程序的名称，选择“**Web 应用程序和/或 Web API**作为应用程序的类型，然后单击下一步按钮。
   
     ![命名应用程序][6]
8. 你可以为“**登录 URL**”和“**应用程序 ID URI**”提供任何虚拟 URL。 它们不会用于我们的方案以及未验证它们自己的 URL。  
   
     ![应用程序属性][7]
9. 完成这部分的内容后，你将有一个使用你先前提供的名称的 AAD 应用，其名称应类似如下所示： 这是你的 **AD\_APP\_APP**，请记下它。  
   
     ![应用程序名称][8]
10. 单击应用程序名称，然后单击“**配置**”。
    
      ![配置应用][9]
11. 记下将用作你 API 调用的 **CLIENT\_ID** 的客户端 ID。 
    
     ![配置应用][10]
12. 向下滚动到“**密钥**”部分，添加一个具有最好 2 年（过期）持续时间的密钥，然后单击“**保存**”。 
    
     ![配置应用][11]
13. 立即复制为密钥显示的值，因为系统仅现在会显示该值且不会存储它，因此，该值将永远不会再显示。 如果丢失了该值，你必须生成一个新密钥。 对于你的 API 调用，这将是 **CLIENT_SECRET**。 
    
     ![配置应用][12]
    
    > [!IMPORTANT]
    > 此密钥将在你指定的持续时间结束时过期，因此，请确保在到期之前续订该密钥，否则你的 API 身份验证将不再起作用。 如果你认为此密钥已遭到破坏，还可以删除它并重新创建一个。
    > 
    > 
14. 现在单击“**查看终结点**”按钮，此操作将打开“**应用终结点**”对话框。 
    
    ![][13]
15. 从“应用终结点”对话框中，复制“**OAuth 2.0 令牌终结点**”。 
    
    ![][14]
16. 此终结点将使用以下形式，其中 URL 中的 GUID 是你的 **TENANT_ID**因此，请记下它： 
    
        https://login.microsoftonline.com/<GUID>/oauth2/token
17. 现在我们将继续配置此应用上的权限。 为此，你将需要打开 [Azure 门户](https://portal.azure.com)。 
18. 单击“**资源组**”并查找 **Mobile Engagement** 资源组。  
    
    ![][15]
19. 单击 **Mobile Engagement** 资源组，然后导航到此处的“**设置**”边栏选项卡。 
    
    ![][16]
20. 单击“设置”边栏选项卡中的“**用户**”，然后单击“**添加**”以添加用户。 
    
    ![][17]
21. 单击“**选择角色**”
    
    ![][18]
22. 单击“**所有者**”
    
    ![][19]
23. 在搜索框中搜索你的应用程序 **AD\_APP\_NAME** 的名称。 默认情况下此处你不会看到此名称。 找到此名称之后，立即选择它，然后单击边栏选项卡底部的“**选择**”。 
    
    ![][20]
24. 在“**添加访问**”边栏选项卡上，它将显示为“**1 个用户，0 个组**”。 单击此边栏选项卡上的“**确定**”以确认更改。 
    
    ![][21]

现在你已完成所需的 AAD 配置，并且已准备好调用 API 了。 

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
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
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






<!--HONumber=Nov16_HO3-->


