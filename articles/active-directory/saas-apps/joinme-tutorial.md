---
title: 教程：Azure Active Directory 与 join.me 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 与 join.me 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cda5ea0d-3270-4ba5-ad81-4df108eaad12
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: dc393b8701bd72ea7f9de543de33116ea3cb5653
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2018
ms.locfileid: "48872032"
---
# <a name="tutorial-azure-active-directory-integration-with-joinme"></a>教程：Azure Active Directory 与 join.me 的集成

在本教程中，了解如何将 join.me 与 Azure Active Directory (Azure AD) 集成。

将 join.me 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 join.me。
- 可以让用户使用其 Azure AD 帐户自动登录到 join.me（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 join.me 的集成，需要以下项目：

- Azure AD 订阅
- 启用了单一登录的 join.me 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 join.me
2. 配置和测试 Azure AD 单一登录

## <a name="adding-joinme-from-the-gallery"></a>从库中添加 join.me
若要配置 join.me 与 Azure AD 的集成，需要从库中将 join.me 添加到托管 SaaS 应用列表。

**若要从库中添加 join.me，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![图像](./media/joinme-tutorial/selectazuread.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![图像](./media/joinme-tutorial/a_select_app.png)
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![图像](./media/joinme-tutorial/a_new_app.png)

4. 在搜索框中，键入“join.me”，在结果面板中选择“join.me”，然后单击“添加”按钮添加该应用程序。

     ![图像](./media/joinme-tutorial/tutorial_joinme_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 Britta Simon 的测试用户配置和测试 join.me 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 join.me 用户。 换句话说，需要在 Azure AD 用户与 join.me 中相关用户之间建立链接关系。

若要配置并测试 join.me 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 join.me 测试用户](#create-a-joinme-test-user)** - 在 join.me 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 join.me 应用程序中配置单一登录。

**若要配置 join.me 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **join.me** 应用程序集成页上，选择“单一登录”。

    ![图像](./media/joinme-tutorial/b1_b2_select_sso.png)

2. 单击屏幕顶部的“更改单一登录模式”，以选择“SAML”模式。

      ![图像](./media/joinme-tutorial/b1_b2_saml_ssso.png)

3. 在“选择单一登录方法”对话框中，对 SAML 模式单击“选择”以启用单一登录。

    ![图像](./media/joinme-tutorial/b1_b2_saml_sso.png)

4. 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“基本 SAML 配置”对话框。

    ![图像](./media/joinme-tutorial/b1-domains_and_urlsedit.png)

5. 在“基本 SAML 配置”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成。

     ![图像](./media/joinme-tutorial/tutorial_joinme_url.png)
 
6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮以复制“应用联合元数据 URL”并将其保存在计算机上。

    ![图像](./media/joinme-tutorial/certificatebase64.png) 

7. 若要在 **join.me** 端配置单一登录，需要将从 Azure 门户复制的**应用联合元数据 URL** 发送给 [join.me 支持团队](https://help.join.me/s/?language)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”。

    ![图像](./media/joinme-tutorial/d_users_and_groups.png)

2. 选择屏幕顶部的“新建用户”。

    ![图像](./media/joinme-tutorial/d_adduser.png)

3. 在“用户属性”中，执行以下步骤。

    ![图像](./media/joinme-tutorial/d_userproperties.png)

    a. 在“名称”字段中，输入 **BrittaSimon**。
  
    b. 在“用户名”字段中键入 **brittasimon@yourcompanydomain.extension**  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
  
### <a name="create-a-joinme-test-user"></a>创建 join.me 测试用户

在本部分中，将在 join.me 中创建一个名为 Britta Simon 的用户。 与 [join.me 支持团队](https://help.join.me/s/?language)协作，将用户添加到 join.me 平台中。 使用单一登录前，必须先创建并激活用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予对 join.me 的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![图像](./media/joinme-tutorial/d_all_applications.png)

2. 在应用程序列表中，选择“join.me”。

    ![图像](./media/joinme-tutorial/tutorial_joinme_app.png)

3. 在左侧菜单中，选择“用户和组”。

    ![图像](./media/joinme-tutorial/d_leftpaneusers.png)

4. 选择“添加”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![图像](./media/joinme-tutorial/d_assign_user.png)

4. 在“用户和组”对话框中，在“用户”列表中选择 Britta Simon，然后单击屏幕底部的“选择”按钮。

5. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 join.me 磁贴时，应当会自动登录到 join.me 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

