---
title: 教程：Azure Active Directory 与 TextMagic 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 TextMagic 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec8085d2d3ac4cc338ab9c0c36eec481a5d4fbc9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56200337"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>教程：Azure Active Directory 与 TextMagic 的集成

本教程介绍了如何将 TextMagic 与 Azure Active Directory (Azure AD) 进行集成。

将 TextMagic 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 TextMagic。
- 可以让用户使用其 Azure AD 帐户自动登录到 TextMagic（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 TextMagic 的集成，需要以下项：

- Azure AD 订阅
- 一个启用了单一登录的 TextMagic 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 TextMagic
2. 配置和测试 Azure AD 单一登录

## <a name="adding-textmagic-from-the-gallery"></a>从库中添加 TextMagic

若要配置 TextMagic 与 Azure AD 的集成，需要从库中将 TextMagic 添加到托管 SaaS 应用列表。

**若要从库中添加 TextMagic，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“TextMagic”，在结果面板中选择“TextMagic”，然后单击“添加”按钮添加应用程序。

    ![结果列表中的 TextMagic](./media/textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户配置和测试 TextMagic 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 TextMagic 用户。 换句话说，需要在 Azure AD 用户与 TextMagic 中的相关用户之间建立链接关系。

若要配置和测试 TextMagic 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 TextMagic 测试用户](#creating-a-textmagic-test-user)** - 在 TextMagic 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 TextMagic 应用程序中配置单一登录。

**若要配置 TextMagic 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **TextMagic** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](common/tutorial_general_301.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](common/editconfigure.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    ![TextMagic 域和 URL 单一登录信息](./media/textmagic-tutorial/tutorial_textmagic_url.png)

    在“标识符”文本框中，键入一个 URL：`https://my.textmagic.com/saml/metadata`

5. TextMagic 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性和声明”部分管理这些属性的值。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性和声明”对话框。

    ![图像](./media/textmagic-tutorial/i4-attribute.png)

6. 在“用户属性和声明”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：

    | Name  | 源属性  | 命名空间 |
    | --------------- | --------------- | --------------- |
    | company | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | firstName               | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastName            | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | phone               | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    
    a. 单击“编辑”图标以将“名称标识符值”从“user.userprinicipalname”编辑为“user.mail”。

    ![TextMagic 属性](./media/textmagic-tutorial/tutorial_textmagic_email.png)

    b. 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](./common/new_save_attribute.png)

    ![图像](./common/new_attribute_details.png)

    c. 在“名称”文本框中，键入为该行显示的属性名称。

    d. 输入“命名空间”值。

    e. 选择“源”作为“属性”。

    f. 在“源属性”列表中，键入为该行显示的属性值。

    g. 单击“确定” 。

    h. 单击“ **保存**”。 

7. 在“SAML 签名证书”页的“SAML 签名证书”部分中，单击“下载”以下载“证书(Base64)”并将证书文件保存在计算机上。

    ![证书下载链接](./media/textmagic-tutorial/tutorial_textmagic_certificate.png) 

8. 在“设置 TextMagic”部分中，根据要求复制相应的 URL。

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![TextMagic 配置](common/configuresection.png)

9. 在另一个 Web 浏览器窗口中，以管理员身份登录到 TextMagic 公司站点。

10. 在用户名下选择“帐户设置”。

    ![TextMagic 配置](./media/textmagic-tutorial/config1.png)

11. 单击“单一登录(SSO)”选项卡并填写以下字段：  
    
    ![TextMagic 配置](./media/textmagic-tutorial/config2.png)

    a. 在“标识提供者实体 ID:”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值 ****  ****。

    b. 在“标识提供者 SSO URL:”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 ****  ****。

    c. 在“标识提供者 SLO URL:”文本框中，粘贴从 Azure 门户复制的“注销 URL”值 ****  ****。

    d. 在记事本中打开从 Azure 门户下载的 base-64 编码证书，将其内容复制到剪贴板，然后粘贴到“公用 x509 证书:”文本框 **** 。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](common/create_aaduser_01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](common/create_aaduser_02.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
  
### <a name="creating-a-textmagic-test-user"></a>创建一个 TextMagic 测试用户

应用程序支持实时用户预配，且进行身份验证后，会在应用程序中自动创建用户。 你需要在首次登录时填写一次该信息以在系统中激活子帐户。
此部分不存在任何操作项。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 TextMagic 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“TextMagic”。

    ![配置单一登录](./media/textmagic-tutorial/tutorial_textmagic_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 TextMagic 磁贴时，应该会自动登录到 TextMagic 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
