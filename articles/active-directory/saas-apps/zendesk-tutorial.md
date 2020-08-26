---
title: 教程：Azure Active Directory 与 Zendesk 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Zendesk 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/13/2020
ms.author: jeedes
ms.openlocfilehash: c7d452803d15bab77df8e85a861de914a5ed08d5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546048"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zendesk"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Zendesk 的集成

本教程介绍如何将 Zendesk 与 Azure Active Directory (Azure AD) 集成。 将 Zendesk 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Zendesk。
* 让用户使用其 Azure AD 帐户自动登录到 Zendesk。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Zendesk 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Zendesk 支持 **SP** 发起的 SSO
* Zendesk 支持[**自动**用户预配](zendesk-provisioning-tutorial.md)
* 配置 Zendesk 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-zendesk-from-the-gallery"></a>从库中添加 Zendesk

若要配置 Zendesk 与 Azure AD 的集成，需要从库中将 Zendesk 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 Zendesk。  
1. 在结果面板中选择“Zendesk”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-zendesk"></a>为 Zendesk 配置并测试 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 Zendesk 的 Azure AD SSO。  若要使 SSO 有效，需要在 Azure AD 用户与 Zendesk 相关用户之间建立关联。

若要配置并测试 Zendesk 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Zendesk SSO](#configure-zendesk-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Zendesk 测试用户](#create-zendesk-test-user)** - 在 Zendesk 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Zendesk”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.zendesk.com`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<subdomain>.zendesk.com`

    c. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<subdomain>.zendesk.com/access/saml`

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [Zendesk 客户端支持团队](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. Zendesk 应用程序需要特定格式的 SAML 断言。 没有强制的 SAML 属性，但可以选择从应用程序集成页上的“用户属性”  部分中进行管理。 在“使用 SAML 设置单一登录”  页上，单击“编辑”  按钮以打开“用户属性”  对话框。

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > 可以使用扩展属性添加默认情况下不在 Azure AD 中的属性。 单击 [可在 SAML 中设置的用户属性](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-)，获取 Zendesk 接受的 SAML 属性的完整列表  。

1. 在“SAML 签名证书”  部分中，单击“编辑”  按钮以打开“SAML 签名证书”  对话框。

    ![编辑 SAML 签名证书](common/edit-certificate.png)

1. 在“SAML 签名证书”部分，复制“指纹值”并将其保存在计算机上。  

    ![复制指纹值](common/copy-thumbprint.png)

1. 在“设置 Zendesk”部分，根据要求复制相应的 URL。 

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

在本部分，你将通过授予 B.Simon 访问 Zendesk 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Zendesk”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-zendesk-sso"></a>配置 Zendesk SSO

1. 若要在 Zendesk 中自动执行配置，需要通过单击“安装扩展”，安装“我的应用安全登录浏览器扩展”  。

    ![图像](./media/target-process-tutorial/install_extension.png)

1. 将扩展添加到浏览器后，单击“安装 Zendesk”会将你定向到 Zendesk 应用程序。 在此处，请提供管理员凭据以登录到 Zendesk。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

1. 若要手动设置 Zendesk，请打开新的 Web 浏览器窗口，以管理员身份登录到 Zendesk 公司网站，并执行以下步骤：

1. 单击“管理”  。

1. 在左侧导航窗格中，单击“设置”  ，并单击“安全”  。

1. 在“安全”  页中，执行以下步骤：

    ![安全性](./media/zendesk-tutorial/ic773089.png "安全性")

    ![单一登录](./media/zendesk-tutorial/ic773090.png "单一登录")

    a. 单击“管理员和代理”  选项卡。

    b. 选择“单一登录 (SSO) 和 SAML”  ，并选择“SAML”  。

    c. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。  

    d. 在“远程注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。  

    e. 在“证书指纹”  文本框中，粘贴从 Azure 门户复制的证书“指纹”  值。

    f. 单击“ **保存**”。

### <a name="create-zendesk-test-user"></a>创建 Zendesk 测试用户

本部分的目的是在 Zendesk 中创建名为“Britta Simon”的用户。 Zendesk 支持在默认情况下启用的自动用户预配。 有关如何配置自动用户预配的更多详细信息，请参见[此处](Zendesk-provisioning-tutorial.md)。

如果需要手动创建用户，请执行以下步骤： 

> [!NOTE]
> 在登录时会自动预配“最终用户”帐户  。 在登录前需要在 Zendesk 中手动预配“代理”和“管理员”帐户    。

1. 登录到 Zendesk 租户。 

2. 选择“客户列表”  选项卡。

3. 选择“用户”  选项卡，并单击“添加”  。

    ![添加用户](./media/zendesk-tutorial/ic773632.png "添加用户")
4. 键入要预配的现有 Azure AD 帐户的**名称**和**电子邮件**，然后单击“保存”  。

    ![新建用户](./media/zendesk-tutorial/ic773633.png "新建用户")

> [!NOTE]
> 可以使用 Zendesk 提供的任何其他 Zendesk 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Zendesk 磁贴时，应当会自动登录到你为其设置了 SSO 的 Zendesk。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Zendesk](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用高级可见性和控制保护 Zendesk](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [配置用户预配](zendesk-provisioning-tutorial.md)