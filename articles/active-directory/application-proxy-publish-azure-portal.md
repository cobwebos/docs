---
title: "使用 Azure AD 应用程序代理发布应用 | Microsoft Docs"
description: "在 Azure 门户中使用 Azure AD 应用程序代理将本地应用程序发布到云。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: harshja
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 5acfbfbe7327fc28fa909e6ef7e0f9b6ce8b0e54
ms.openlocfilehash: e0be8f4617c2fdbe1cb6fe2f904bf9ea7056c55e

---


# <a name="publish-applications-using-azure-ad-application-proxy---public-preview"></a>使用 Azure AD 应用程序代理（公共预览版）发布应用程序

> [!div class="op_single_selector"]
> * [Azure 门户](application-proxy-publish-azure-portal.md)
> * [Azure 经典门户](active-directory-application-proxy-publish.md)

Azure Active Directory (AD) 应用程序代理可发布要通过 Internet 访问的本地应用程序，帮助用户为远程辅助角色提供支持。 你可以通过 Azure 门户发布应用程序，这些应用程序在本地网络上运行，允许用户从网络外部进行安全的远程访问。

本文演示的步骤用于通过应用程序代理发布本地应用。 阅读完本文后，你将能够根据个性化信息或安全要求配置应用程序的单一登录。

如果不熟悉应用程序代理，可通过[如何提供对本地应用程序的安全远程访问](active-directory-application-proxy-get-started.md)一文详细了解此功能。


## <a name="publish-an-on-premises-app-for-remote-access"></a>发布进行远程访问的本地应用


> [!TIP]
> 若是第一次使用应用程序代理，请选择一个已经设置好进行基于密码的身份验证的应用程序。 应用程序代理支持其他类型的身份验证，但基于密码的应用是最容易快速启动并运行的。 

1. 在 [Azure 门户](https://portal.azure.com/)中，以管理员身份登录。
2. 选择“Azure Active Directory” > “企业应用程序” > “添加”。

  ![添加企业应用程序](./media/application-proxy-publish-azure-portal/add-app.png)

3. 在“类别”页中，选择“本地应用程序”。  

  ![添加自己的应用程序](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. 提供有关应用程序的以下信息：

   - **名称**：要显示在访问面板上的应用程序名称。 

   - **内部 URL**：这是应用程序代理连接器用于从专用网络内部访问应用程序的地址。 你可以提供后端服务器上要发布的特定路径，而服务器的其余部分则不发布。 通过这种方式，可以在同一服务器上将不同站点发布为不同应用，并为每个站点提供其自己的名称和访问规则。

     > [!TIP]
     > 如果发布路径，请确保它包含应用程序的所有必要映像、脚本和样式表。 例如，如果应用位于 https://yourapp/app 并使用位于 https://yourapp/media 的映像，则应将 https://yourapp/ 发布为路径。

   - **外部 URL**：用户从网络外部访问应用时需使用的地址。
   - **预先身份验证**：应用程序代理如何在向用户提供对应用程序的访问权限之前验证用户。 

     - Azure Active Directory：应用程序代理会重定向用户以便使用 Azure AD 登录，这会对他们针对目录和应用程序的权限进行身份验证。 我们建议保留此选项作为默认值。
     - 直通：用户不必向 Azure Active Directory 进行身份验证即可访问应用程序。 仍可在后端设置身份验证要求。
   - **转换标头中的 URL?**：选择是转换标头中的 URL，还是保留原始值。 
   - **连接器组**：连接器处理对应用程序的远程访问，连接器组用于按区域、网络或用途组织连接器和应用。 如果尚未创建任何连接器组，系统会将应用分配到**默认**组，并会显示一条警告消息，要求用户[创建连接器组](active-directory-application-proxy-connectors-azure-portal.md)。

   ![配置你的应用程序](./media/application-proxy-publish-azure-portal/configure-app.png)

8. 选择“添加”。


## <a name="add-a-test-user"></a>添加测试用户 

若要测试应用是否已正确发布，可添加一个你有权访问的用户帐户。 

1. 回到“快速启动”边栏选项卡，选择“分配用于测试的用户”。

  ![分配用于测试的用户](./media/application-proxy-publish-azure-portal/assign-user.png)

2. 在“用户和组”边栏选项卡中，选择“添加”。

  ![添加用户或组](./media/application-proxy-publish-azure-portal/add-user.png)

3. 在“添加分配”边栏选项卡上选择“用户和组”，然后选择要添加的帐户。 
4. 选择“分配”。

## <a name="test-your-published-app"></a>测试已发布的应用

在浏览器中，导航到在发布步骤中配置的外部 URL。 此时会看到开始屏幕，并能够使用已设置的测试帐户登录。

![测试已发布的应用](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>后续步骤
- [下载连接器](active-directory-application-proxy-enable.md)并[创建连接器组](active-directory-application-proxy-connectors-azure-portal.md)，以便将应用程序发布到单独的网络和位置。

- 为新发布的应用[设置单一登录](application-proxy-sso-azure-portal.md)



<!--HONumber=Feb17_HO2-->


