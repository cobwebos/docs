---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 SignalFx 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SignalFx 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc8ba01ea8a443c4f8a3a8e7b911dcc605ee61ea
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967696"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>教程：Azure Active Directory 单一登录 (SSO) 与 SignalFx 集成

本教程介绍如何将 SignalFx 与 Azure Active Directory (Azure AD) 集成。 将 SignalFx 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 SignalFx；
* 让用户可以使用其 Azure AD 帐户自动登录到 SignalFx；
* 在一个位置（Azure 门户）管理帐户。

若要详细了解有关 SaaS 应用程序与 Azure AD 的集成，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

在开始之前，需要：

* Azure AD 订阅
    * 如果没有订阅，可[在此处获取免费帐户](https://azure.microsoft.com/free/)。
* 已启用 SignalFx 单一登录 (SSO) 的订阅

## <a name="scenario-description"></a>方案描述

本教程将在测试环境中配置并测试 Azure AD SSO。

* SignalFx 支持 **IDP** 发起的 SSO
* SignalFx 支持**实时**用户预配
* 配置 SignalFx 后，可以强制实施会话控制，从而实时保护组织的敏感数据，使其免遭外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="step-1-add-the-signalfx-application-in-azure"></a>步骤 1：在 Azure 中添加 SignalFx 应用程序

按照以下说明操作，以便将 SignalFx 应用程序添加到托管 SaaS 应用列表。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧的导航窗口中，选择“Azure Active Directory”。 
1. 选择“企业应用程序”，然后选择“所有应用程序”。  
1. 选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，输入并选择“SignalFx”。  
    * 该应用程序可能需要几分钟的时间才会添加到租户，请稍等片刻。
1. 将 Azure 门户保持打开状态，然后打开新的 Web 选项卡。    

## <a name="step-2-begin-signalfx-sso-configuration"></a>步骤 2：开始 SignalFx SSO 配置

按照以下说明操作，以便开始 SignalFx SSO 的配置过程。

1. 在新打开的选项卡中，访问并登录到 SignalFx UI。 
1. 在顶部菜单中，单击“集成”。  
1. 在搜索字段中，输入并选择“Azure Active Directory”。 
1. 单击“创建新的集成”。 
1. 在“名称”中，输入用户可以理解的且易于辨识的名称。 
1. 勾选“在登录页上显示”。 
    * 此功能将在登录页中显示一个可让用户单击的自定义按钮。 
    * 在“名称”中输入的信息将显示在该按钮上。  因此，请输入用户可以识别的**名称**。 
    * 仅当对 SignalFx 应用程序使用自定义子域（例如 **yourcompanyname.signalfx.com**）时，此选项才起作用。 若要获取自定义子域，请联系 SignalFx 支持人员。 
1. 复制“集成 ID”。  稍后的步骤需要用到此信息。 
1. 将 SignalFx UI 保持打开状态。 

## <a name="step-3-configure-azure-ad-sso"></a>步骤 3：配置 Azure AD SSO

按照以下说明操作，以便在 Azure 门户中启用 Azure AD SSO。

1. 返回 [Azure 门户](https://portal.azure.com/)，在“SignalFx”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”旁边的铅笔（编辑）图标以编辑设置。  

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“设置 SAML 单一登录”页上填写以下字段：  

    a. 在“标识符”中输入以下 URL：`https://api.<realm>.signalfx.com/v1/saml/metadata`（请将 `<realm>` 替换为你的 SignalFx 领域）。  

    b. 在“回复 URL”中输入以下 URL：`https://api.<realm>.signalfx.com/v1/saml/acs/<integration ID>`（请将 `<realm>` 替换为你的 SignalFx 领域，并将 `<integration ID>` 替换为先前从 SignalFx UI 复制的“集成 ID”）。  

1. SignalFx 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 
    
1. 查看并验证以下声明是否映射到 Active Directory 中填充的源属性。 

    | 名称 |  源属性|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | User.email  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

    > [!NOTE]
    > 此过程要求你的 Active Directory 至少配置了一个已验证的自定义域，并有权访问此域中的电子邮件帐户。 如果你不确定如何完成此配置或需要帮助，请联系 SignalFx 支持人员。  

1. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，找到“证书(Base64)”并选择“下载”。     下载证书，并将其保存到计算机上。 然后复制“应用联合元数据 URL”值；稍后在 SignalFx UI 中执行步骤时需要用到此信息。  

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 SignalFx”部分，复制“Azure AD 标识符”值。   稍后在 SignalFx UI 中执行步骤时需要用到此信息。 

## <a name="step-4-create-an-azure-ad-test-user"></a>步骤 4：创建 Azure AD 测试用户

按照以下说明操作，以便在 Azure 门户中创建名为 **B.Simon** 的测试用户。

1. 在 Azure 门户的左侧导航窗口中，依次选择“Azure Active Directory”、“用户”、“所有用户”。   
1. 选择页面顶部的“新建用户”。 
1. 在“用户”属性中： 
   1. 在“用户名”中输入 `username@companydomain.extension`，例如 `b.simon@contoso.com`。 
   1. 在“名称”  中，请输入 `B.Simon`。
   1. 勾选“显示密码”，然后复制“密码”中显示的值。   在后续步骤中需要使用此信息来测试此集成。 
   1. 单击“创建”。 

## <a name="step-5-assign-the-azure-ad-test-user"></a>步骤 5：分配 Azure AD 测试用户

按照以下说明操作，以使测试用户能够对 SignalFx 使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“SignalFx”  。
1. 在应用的概述页中，找到“管理”部分，然后选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中的“用户”列表内选择“B.Simon”，然后单击页面底部的“选择”。    
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框中的列表内为用户选择相应的角色，然后单击页面底部的“选择”。  
1. 在“添加分配”对话框中，单击“分配”。  

## <a name="step-6-complete-the-signalfx-sso-configuration"></a>步骤 6：完成 SignalFx SSO 配置 

1. 打开上一个选项卡，返回到 SignalFx UI 查看当前的 Azure Active Directory 集成页。 
1. 在“证书(Base64)”旁边，单击“上传文件”，然后找到前面从 Azure 门户下载的 **Base64 编码证书**文件。  
1. 在“Azure AD 标识符”旁边，粘贴前面从 Azure 门户复制的“Azure AD 标识符”值。   
1. 在“联合元数据 URL”旁边，粘贴前面从 Azure 门户复制的“应用联合元数据 URL”值。   
1. 单击“ **保存**”。

## <a name="step-7-test-sso"></a>步骤 7：测试 SSO

查看以下有关如何测试 SSO 的信息，以及首次登录到 SignalFx 时预期会发生的情况。 

### <a name="test-logins"></a>测试登录

* 若要测试登录，应使用私密/隐匿窗口，或者可以从 Azure 门户注销。 否则，配置该应用程序的用户的 Cookie 将会干扰并阻止以测试用户的身份成功登录。

* 当新的测试用户首次登录时，Azure 会强制要求更改密码。 如果出现这种情况，SSO 登录过程将不会完成；测试用户会被引导到 Azure 门户。 若要进行故障排除，测试用户应更改其密码，然后导航到 SignalFx 登录页或访问面板并重试。
    * 在访问面板中单击“SignalFx”磁贴时，应会自动登录到 SignalFx。 
        * 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

* 可以通过访问面板或已分配到组织的自定义登录页访问 SignalFx 应用程序。 测试用户应从上述任一位置开始测试集成。
    * 测试用户可以使用前面在此过程中为 **b.simon@contoso.com** 创建的凭据。

### <a name="first-time-logins"></a>首次登录

* 当某个用户首次通过 SAML SSO 登录到 SignalFx 时，该用户会收到一封包含链接的 SignalFx 电子邮件。 该用户必须单击此链接才能完成身份验证。 此电子邮件验证仅在用户首次登录时才会发生。 

* SignalFx 支持**即时**用户创建，这意味着，如果 SignalFx 中不存在某个用户，则首次尝试登录时会创建该用户的帐户。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [尝试结合使用 SignalFx 和 Azure AD](https://aad.portal.azure.com/)