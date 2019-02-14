---
title: 教程：Azure Active Directory 与 BorrowBox 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 BorrowBox 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd8e4178-9a63-492a-bd48-782e94e404af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a36b87f0a440bb2bf8d8d75cf0d29dbd521ff43f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56174990"
---
# <a name="tutorial-azure-active-directory-integration-with-borrowbox"></a>教程：Azure Active Directory 与 BorrowBox 集成

在本教程中，了解如何将 BorrowBox 与 Azure Active Directory (Azure AD) 集成。

将 BorrowBox 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 BorrowBox。
- 可以让用户使用其 Azure AD 帐户自动登录到 BorrowBox（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 BorrowBox 的集成，需要以下项目：

- Azure AD 订阅
- 启用了单一登录的 BorrowBox 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 BorrowBox
2. 配置和测试 Azure AD 单一登录

## <a name="adding-borrowbox-from-the-gallery"></a>从库中添加 BorrowBox
若要配置 BorrowBox 与 Azure AD 的集成，需要从库中将 BorrowBox 添加到托管 SaaS 应用列表。

**若要从库中添加 BorrowBox，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![图像](./common/selectazuread.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![图像](./common/a_select_app.png)
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![图像](./common/a_new_app.png)

4. 在搜索框中，键入“BorrowBox”，在结果面板中选择“BorrowBox”，然后单击“添加”按钮添加该应用程序。

     ![图像](./media/borrowbox-tutorial/tutorial_borrowbox_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 Britta Simon 的测试用户配置和测试 BorrowBox 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 BorrowBox 用户。 换句话说，需要在 Azure AD 用户与 BorrowBox 中相关用户之间建立链接关系。

若要配置并测试 BorrowBox 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 BorrowBox 测试用户](#create-a-borrowbox-test-user)** - 在 BorrowBox 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 BorrowBox 应用程序中配置单一登录。

**若要配置 BorrowBox 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **BorrowBox** 应用程序集成页上，选择“单一登录”。

    ![图像](./common/B1_B2_Select_SSO.png)

2. 在“选择单一登录方法”对话框中，选择 SAML 模式以启用单一登录。

    ![图像](./common/b1_b2_saml_sso.png)

3. 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“基本 SAML 配置”对话框。

    ![图像](./common/b1-domains_and_urlsedit.png)

4. 在“基本 SAML 配置”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成。

    ![图像](./media/borrowbox-tutorial/tutorial_borrowbox_url.png)

    a. 单击“设置其他 URL”。

    b. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://fe.bolindadigital.com/wldcs_bol_fo/b2i/mainPage.html?b2bSite=<ID>`。

    ![图像](./media/borrowbox-tutorial/tutorial_borrowbox_url1.png)

    > [!NOTE]
    > 登录 URL 值不是实际值。 请使用实际登录 URL 更新此值。 若要获取此值，请与 [BorrowBox 客户端支持团队](mailto:borrowbox@bolinda.com)联系。

5. BorrowBox 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性和声明”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性和声明”对话框。

    ![图像](./media/borrowbox-tutorial/i4-attribute.png)

6. 在“用户属性和声明”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    a. 单击“编辑”图标，以打开“管理用户声明”对话框。

    ![图像](./media/borrowbox-tutorial/i2-attribute.png)

    ![图像](./media/borrowbox-tutorial/i3-attribute.png)

    b. 在“源属性”列表中，选择 **user.mail**。

    c. 单击“ **保存**”。 

7. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，单击“下载”，以根据要求下载相应的证书，并将证书保存在计算机上。

    ![图像](./media/borrowbox-tutorial/tutorial_borrowbox_certificate.png) 

8. 若要在 **BorrowBox** 端配置单一登录，你需要将从 Azure 门户下载的证书/元数据发送给 [BorrowBox 支持团队](mailto:borrowbox@bolinda.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![图像](./common/d_users_and_groups.png)

2. 选择屏幕顶部的“新建用户”。

    ![图像](./common/d_adduser.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![图像](./common/d_userproperties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
 
### <a name="create-a-borrowbox-test-user"></a>创建 BorrowBox 测试用户

本部分的目的是在 BorrowBox 中创建名为 Britta Simon 的用户。 BorrowBox 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 如果尚不存在用户，在尝试访问 BorrowBox 期间将创建一个新用户。
>[!Note]
>如果需要手动创建用户，请联系  [BorrowBox 支持团队](mailto:borrowbox@bolinda.com)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予对 BorrowBox 的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![图像](./common/d_all_applications.png)

2. 在应用程序列表中选择“BorrowBox”。

    ![图像](./media/borrowbox-tutorial/tutorial_borrowbox_app.png)

3. 在左侧菜单中，选择“用户和组”。

    ![图像](./common/d_leftpaneusers.png)

4. 选择“添加”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![图像](./common/d_assign_user.png)

4. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

5. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 BorrowBox 磁贴时，应当会自动登录到 BorrowBox 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
