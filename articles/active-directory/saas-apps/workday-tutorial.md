---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Workday 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Workday 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83fd986e31410e34871d1b795db0c8c83ece688e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648617"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Workday 集成

本教程介绍如何将 Workday 与 Azure Active Directory (Azure AD) 集成。 将 Workday 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Workday。
* 让用户使用其 Azure AD 帐户自动登录到 Workday。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 Workday 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Workday 支持 **SP** 发起的 SSO。

* 配置 Workday 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>从库中添加 Workday

若要配置 Workday 与 Azure AD 的集成，需要从库中将 Workday 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Workday”   。
1. 从结果面板中选择“Workday”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>配置和测试 Workday 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 Workday 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Workday 相关用户之间建立链接关系。

若要配置和测试 Workday 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B.Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
2. **[配置 Workday](#configure-workday)** ，以在应用程序端配置 SSO 设置。
    1. **[创建 Workday 测试用户](#create-workday-test-user)** - 在 Workday 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Workday”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，输入以下字段的值  ：

    a. 在“登录 URL”  文本框中，使用以下模式键入 URL：`https://impl.workday.com/<tenant>/login-saml2.flex`。

    b. 在“标识符”  文本框中，使用以下模式键入 URL：`http://www.workday.com`

    c. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的登录 URL 和回复 URL 更新这些值。 回复 URL 必须具有一个子域（例如：www、wd2、wd3、wd3-impl、wd5 和 wd5-impl）。
    > 可以使用类似于 `http://www.myworkday.com` 的内容，但不能使用 `http://myworkday.com`。 请联系 [Workday 客户端支持团队](https://www.workday.com/en-us/partners-services/services/support.html)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

6. Workday 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 Workday 应用程序要求将 **nameidentifier** 与 **user.mail**、**UPN** 等进行映射，因此需单击“编辑”图标对属性映射进行编辑，然后更改属性映射  。

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > 此处我们已将名称 ID 与 UPN (user.userprincipalname) 映射为默认值。 需要在 Workday 帐户（电子邮件、UPN 等）中将名称 ID 与实际用户 ID 进行映射，以便成功运行 SSO。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

   ![证书下载链接](common/certificatebase64.png)

1. 若要根据要求修改“签名”选项，请单击“编辑”按钮，打开“SAML 签名证书”对话框    。

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. 选择“签名 SAML 响应和断言”作为“签名选项”   。

    b. 单击“保存” 

1. 在“设置 Workday”部分，根据要求复制相应的 URL  。

   ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Workday 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Workday”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-workday"></a>配置 Workday

1. 在另一 Web 浏览器窗口中，以管理员身份登录 Workday 公司站点。

2. 在主页左上方的“搜索框”  中使用名称“编辑租户设置 - 安全性”  搜索。

    ![编辑租户安全](./media/workday-tutorial/IC782925.png "编辑租户安全")

3. 在“重定向 URL”  部分中，执行以下步骤：

    ![重定向 URL](./media/workday-tutorial/IC7829581.png "重定向 URL")

    a. 单击“添加行”  。

    b. 在“登录重定向 URL”、“超时重定向 URL”和“移动重定向 URL”文本框中，粘贴从 Azure 门户的“设置 Workday”部分中复制的“登录 URL”      。

    c. 在“注销重定向 URL”文本框中，粘贴从 Azure 门户的“设置 Workday”部分中复制的“注销 URL”    。

    d. 在“用于环境”  文本框中，选择环境名称。  

   > [!NOTE]
   > “环境”属性的值与租户 URL 的值绑定：  
   > -如果 Workday 租户 URL 的域名以 impl 开头（例如： https://www.myworkday.com/"tenant"/login-saml2.htmld  ），则“环境”  属性必须设置为“实现”。  
   > 如果域名以其他内容开头，则需要联系 [Workday 客户端支持团队](https://www.workday.com/en-us/partners-services/services/support.html)，获取匹配的“环境”值  。

4. 在“SAML 设置”  部分中执行以下步骤：

    ![SAML 设置](./media/workday-tutorial/IC782926.png "SAML 设置")

    a.  选择“启用 SAML 身份验证”  。

    b.  单击“添加行”  。

5. 在“SAML 标识提供者”  部分中，执行以下步骤：

    ![SAML 标识提供者](./media/workday-tutorial/IC7829271.png "SAML 标识提供者")

    a. 在“标识提供者名称”  文本框中，键入提供者名称（例如：SPInitiatedSSO  ）。

    b. 在 Azure 门户的“设置 Workday”部分复制“Azure AD 标识符”值，然后将其粘贴到“颁发者”文本框中    。

    ![SAML 标识提供者](./media/workday-tutorial/IC7829272.png "SAML 标识提供者")

    c. 在 Azure 门户的“设置 Workday”部分复制“注销 URL”值，然后将其粘贴到“注销响应 URL”文本框中    。

    d. 在 Azure 门户的“设置 Workday”部分复制“登录 URL”值，然后将其粘贴到“IdP SSO 服务 URL”文本框中    。

    e. 在“用于环境”  文本框中，选择环境名称。

    f. 单击“标识提供者公钥证书”  ，并单击“创建”  。

    ![创建](./media/workday-tutorial/IC782928.png "创建")

    g. 单击“创建 x509 公钥”  。

    ![创建](./media/workday-tutorial/IC782929.png "创建")

6. 在“查看 x509 公钥”  部分中，执行以下步骤：

    ![查看 x509 公钥](./media/workday-tutorial/IC782930.png "查看 x509 公钥")

    a. 在“名称”  文本框中，键入证书名称（例如：PPE\_SP  ）。

    b. 在**有效起始日期**文本框中，键入证书的“有效起始日期”属性值。

    c.  在**有效截止日期**文本框中，键入证书的“有效截止日期”属性值。

    > [!NOTE]
    > 可以双击下载的证书，以从中获取有效起始日期和有效截止日期。  该日期在“详细信息”  选项卡下列出。
    >
    >

    d.  在记事本中打开 Base-64 编码的证书，并复制其内容。

    e.  在“证书”  文本框中，粘贴剪贴板的内容。

    f.  单击“确定”。 

7. 执行以下步骤：

    ![SSO 配置](./media/workday-tutorial/WorkdaySSOConfiguratio.png "SSO 配置")

    a.  在“服务提供商 ID”  文本框中，键入 **http://www.workday.com** 。

    b. 选择“不削弱 SP 发起的身份验证请求”  。

    c. 对于**身份验证请求签名方法**，请选择 **SHA256**。

    ![身份验证请求签名方法](./media/workday-tutorial/WorkdaySSOConfiguration.png "身份验证请求签名方法")

    d. 单击“确定”。 

    ![确定](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > 请确保正确设置单一登录。 如果使用不正确的设置启用单一登录，则可能无法使用凭据进入应用程序并被锁定。在这种情况下，Workday 提供备份登录 URL，用户可以使用以下格式的普通用户名和密码登录：[Workday URL]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>创建 Workday 测试用户

在本部分中，将在 Workday 中创建名为 B.Simon 的用户。 请与 [Workday 客户端支持团队](https://www.workday.com/en-us/partners-services/services/support.html)协作来在 Workday 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在访问面板中选择“Workday”磁贴时，应会自动登录到设置了 SSO 的 Workday。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Workday](https://aad.portal.azure.com)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Workday](https://docs.microsoft.com/cloud-app-security/protect-workday)