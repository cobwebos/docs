---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Netskope 用户身份验证的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Netskope 用户身份验证之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e9bb71cd-aaf9-4403-aca5-c5a349ec562a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e33af932e405552cf9d8f5aaf6d42cbd095607b0
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085010"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-user-authentication"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Netskope 用户身份验证的集成

本教程介绍如何将 Netskope 用户身份验证与 Azure Active Directory (Azure AD) 集成。 将 Netskope 用户身份验证与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Netskope 用户身份验证。
* 可让用户使用其 Azure AD 帐户自动登录到 Netskope 用户身份验证。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Netskope 用户身份验证单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Netskope 用户身份验证支持 SP 和 IDP 发起的 SSO 

## <a name="adding-netskope-user-authentication-from-the-gallery"></a>从库中添加 Netskope 用户身份验证

若要配置 Netskope 用户身份验证与 Azure AD 的集成，需从库中将 Netskope 用户身份验证添加到托管 SaaS 应用程序列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Netskope 用户身份验证”   。
1. 从结果面板中选择“Netskope 用户身份验证”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-user-authentication"></a>配置和测试 Netskope 用户身份验证的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置并测试 Netskope 用户身份验证的 Azure AD SSO  。 若要正常使用 SSO，需要在 Azure AD 用户与 Netskope 用户身份验证中的相关用户之间建立链接关系。

若要配置并测试 Netskope 用户身份验证的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Netskope 用户身份验证 SSO](#configure-netskope-user-authentication-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Netskope 用户身份验证测试用户](#create-netskope-user-authentication-test-user)** - 在 Netskope 用户身份验证中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Netskope 用户身份验证”应用程序集成页上，找到“管理”部分，然后选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<tenantname>.goskope.com/<customer entered string>`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<tenantname>.goskope.com/nsauth/saml2/http-post/<customer entered string>`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 本教程后面的步骤中将介绍这些值。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<tenantname>.goskope.com`

    > [!NOTE]
    > “登录 URL”值不是实际值。 请使用实际登录 URL 更新登录 URL 值。 请联系 [Netskope 用户身份验证客户端支持团队](mailto:support@netskope.com)以获取登录 URL 值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分，单击“下载”以根据要求下载从给定选项提供的“联合元数据 XML”并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Netskope 用户身份验证”部分，根据要求复制相应的 URL  。

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

在本部分中，你将通过授予 B.Simon 访问 Netskope 用户身份验证的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中选择“Netskope 用户身份验证”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-netskope-user-authentication-sso"></a>配置 Netskope 用户身份验证 SSO

1. 在浏览器中打开新标签页，并以管理员身分登录 Netskope 用户身份验证公司站点。

1. 单击“活动平台”选项卡  。

    ![Netskope 用户身份验证配置](./media/netskope-user-authentication-tutorial/user1.png)

1. 向下滚动至“正向代理”并选择“SAML”   。

    ![Netskope 用户身份验证配置](./media/netskope-user-authentication-tutorial/config-saml.png)

1. 在“SAML 设置”  页上，执行以下步骤：

    ![Netskope 用户身份验证配置](./media/netskope-user-authentication-tutorial/configure-copyurls.png)

    a. 复制“SAML 实体 ID”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“标识符”文本框中    。

    b. 复制“SAML ACS URL”值，并将其粘贴到 Azure 门户上“基本 SAML 配置”部分的“回复 URL”文本框中    。

1. 单击“添加帐户”  。

    ![Netskope 用户身份验证配置](./media/netskope-user-authentication-tutorial/config-addaccount.png)

1. 在“添加 SAML 帐户”页上执行以下步骤  ：

    ![Netskope 用户身份验证配置](./media/netskope-user-authentication-tutorial/config-settings1.png)

    a. 在“名称”文本框中提供类似于 Azure AD 的名称  。

    b. 在“IDP URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    c. 在“IDP 实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    d. 在记事本中打开下载的元数据文件，将其内容复制到剪贴板，然后将其粘贴到“IDP 证书”文本框中  。

    e. 单击“保存”  。

### <a name="create-netskope-user-authentication-test-user"></a>创建 Netskope 用户身份验证测试用户

1. 在浏览器中打开新标签页，并以管理员身分登录 Netskope 用户身份验证公司站点。

1. 在左侧导航窗格中单击“设置”选项卡  。

    ![Netskope 用户身份验证用户创建](./media/netskope-user-authentication-tutorial/config-settings.png)

1. 单击“活动平台”选项卡  。

    ![Netskope 用户身份验证用户创建](./media/netskope-user-authentication-tutorial/user1.png)

1. 单击“用户”选项卡。 

    ![Netskope 用户身份验证用户创建](./media/netskope-user-authentication-tutorial/add-user.png)

1. 单击“添加用户”  。

    ![Netskope 用户身份验证用户创建](./media/netskope-user-authentication-tutorial/user-add.png)

1. 输入要添加的用户的电子邮件地址，然后单击“添加”  。

    ![Netskope 用户身份验证用户创建](./media/netskope-user-authentication-tutorial/add-user-popup.png)

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在“访问面板”中单击“Netskope 用户身份验证”磁贴时，应会自动登录到设置了 SSO 的 Netskope 用户身份验证。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Netskope 用户身份验证](https://aad.portal.azure.com/)