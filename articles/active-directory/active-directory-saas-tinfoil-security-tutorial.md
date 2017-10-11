---
title: "教程：Azure Active Directory 与 TINFOIL SECURITY 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 TINFOIL SECURITY 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 614e4de3335574f4b56c7d641af4fcfafdb17d12
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>教程：Azure Active Directory 与 TINFOIL SECURITY 集成

本教程介绍如何将 TINFOIL SECURITY 与 Azure Active Directory (Azure AD) 集成。

将 TINFOIL SECURITY 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 TINFOIL SECURITY
- 可以让用户使用其 Azure AD 帐户自动登录到 TINFOIL SECURITY（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 TINFOIL SECURITY 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 TINFOIL SECURITY 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 TINFOIL SECURITY
2. 配置和测试 Azure AD 单一登录

## <a name="add-tinfoil-security-from-the-gallery"></a>从库中添加 TINFOIL SECURITY
若要配置 TINFOIL SECURITY 与 Azure AD 的集成，需从库中将 TINFOIL SECURITY 添加到托管 SaaS 应用列表。

**若要从库中添加 TINFOIL SECURITY，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“TINFOIL SECURITY”，从结果面板中选择“TINFOIL SECURITY”，然后单击“添加”按钮添加该应用程序。

    ![库中的 TINFOIL SECURITY](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分中，将基于名为“Britta Simon”的测试用户配置并测试 TINFOIL SECURITY 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道 TINFOIL SECURITY 中与 Azure AD 用户相对应的用户。 换句话说，需要建立起 Azure AD 用户与 TINFOIL SECURITY 中的相关用户之间的关联。

通过将 TINFOIL SECURITY 中“用户名”的值指定为 Azure AD 中“用户名”的值来建立此关联。

若要配置并测试 TINFOIL SECURITY 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建一个 TINFOIL SECURITY 测试用户](#create-a-tinfoil-security-test-user)** -若要链接到用户的 Azure AD 表示的 TINFOIL SECURITY 中具有 Britta 人 Simon 的副本。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 TINFOIL SECURITY 应用程序中配置单一登录。

**若要配置 TINFOIL SECURITY 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 TINFOIL SECURITY 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![基于 SAML 的登录](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

3. 在“TINFOIL SECURITY 域和 URL”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成。

    ![配置单一登录](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


4. 在“SAML 签名证书”部分中，复制“指纹”值。

    ![“SAML 签名证书”部分](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

5. 若要添加所需的属性映射，请执行以下步骤：
    
    ![属性](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "属性")
    
    | 属性名称    |   属性值 |
    | ------------------- | -------------------- |
    | 帐户 ID | UXXXXXXXXXXXXX |
    
    a. 单击“添加用户属性”。
    
    ![添加属性](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "属性")
    
    ![添加属性](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "属性")
    
    b. 在“属性名称”文本框中，键入“accountid”。
    
    c. 在“属性值”文本框中，粘贴稍后将在教程中获取的帐户 ID 值。
    
    d. 单击“确定” 。    

6. 单击“保存”按钮。

    ![“保存”按钮](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_400.png)

7. 在“TINFOIL SECURITY 配置”部分中，单击“配置 TINFOIL SECURITY”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”

    ![TINFOIL SECURITY 配置](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

8. 在另一 Web 浏览器窗口中，以管理员身份登录 TINFOIL SECURITY 公司站点。

9. 在顶部菜单栏中，单击“我的帐户”。
   
    ![仪表板](./media/active-directory-saas-tinfoil-security-tutorial/ic798971.png "Dashboard")

10. 单击“安全”。
   
    ![安全](./media/active-directory-saas-tinfoil-security-tutorial/ic798972.png "安全")

11. 在“单一登录”配置页上，执行以下步骤：
   
    ![单一登录](./media/active-directory-saas-tinfoil-security-tutorial/ic798973.png "单一登录")
   
    a. 选择“启用 SAML”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 单击“手动配置”。
   
    c. 在“SAML 发布 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值
   
    d. 在“SAML 证书指纹”文本框中，粘贴从“SAML 签名证书”部分复制的“指纹”值。
  
    e. 复制“你的帐户 ID”值，将此值粘贴在 Azure 门户中“添加属性”部分下的“属性值”文本框中。
   
    f. 单击“保存” 。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![“用户和组”->“所有用户” ](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![用户](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="create-a-tinfoil-security-test-user"></a>创建 TINFOIL SECURITY 测试用户

为使 Azure AD 用户能够登录 TINFOIL SECURITY，必须将这些用户预配到 TINFOIL SECURITY 中。 对于 TINFOIL SECURITY，需手动完成预配。

**若要预配用户，请执行以下步骤：**

1. 如果用户是企业帐户的一部分，你则需要联系 [TINFOIL SECURITY 支持团队](https://www.tinfoilsecurity.com/contact)以创建用户帐户。

2. 如果用户是常规 TINFOIL SECURITY SaaS 用户，则该用户可向任何用户站点添加协作者。 这会触发向指定的电子邮件发送邀请以创建新 TINFOIL SECURITY 用户帐户的过程。

> [!NOTE]
> 可使用任何其他 TINFOIL SECURITY 用户帐户创建工具或使用 TINFOIL SECURITY 提供的 API 来预配 Azure AD 用户帐户。
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 TINFOIL SECURITY 的权限，使她能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 TINFOIL SECURITY，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“TINFOIL SECURITY”。

    ![选择“TINFOIL SECURITY”](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 TINFOIL SECURITY 磁贴时，应会自动登录到 TINFOIL SECURITY 应用程序。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_203.png

