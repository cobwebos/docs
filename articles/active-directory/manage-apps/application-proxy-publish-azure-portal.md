---
title: 使用 Azure AD 应用程序代理发布应用 | Microsoft Docs
description: 在 Azure 门户中使用 Azure AD 应用程序代理将本地应用程序发布到云。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 8eb629396629a92503907439a64cca9d70747010
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594104"
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>使用 Azure AD 应用程序代理发布应用程序

Azure Active Directory (AD) 应用程序代理可发布要通过 Internet 访问的本地应用程序，帮助用户为远程辅助角色提供支持。 可通过 Azure 门户发布这些应用程序，允许用户从网络外部进行安全的远程访问。

本文演示的步骤用于通过应用程序代理发布本地应用。 阅读完本文后，用户将能够远程访问你的应用。 并且能够配置单一登录、个性化信息和安全要求等应用程序的其他功能。

如果不熟悉应用程序代理，可通过[如何提供对本地应用程序的安全远程访问](application-proxy.md)一文详细了解此功能。

## <a name="before-you-begin"></a>开始之前

本文假定你已安装并注册连接器。 如果仍需执行这些步骤，请参阅[开始使用应用程序代理并安装连接器](application-proxy-enable.md)。

## <a name="publish-an-on-premises-app-for-remote-access"></a>发布进行远程访问的本地应用

请按照下列步骤使用应用程序代理发布应用。 如果尚未为组织下载并配置连接器，请先转到[开始使用应用程序代理并安装连接器](application-proxy-enable.md)，然后发布应用。

> [!TIP]
> 若是首次使用应用程序代理，请选择一个已设置基于密码的身份验证的应用程序。 应用程序代理支持其他类型的身份验证，但基于密码的应用是最容易快速启动并运行的。 

1. 在 [Azure 门户](https://portal.azure.com/)中，以管理员身份登录。
2. 选择“Azure Active Directory” > “企业应用程序” > “新建应用程序”。

  ![添加企业应用程序](./media/application-proxy-publish-azure-portal/add-app.png)

3. 选择“全部”，然后选择“本地应用程序”。  

  ![添加自己的应用程序](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. 提供有关应用程序的以下信息：

   - 名称：要显示在访问面板上以及 Azure 门户中的应用程序名称。 

   - 内部 URL：用于从专用网络内部访问应用程序的 URL。 可以提供后端服务器上要发布的特定路径，而服务器的其余部分则不发布。 通过这种方式，可以在同一服务器上将不同站点发布为不同应用，并为每个站点提供其自己的名称和访问规则。

     > [!TIP]
     > 如果发布路径，请确保它包含应用程序的所有必要映像、脚本和样式表。 例如，如果你的应用位于 https://yourapp/app，并使用位于 https://yourapp/media 的映像，则应发布 https://yourapp/ 作为路径。 此内部 URL 不一定是用户看到的登陆页面。 有关详细信息，请参阅[为发布的应用设置自定义主页](application-proxy-configure-custom-home-page.md)。

   - **外部 URL**：用户从网络外部访问应用时需使用的地址。 如果不想使用默认的应用程序代理域，请参阅 [Azure AD 应用程序代理中的自定义域](application-proxy-configure-custom-domain.md)。
   - **预先身份验证**：应用程序代理如何在向用户提供对应用程序的访问权限之前验证用户。 

     - Azure Active Directory：应用程序代理会重定向用户以便使用 Azure AD 登录，这会对他们针对目录和应用程序的权限进行身份验证。 建议将此选项保留为默认值，以便可以利用条件性访问和多重身份验证等 Azure AD 安全功能。
     - 直通：用户不必向 Azure Active Directory 进行身份验证即可访问应用程序。 仍可在后端设置身份验证要求。
   - **连接器组**：连接器处理对应用程序的远程访问，连接器组用于按区域、网络或用途组织连接器和应用。 如果尚未创建任何连接器组，应用将分配到“默认”。

>[!NOTE]
>如果应用程序使用 websocket 进行连接，请确保连接器版本为 1.5.612.0 或更高版本且支持 websocket，并且分配的连接器组仅使用这些连接器。

   ![配置应用程序](./media/application-proxy-publish-azure-portal/configure-app.png)
5. 如有必要，请配置其他设置。 对于大多数应用程序，应保留这些设置的默认状态。 
   - 后端应用程序超时：仅当应用程序身份验证和连接速度较慢时，才将此值设为“长”。 
   - 转换标头中的 URL：除非应用程序要求身份验证请求中的原始主机标头，否则请将此值保留为“是”。
   - 转换应用程序正文中的 URL：除非具有指向其他本地应用程序的硬编码 HTML 链接，且不使用自定义域，否则请将此值保留为“否”。 有关详细信息，请参阅[使用应用程序代理进行链接转换](application-proxy-configure-hard-coded-link-translation.md)。
   
   ![配置应用程序](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. 选择 **添加** 。


## <a name="add-a-test-user"></a>添加测试用户 

若要测试应用是否已正确发布，请添加测试用户帐户。 验证此帐户是否已有权从企业网络内部访问该应用。

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
- [下载连接器](application-proxy-enable.md)并[创建连接器组](application-proxy-connector-groups.md)，以便将应用程序发布到单独的网络和位置。

- 为新发布的应用[设置单一登录](application-proxy-configure-single-sign-on-password-vaulting.md)
