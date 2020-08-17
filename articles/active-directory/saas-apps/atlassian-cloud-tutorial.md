---
title: 教程：Azure Active Directory 与 Atlassian Cloud 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Atlassian Cloud 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/04/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcb3522898a40dc79e8465af813633015568f1c8
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2020
ms.locfileid: "88033666"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>教程：将 Atlassian Cloud 与 Azure Active Directory 集成

本教程介绍如何将 Atlassian Cloud 与 Azure Active Directory (Azure AD) 集成。 将 Atlassian Cloud 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权限访问 Atlassian Cloud。
* 让用户使用其 Azure AD 帐户自动登录到 Atlassian Cloud。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月免费试用版。
* 已启用 Atlassian Cloud 单一登录 (SSO) 的订阅。
* 若要对 Atlassian Cloud 产品启用安全断言标记语言 (SAML) 单一登录，需要设置 Atlassian Access。 详细了解 [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager)。

> [!NOTE]
> 此集成也可以通过 Azure AD 美国国家云环境使用。 你可以在“Azure AD 美国国家云应用程序库”中找到此应用程序，并以与在公有云中相同的方式对其进行配置。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 

* Atlassian Cloud 支持 SP 和 IDP 发起的 SSO 
* Atlassian Cloud 支持[自动用户预配和取消预配](atlassian-cloud-provisioning-tutorial.md)
* 配置 Atlassian Cloud 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-atlassian-cloud-from-the-gallery"></a>从库中添加 Atlassian Cloud

要配置 Atlassian Cloud 与 Azure AD 的集成，需要从库中将 Atlassian Cloud 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Atlassian Cloud”   。
1. 从结果面板中选择“Atlassian Cloud”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO

使用名为 B.Simon 的测试用户配置和测试 Atlassian Cloud 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Atlassian Cloud 相关用户之间建立链接关系。

若要配置和测试 Atlassian Cloud 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Atlassian Cloud 的 Azure AD SSO](#configure-azure-ad-sso)** - 以使用户能够将基于 Azure AD 的 SAML SSO 用于 Atlassian Cloud。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[创建 Atlassian Cloud 测试用户](#create-atlassian-cloud-test-user)** - 在 Atlassian Cloud 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在开始之前，请先转到 Atlassian 产品实例并复制/保存实例 URL
   > [!NOTE]
   > URL 应为 `https://<instancename>.atlassian.net` 模式

   ![图像](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. 打开 [Atlassian 管理门户](https://admin.atlassian.com/)并单击你的组织名称

   ![图像](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. 在打算配置单一登录前，需要对你的域进行验证。 有关详细信息，请参阅 [Atlassian 域验证](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)文档。
1. 从 Atlassian 管理门户屏幕上，选择左侧抽屉中的“安全性”

   ![图像](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. 从 Atlassian 管理门户安全性屏幕上，选择左侧抽屉中的“SAML 单一登录”

   ![图像](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. 单击“添加 SAML 配置”并将该页面保持为打开状态

   ![图像](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![图像](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. 在 [Azure 门户](https://portal.azure.com/)的“Atlassian Cloud”应用程序集成页上，找到“管理”部分，选择“设置单一登录”  。

   ![图像](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. 在“选择单一登录方法”页上选择“SAML” 。

   ![图像](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. 在“设置 SAML 单一登录”页上，向下滚动到“设置 Atlassian Cloud” 
   
   a. 单击“配置 URL”

   ![图像](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. 从 Azure 门户复制“Azure AD 标识符”值，将其粘贴到 Atlassian 中的“标识提供者实体 ID”文本框中 
   
   c. 从 Azure 门户复制“登录 URL”值，将其粘贴到 Atlassian 中的“标识提供者 SSO URL”文本框中 

   ![图像](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![image](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

   ![图像](./media/atlassian-cloud-tutorial/certificate.png)

   ![图像](./media/atlassian-cloud-tutorial/certificate-1.png)

1. 在 Atlassian 中添加/保存 SAML 配置

1. 如果要在 IDP 发起的模式下配置应用程序，请在 Azure 中编辑“设置 SAML 单一登录”页中的“基本 SAML 配置”部分，并在 Atlassian 管理门户中打开“SAML 单一登录页”   

   a. 从 Atlassian 复制“SP 实体 ID”值，将其粘贴到 Azure 中的“标识符(实体 ID)”框中，并将其设置为默认值 
   
   b. 从 Atlassian 复制“SP 断言使用者服务 URL”值，将其粘贴到 Azure 中的“回复 URL (断言使用者服务 URL)”框中，并将其设置为默认值 
   
   c. 复制在步骤 1 中复制的“实例 URL”值，并将其粘贴到 Azure 中的“中继状态”框 

   ![图像](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![图像](./media/atlassian-cloud-tutorial/edit-button.png)

   ![图像](./media/atlassian-cloud-tutorial/urls.png)
   
1. 如果要在 SP 发起的模式下配置应用程序，请在 Azure 中编辑“设置 SAML 单一登录”页中的“基本 SAML 配置”部分  。 复制“实例 URL”（来自步骤 1），并将其粘贴到 Azure 中的“登录 URL”框 

   ![图像](./media/atlassian-cloud-tutorial/edit-button.png)

   ![图像](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. Atlassian Cloud 应用程序需要特定格式的 SAML 断言，这需要向“SAML 令牌属性”配置添加自定义属性映射。 可单击“编辑”图标来编辑属性映射。 

   ![图像](./media/atlassian-cloud-tutorial/default-attributes.png)
   
   1. 针对具有 Office 365 许可证的 Azure AD 租户的属性映射
      
      a. 单击“唯一用户标识符(名称 ID)”声明

      ![图像](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. Atlassian Cloud 需要 nameidentifier（唯一用户标识符）映射到用户的电子邮件 (user.email)  。 编辑“源属性”，并将其更改为 user.mail 。 保存对声明所做的更改。

      ![图像](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. 最终属性映射应如下所示。

      ![图像](common/default-attributes.png)
      
   1. 针对无 Office 365 许可证的 Azure AD 租户的属性映射 

      a. 单击 http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress 声明。

      ![图像](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. 尽管 Azure 不会为在 Azure AD 租户中创建的无 Office 365 许可证的用户填充 user.mail 属性，但会将此类用户的电子邮件存储在 userprincipalname 属性中 。 Atlassian Cloud 期望 nameidentifier（唯一用户标识符）映射到用户的电子邮件 (user.userprincipalname)  。  编辑“源属性”，并将其更改为 user.userprincipalname 。 保存对声明所做的更改。

      ![图像](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. 最终属性映射应如下所示。

      ![图像](common/default-attributes.png)
     
### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 B.Simon 访问 Atlassian Cloud 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Atlassian Cloud”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](./media/atlassian-cloud-tutorial/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-atlassian-cloud-test-user"></a>创建 Atlassian Cloud 测试用户

若要使 Azure AD 用户能够登录到 Atlassian Cloud，请执行以下操作，在 Atlassian Cloud 中手动预配用户帐户：

1. 在“管理”窗格中，选择“用户”。  

    ![Atlassian Cloud“用户”链接](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. 若要在 Atlassian Cloud 中创建用户，请选择“邀请用户”。 

    ![创建 Atlassian Cloud 用户](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. 在“电子邮件地址”框中输入该用户的电子邮件地址，然后分配应用程序访问权限。 

    ![创建 Atlassian Cloud 用户](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. 若要向该用户发送电子邮件邀请，请选择“邀请用户”。  随后会将一封电子邮件邀请发送给该用户，接受邀请后，该用户将在系统中激活。

> [!NOTE]
> 还可以通过选择“用户”部分中的“批量创建”按钮来批量创建用户。  

### <a name="test-sso"></a>测试 SSO

选择访问面板中的 Atlassian Cloud 磁贴时，应当会自动登录到设置了 SSO 的 Atlassian Cloud。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Atlassian Cloud](https://aad.portal.azure.com/)

- [如何通过高级可见性和控制保护 Atlassian Cloud](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)