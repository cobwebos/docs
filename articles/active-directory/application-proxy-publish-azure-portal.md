---
title: "使用 Azure AD 应用程序代理发布应用 | Microsoft Docs"
description: "在 Azure 门户中使用 Azure AD 应用程序代理将本地应用程序发布到云。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: e00a939f2b20ab8e0a2ddf0ff91e59db440213ac
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理发布应用程序

> [!div class="op_single_selector"]
> * [Azure 门户](application-proxy-publish-azure-portal.md)
> * [Azure 经典门户](active-directory-application-proxy-publish.md)

Azure Active Directory (AD) 应用程序代理可发布要通过 Internet 访问的本地应用程序，帮助用户为远程辅助角色提供支持。 你可以发布这些应用程序通过 Azure 门户，以提供从网络外部的安全远程的访问。

本文演示的步骤用于通过应用程序代理发布本地应用。 完成这篇文章后，你的用户将能够远程访问你的应用程序。 然后，就会准备好配置上单一登录、 个性化的信息和安全要求等应用程序的额外功能。

如果不熟悉应用程序代理，可通过[如何提供对本地应用程序的安全远程访问](active-directory-application-proxy-get-started.md)一文详细了解此功能。


## <a name="publish-an-on-premises-app-for-remote-access"></a>发布进行远程访问的本地应用

请按照下列步骤以发布你的应用的应用程序代理。 如果你已尚未下载，并为你的组织配置连接器，请转到[开始使用应用程序代理并安装连接器](active-directory-application-proxy-enable.md)第一个，然后发布你的应用程序。

> [!TIP]
> 如果您要测试应用程序代理在第一次，选择的应用程序的基于密码的身份验证设置。 应用程序代理支持其他类型的身份验证，但基于密码的应用是最容易快速启动并运行的。 

1. 在 [Azure 门户](https://portal.azure.com/)中，以管理员身份登录。
2. 选择“Azure Active Directory” > “企业应用程序” > “新建应用程序”。

  ![添加企业应用程序](./media/application-proxy-publish-azure-portal/add-app.png)

3. 选择**所有**，然后选择**本地应用程序**。  

  ![添加自己的应用程序](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. 提供有关应用程序的以下信息：

   - **名称**： 将出现在访问面板上，并且在 Azure 门户中应用程序的名称。 

   - **内部 URL**： 用来访问专用网络内部从应用程序的 URL。 可以提供后端服务器上要发布的特定路径，而服务器的其余部分则不发布。 通过这种方式，可以在同一服务器上将不同站点发布为不同应用，并为每个站点提供其自己的名称和访问规则。

     > [!TIP]
     > 如果发布路径，请确保它包含应用程序的所有必要映像、脚本和样式表。 例如，如果应用位于 https://yourapp/app 并使用位于 https://yourapp/media 的映像，则应将 https://yourapp/ 发布为路径。 此内部 URL 不一定是你的用户看到的登录页面。 有关详细信息，请参阅[设置已发布的应用在自定义主页上](application-proxy-office365-app-launcher.md)。

   - **外部 URL**：用户从网络外部访问应用时需使用的地址。 如果你不想使用默认应用程序代理域，请阅读有关[Azure AD 应用程序代理中的自定义域](active-directory-application-proxy-custom-domains.md)。
   - **预先身份验证**：应用程序代理如何在向用户提供对应用程序的访问权限之前验证用户。 

     - Azure Active Directory：应用程序代理会重定向用户以便使用 Azure AD 登录，这会对他们针对目录和应用程序的权限进行身份验证。 我们建议保持为默认值，此选项，以便你可以充分利用 Azure AD 安全功能，如条件性访问和多因素身份验证。
     - 直通：用户不必向 Azure Active Directory 进行身份验证即可访问应用程序。 仍可在后端设置身份验证要求。
   - **连接器组**：连接器处理对应用程序的远程访问，连接器组用于按区域、网络或用途组织连接器和应用。 如果你没有任何尚未创建的连接器组，你的应用程序分配给**默认**。

   ![配置应用程序](./media/application-proxy-publish-azure-portal/configure-app.png)
5. 如有必要，配置其他设置。 对于大多数应用程序，你应该在其默认状态保留这些设置。 
   - **后端应用程序超时**： 将此值设置为**长**仅当你的应用程序为身份验证和连接速度变慢。 
   - **转换标头中的 Url**： 保留此值为**是**除非你的应用程序所需身份验证请求中的原始主机标头。
   - **转换中应用程序正文的 Url**： 保留此值为**否**除非具有硬编码 HTML 链接到其他本地应用程序中，并且不使用自定义域。 有关详细信息，请参阅[链接使用应用程序代理的翻译](application-proxy-link-translation.md)。
   
   ![配置应用程序](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. 选择“添加”。


## <a name="add-a-test-user"></a>添加测试用户 

若要测试已正确发布你的应用，请添加一个测试用户帐户。 验证此帐户已经有权访问该应用程序从公司网络内部。

1. 回到“快速启动”边栏选项卡，选择“分配用于测试的用户”。

  ![分配用于测试的用户](./media/application-proxy-publish-azure-portal/assign-user.png)

2. 在“用户和组”边栏选项卡中，选择“添加”。

  ![添加用户或组](./media/application-proxy-publish-azure-portal/add-user.png)

3. 在“添加分配”边栏选项卡上选择“用户和组”，并选择要添加的帐户。 
4. 选择“分配”。

## <a name="test-your-published-app"></a>测试已发布的应用

在浏览器中，导航到在发布步骤中配置的外部 URL。 此时会看到开始屏幕，并能够使用已设置的测试帐户登录。

![测试已发布的应用](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>后续步骤
- [下载连接器](active-directory-application-proxy-enable.md)并[创建连接器组](active-directory-application-proxy-connectors-azure-portal.md)，以便将应用程序发布到单独的网络和位置。

- [设置单一登录](application-proxy-sso-azure-portal.md)为新发布应用程序
