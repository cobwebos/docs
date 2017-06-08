---
title: "Azure Active Directory 中企业应用程序管理的新增功能 | Microsoft 文档"
description: "了解 Azure Active Directory 中企业应用程序管理的新增功能。"
services: active-directory
documentationcenter: 
author: asteen
manager: femila
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: asteen
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 36de09434ae5082e5f92dcc9a82bba88f5b89c85
ms.contentlocale: zh-cn
ms.lasthandoff: 05/08/2017


---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Azure Active Directory 中企业应用程序管理的新增功能 

Azure Active Directory (Azure AD) 具有增强的企业应用程序管理工具，其新功能可使管理应用更简单和更高效。

下面是 [Azure 门户](https://portal.azure.com)中的 Azure AD 的一些增强功能。

- 改进了应用程序库体验，提供简化的应用程序创建模型，支持你惯于使用的所有应用程序类型。 
- 全新的快速入门体验，可帮助用户开始试运行应用程序。 
- 只需单击几下鼠标，即可配置自助服务策略。 
- 对应用程序代理、单一登录配置和引入自己的应用程序体验做出改进，让用户可以比以前完成更多任务。

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>对 Azure Active Directory 应用程序库做出改进

添加常用的应用程序，无论这些应用程序是来自[应用程序库](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)的应用程序、正在扩展到云中的自定义应用程序，还是正在开发的新应用程序。  在“企业应用程序”概述边栏选项卡或“所有应用程序”边栏选项卡上单击“添加”，即可开始使用这种新体验。
 
  ![添加应用程序](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

进入库后，首先会看到所有支持用户预配的特色应用程序。  可以浏览各种不同的类别，深入到你所关注的应用程序，或者使用搜索体验快速找到想要集成的应用程序。

  ![应用程序库](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>从一个位置添加自定义应用程序

除了添加库中预先集成的应用程序以外，在新门户中还可以使用过去常常在经典管理门户中所用的自定义应用程序配置体验。 不管是要使用应用程序代理在本地扩展应用程序、集成自己的密码或联合 SSO 应用程序，还是使用应用程序注册表创建全新的应用程序，都可以从这一个位置实现这些目的。

  ![添加自己的应用程序](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**开始添加自己的应用程序**：

1. 单击应用程序库顶部的“添加自己的链接”。 
2. 此时将出现两个选项：“部署现有应用程序”或“开发新应用程序”。 请继续阅读，了解两个选项的差别及用法。

### <a name="deploying-existing-applications"></a>部署现有应用程序

1. 如果已运行某个应用程序并想要将其集成到 Azure AD 进行单一登录或预配，请选择“部署现有应用程序”选项。 为应用程序选择一个名称，然后单击“添加”。
2. 就这么简单！ 现在，无需事先了解应用程序的所有详细信息，随时可以在左侧菜单中导航并根据需要配置应用程序，设置新应用程序的工作方式。

  ![单击一下鼠标即可添加现有应用程序](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>开发新应用程序

1. 若要开发新应用程序，可以轻松地从库直接转到应用程序注册表：
2. 在应用程序库中单击“添加自己的应用程序”，选择“开发现有应用程序”选项，然后，应用程序添加体验的右侧会显示一个快速链接。

  ![单击几下鼠标即可添加新开发的应用程序](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>使用应用程序注册表添加应用程序后，该应用程序将显示在“企业应用程序”列表中，可在其中配置单一登录，管理新应用程序的访问策略。

  ![管理对企业应用程序下的新应用程序的访问](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quick-start-get-going-with-your-new-application-right-away"></a>快速启动：立即开始使用新应用程序 

添加应用程序后（无论是预先集成的应用程序还是你自己的应用程序），可以使用我们创建的定制快速启动体验，快速着手新应用程序的体验。 当你有条不紊地遵循每个选项时，我们将引导你完成 UI，并演示如何尽快开始试运行新应用程序。 
 
  ![新应用程序快速启动体验](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 在应用程序的左侧导航菜单中单击“快速启动”，随时可以针对任何应用程序访问这个新的快速启动体验。


## <a name="updated-application-proxy-configuration"></a>更新的应用程序代理配置
假设你在本地环境中运行了一个添加的新应用程序，并想要将它与 Azure AD 集成。  在新的 Azure AD 门户中，有关新应用程序配置体验的一个很棒的新功能是，通过从应用程序代理配置中拆分应用程序登录模式，可以直接向云公开企业网络中运行的密码 SSO 或联合应用程序，而无需创建多个应用程序实例。

除此之外，现在还可以通过新门户配置添加的用于 Azure AD 应用程序代理的任何新应用程序，包括支持本机 Windows 身份验证体验的应用程序。

  ![将应用程序配置为使用 Windows 集成身份验证登录选项](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 

若要开始使用应用程序代理配置本机 Windows 身份验证应用程序：
1. 单击“单一登录”导航项，然后在登录设置边栏选项卡中选择“Windows 集成身份验证”并根据需要配置设置。
2. 由于支持这些新的身份验证模式，现在还可以从自定义域上载证书，为组织中的安全终结点上运行的应用程序提供支持。  
 
   ![上载用于应用程序代理的证书](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

3. 若要为喜好的本地应用程序上载新证书，请在左侧导航菜单中单击“应用程序代理”选项，单击“证书”选择器，然后上载一个证书文件用于加密从云终结点发往应用程序的请求。

## <a name="advanced-federated-single-sign-on-configuration"></a>高级联合单一登录配置

针对使用联合应用程序的用户，基于 SAML 的登录配置边栏选项卡中提供了许多新功能。 现在，可以完全自定义、添加、删除和映射在 SAML 令牌中作为声明颁发的现有用户属性。
 
  ![自定义传递给联合应用程序的 SAML 令牌用户属性](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)


启用新的联合 SSO 配置：
1. 在左侧导航菜单中打开联合应用程序的“单一登录”边栏选项卡，并确保“基于 SAML 的登录”模式处于选中状态。 
2. 然后，启用“用户属性”标题下面的复选框，修改传递给该应用程序的 SAML 令牌中包含的所有属性。

还可以创建、滚动更新和管理用于联合单一登录的证书，以及编辑证书即将过期时收到通知的人员。 在同一个“单一登录”边栏选项卡上的“证书”标题下面可以看到这些新选项。
 
  ![创建新证书、自定义过期通知电子邮件和证书签名选项](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-paramenter"></a>中继状态参数
最后，我们还扩展了支持的 SAML URL 参数集以包含**中继状态参数**，即，用户在联合应用程序内部完成登录后看到的第一个页面。 如果希望将用户定向到应用程序中的特定位置，使其快速开始使用应用程序，则配置此项设置将十分有用。

  ![设置 SAML 中继状态参数](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**设置中继状态参数**：

1. 启用单一登录配置边栏选项卡上“域和 URL”标题下面的“显示高级 URL 设置”复选框。 
2. 执行此操作后，将显示一组新的 URL 输入框，用于指定此项设置以及其他 SAML URL 设置。

## <a name="bring-your-own-password-sso-applications"></a>引入自己的密码 SSO 应用程序

我们知道，并不是每个应用程序都现成地支持联合身份验证。 例如，可能添加的某个新应用程序提供自定义的登录屏幕，让用户使用用户名和密码登录。 仍可以使用“引入自己的应用程序”功能将此类应用程序与 Azure AD 集成，现在，可在新门户中配置此功能。
 
  ![将自定义的密码保管应用程序与 Azure AD 集成](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**启用“引入自己的应用程序”功能**：

1. 为添加到“基于密码的登录”的新自定义应用程序设置单一登录模式后，请输入应用程序显示其登录屏幕时所用的 URL，然后单击“保存”。  
2. 执行该操作后，系统将自动擦除该 URL 并显示用户名和密码输入框，允许使用 Azure AD 通过访问面板浏览器扩展安全地将密码传输到该应用程序。

## <a name="configure-self-service-application-access"></a>配置自助应用程序访问

添加大量新应用程序后，你可能想要允许用户浏览这些应用程序并将其添加到自己的访问面板，而无需你（管理员）的干预。 在此最新版本中，可以直接从新门户配置和管理自助应用程序访问。

  ![为密码 SSO 应用程序配置自助应用程序访问](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**配置和管理自助应用程序访问**：

1. 若要开始，可以在应用程序的左侧导航菜单中选择“自助服务”选项，并将“允许用户请求访问此应用程序?”选项设为“是”。 
2. 这样，便可以配置允许谁批准访问此应用程序，以及要添加哪个自助服务用户组。 此外，如果为密码单一登录配置了应用程序，还会看到另一个选项，用于根据需要允许这些审批人管理分配给应用程序的密码。

##<a name="feedback"></a>反馈

我们希望你喜欢使用改进的 Azure AD 体验。 敬请不时地向我们发送反馈！ 欢迎在[反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal)的**管理员门户**版块中发表有关改进的意见和想法。  我们热衷于不断推出炫酷的新功能，遵从用户的指导来塑造和定义后续版本。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Managing Applications with Azure Active Directory](active-directory-enable-sso-scenario.md)（使用 Azure Active Directory 管理应用程序）。




