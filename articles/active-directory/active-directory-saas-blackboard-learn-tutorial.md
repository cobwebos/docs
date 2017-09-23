---
title: "教程：Azure Active Directory 与 Blackboard Learn 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Blackboard Learn 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 0b8ca505-61ea-487c-9a3e-fa50c936df0c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: c2b7638e99fa46ff41a7f2202bdf0e7a2b017c19
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017

---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn"></a>教程：Azure Active Directory 与 Blackboard Learn 的集成

在本教程中，了解如何将 Blackboard Learn 与 Azure Active Directory (Azure AD) 集成。

将 Blackboard Learn 与 Azure AD 集成可为你提供以下好处：

- 可在 Azure AD 中控制哪些人有权访问 Blackboard Learn
- 可使用户使用其 Azure AD 帐户自动登录到 Blackboard Learn（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Blackboard Learn 的集成，需要以下各项：

- 一个 Azure AD 订阅
- 已启用 Blackboard Learn 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Blackboard Learn
2. 配置和测试 Azure AD 单一登录

## <a name="adding-blackboard-learn-from-the-gallery"></a>从库添加 Blackboard Learn
若要配置 Blackboard Learn 到 Azure AD 的集成，需要从库将 Blackboard Learn 添加到托管 SaaS 应用列表。

**若要从库添加 Blackboard Learn，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **Blackboard Learn**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_search.png)

5. 在“结果”窗格中，选择“Blackboard Learn”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于名为“Britta Simon”的测试用户配置和测试 Blackboard Learn 的 Azure AD 单一登录。

若要使单一登录工作，Azure AD 需要知道对于 Azure AD 中的用户，Blackboard Learn 中的对应用户是什么。 换言之，需要在 Azure AD 用户和 Blackboard Learn 中的相关用户之间建立链接关系。

通过将 Azure AD 中的“用户名”指定为 Blackboard Learn 中“用户名”的值建立此链接关系。

若要配置和测试对 Blackboard Learn 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Blackboard Learn 测试用户](#creating-a-blackboard-learn-test-user)** - 目的是在 Blackboard Learn 中拥有一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，在 Azure 门户中启用 Azure AD 单一登录并在 Blackboard Learn 应用程序中配置单一登录。

**若要配置对 Blackboard Learn 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 Blackboard Learn 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_samlbase.png)

3. 在“Blackboard Learn 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.blackboard.com/`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<subdomain>.blackboard.com/auth-saml/saml/SSO/entity-id/SAML_AD`
    
    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Blackboard Learn 客户端支持团队](https://www.blackboard.com/support/index.aspx)获取这些值。 

4. Blackboard Learn 应用程序需要特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。
 以下屏幕截图显示了关于它的示例。

    ![配置单一登录](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_attribute.png)

5. 在“单一登录”对话框的“用户属性”部分中，按图中所示配置 SAML 令牌属性，然后执行以下步骤。 我们已将 Userprincipalname 映射为唯一用户属性，但你可以将其映射到唯一区分组织中的用户和映射到 Blackboard Learn 用户名字段的相应值。
           
    | 属性名称 | 属性值 |   
    | ---------------| ----------------|
    | urn:oid:1.3.6.1.4.1.5923.1.1.1.6 |user.userprincipalname |

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_attribute_04.png)
    
    ![配置单一登录](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_attribute_05.png)

    b.保留“数据库类型”设置，即设置为“共享”。 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“值”列表中，选择为该行显示的属性值。
    
    d. 单击“确定” 。

4. 在“SAML 签名证书”部分中，单击“元数据 XML”，然后在计算机上保存 XML 文件。

    ![配置单一登录](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_certificate.png)

7. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_400.png)

8. 在“Blackboard Learn 配置”部分，单击“配置 Blackboard Learn”以打开“配置登录”窗口。 从“快速参考”部分复制 SAML 实体 ID。

    ![配置单一登录](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_configure.png) 

9. 若要在 Blackboard Learn 端配置单一登录，需要将下载的元数据 XML 和 SAML 实体 ID 发送到 [Blackboard Learn 支持](https://www.blackboard.com/support/index.aspx)。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 Britta Simon 的**电子邮件地址**。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-blackboard-learn-test-user"></a>创建 Blackboard Learn 测试用户
在本部分中，在 Blackboard Learn 中创建名为 Britta Simon 的用户。 

Blackboard Learn 应用程序支持准时用户预配。 请确保已按照**[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)**部分中所述配置声明
### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Blackboard Learn 的访问权限支持她使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Blackboard Learn，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Blackboard Learn”。

    ![配置单一登录](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Blackboard Learn”磁贴时，应该会自动登录到 Blackboard Learn 应用程序。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_203.png


