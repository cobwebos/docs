---
title: "教程：Azure Active Directory 与 ADP Globalview 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 ADP Globalview 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffb6464f-714d-41a9-869a-2b7e5ae9f125
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: e9a5e65c484dfb98d1a7bc63d55f6ef92039554b
ms.contentlocale: zh-cn
ms.lasthandoff: 06/22/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adp-globalview"></a>教程：Azure Active Directory 与 ADP Globalview 集成

在本教程中，了解如何将 ADP Globalview 与 Azure Active Directory (Azure AD) 集成。

将 ADP Globalview 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权限访问 ADP Globalview
- 可使用户通过其 Azure AD 帐户自动登录 ADP Globalview（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 ADP Globalview 的集成，需要具有以下项：

- 一个 Azure AD 订阅
- 已启用 ADP Globalview 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 ADP Globalview
2. 配置和测试 Azure AD 单一登录

## <a name="adding-adp-globalview-from-the-gallery"></a>从库中添加 ADP Globalview
若要配置 ADP Globalview 与 Azure AD 的集成，需要将库中的 ADP Globalview 添加到托管的 SaaS 应用列表。

若要从库中添加 ADP Globalview，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 ADP Globalview。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_search.png)

5. 在”结果”窗格中，选择“ADP Globalview”，然后单击“添加”按钮，添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 ADP Globalview 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 ADP Globalview 用户。 换句话说，需要建立 Azure AD 用户与 ADP Globalview 中相关用户之间的链接关系。

可以通过将 Azure AD 中“用户名”的值指定为 ADP GlobalView 中的“用户名”的值来建立此链接关系。

若要配置和测试 ADP Globalview 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 ADP Globalview 测试用户](#creating-an-adp-globalview-test-user)** - 在 ADP Globalview 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 ADP Globalview 应用程序中配置单一登录。

若要通过 ADP Globalview 配置 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“ADP Globalview”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_samlbase.png)

3. 在“ADP Globalview 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_url.png)

     在“标识符”文本框中，使用以下模式键入 URL：`https://<subdomain>.globalview.adp.com/federate` 或 `https://<subdomain>.globalview.adp.com/federate2`

    > [!NOTE] 
    > 此值不是真实值。 请使用实际标识符更新此值。 请与 [ADP Globalview 支持](https://www.adp.com/contact-us/overview.aspx)联系以获取此值。
 
4. 在“SAML 签名证书”部分中，单击“证书(Base64)”，然后在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_certificate.png) 

5. ADP GlobalView 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 

6. 以下屏幕截图显示了一个示例。 声明名称始终是“PersonImmutableID”和映射到包含用户 EmployeeID 的 ExtensionAttribute2 的值。 在此将用户从 Azure AD 映射到 ADP GlobalView 是在 EmployeeID 上完成的，但可将其映射到同样基于应用程序设置的其他值。 首先可与 ADP GlobalView 团队合作，使用用户的正确标识符，并将该值与**“PersonImmutableID”**声明一起映射。 也可映射电子邮件和 UserID 声明，如图中所示。

    ![配置单一登录](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_attribute.png)

7. 在“单一登录”对话框的“用户属性”部分，按图中所示配置 SAML 令牌属性，然后执行以下步骤：
    
    | 属性名称 | 属性值 |
    | ------------------- | -------------------- |    
    | personalimmutableid | user.extensionattribute2 |
    | email               | user.mail |
    | userid              | user.userprincipalname|
    
    a.在“横幅徽标”下面，选择“删除上传的徽标”。 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/active-directory-saas-adglobalview-tutorial/tutorial_attribute_04.png)

    ![配置单一登录](./media/active-directory-saas-adglobalview-tutorial/tutorial_attribute_05.png)

    b.保留“数据库类型”设置，即设置为“共享”。 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。
    
    d. 单击“确定” 。

    > [!NOTE] 
    > 在配置 SAML 断言前，需要联系 [ADP Globalview 支持](https://www.adp.com/contact-us/overview.aspx)，并为租户请求唯一标识符属性值。 拥有此值才能为应用程序配置自定义声明。 

8. 在“ADP Globalview 配置”部分，单击“配置 ADP Globalview”，以打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_configure.png) 

9. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-adglobalview-tutorial/tutorial_general_400.png)

10. 若要在“ADP Globalview”端配置单一登录，需要将下载的证书 (Base64)、注销 URL、SAML 实体 ID 和 SAML 单一登录服务 URL 发送给 [ADP Globalview 支持](https://www.adp.com/contact-us/overview.aspx)。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-adglobalview-tutorial/create_aaduser_01.png) 

2.  若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adglobalview-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adglobalview-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-adglobalview-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-an-adp-globalview-test-user"></a>创建 ADP Globalview 测试用户

本部分要在 ADP GlobalView 中创建名为“Britta Simon”的用户。 请与 [ADP Globalview 支持](https://www.adp.com/contact-us/overview.aspx)协作，以添加 ADP Globalview 帐户中的用户。 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 ADP Globalview 的权限，以支持其使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 ADP Globalview，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在“应用程序列表”中，选择“ADP Globalview”。

    ![配置单一登录](./media/active-directory-saas-adglobalview-tutorial/tutorial_adpglobalview_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD SSO 配置。  

当在访问面板中单击“ADP GlobalView”磁贴时，应该会自动登录“ADP GlobalView”应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adglobalview-tutorial/tutorial_general_203.png


