---
title: "教程：Azure Active Directory 与 LinkedInSalesNavigator 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 LinkedInSalesNavigator 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: ef26a16e79d9c9b0654634960b57dc59827b2c24
ms.contentlocale: zh-cn
ms.lasthandoff: 06/16/2017


---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>教程：Azure Active Directory 与 LinkedIn Sales Navigator 的集成

本教程介绍了如何将 LinkedIn Sales Navigator 与 Azure Active Directory (Azure AD) 进行集成。

将 LinkedIn Sales Navigator 与 Azure AD 集成可具有以下优势：

- 可以在 Azure AD 中控制谁有权访问 LinkedIn Sales Navigator
- 可以让用户使用其 Azure AD 帐户自动登录到 LinkedIn Sales Navigator（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请浏览 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 LinkedIn Sales Navigator 的集成，需要具有以下项：

- 一个 Azure AD 订阅
- 已启用 LinkedIn Sales Navigator 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，否则请避免使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 LinkedIn Sales Navigator
2. 配置并测试 Azure AD 单一登录

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>从库中添加 LinkedIn Sales Navigator
若要配置 LinkedIn Sales Navigator 与 Azure AD 的集成，需要从库中将 LinkedIn Sales Navigator 添加到托管 SaaS 应用列表。

**若要从库中添加 LinkedIn Sales Navigator，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **LinkedIn Sales Navigator**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_search.png)

5. 在结果窗格中选择“LinkedIn Sales Navigator”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 LinkedIn Sales Navigator 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 LinkedIn Sales Navigator 用户。 换句话说，需要在 Azure AD 用户与 LinkedIn Sales Navigator 中的相关用户之间建立关联关系。

可以通过将 Azure AD 中“用户名”的值分配为 LinkedIn Sales Navigator 中“用户名”的值来建立此链接关系。

若要配置并测试 LinkedIn Sales Navigator 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 LinkedIn Sales Navigator 测试用户](#creating-a-linkedin-sales-navigator-test-user)** - 在 LinkedIn Sales Navigator 中创建 Britta Simon 的对应者，链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分中的步骤在 Azure 门户中启用 Azure AD 单一登录并在 LinkedIn Sales Navigator 应用程序中配置单一登录。

**若要配置 LinkedIn Sales Navigator 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户的“LinkedIn Sales Navigator”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，在“模式”中选择“基于 SAML 的登录”启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_samlbase.png)

3. 在另一个 Web 浏览器窗口中，以管理员身份登录到 **LinkedIn Sales Navigator** 网站。

4. 在“帐户中心”，单击“设置”下的“全局设置”。 此外，请从下拉列表中选择“Sales Navigator”。

    ![配置单一登录](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

5. 单击“或单击此处从窗体加载和复制单个字段”，并复制“实体 ID”和“断言使用者访问(ACS) URL”。

    ![配置单一登录](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

6. 在 Azure 门户的“LinkedIn Sales Navigator 域和 URL”部分中，如果想要以“已启动 IDP”模式配置应用程序，请执行以下步骤。

    ![配置单一登录](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url1.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“标识符”文本框中，输入从 LinkedIn 门户复制的“实体 ID” 

    b. 在“回复 URL”文本框中，输入从 LinkedIn 门户复制的“断言使用者访问(ACS) URL”

7. 如果要在“SP”发起的模式下配置应用程序，请选中“显示高级 URL 设置”。

    ![配置单一登录](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url2.png)

    在“登录 URL”文本框中，使用以下模式键入值：`https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

8. **LinkedIn Sales Navigator** 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了示例。 “用户标识符”的默认值是“user.userprincipalname”，但 LinkedIn Sales Navigator 要求通过用户的电子邮件地址映射此项。 可以使用列表中的 **user.mail** 属性，或使用基于组织配置的相应属性值。 

    ![配置单一登录](./media/active-directory-saas-linkedinsalesnavigator-tutorial/updateusermail.png)
    
9. 在“用户属性”部分，单击“查看和编辑所有其他用户属性”并设置属性。 用户需要添加名为 email、department、firstname 和 lastname 的四个声明，并分别映射值 user.mail、user.department、user.givenname 和 user.surname

    | 属性名称 | 属性值 |
    | --- | --- |    
    | email| user.mail |
    | department| user.department |
    | 名| user.givenname |
    | 姓| user.surname |
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinsalesnavigator-tutorial/userattribute.png)
    
    a.在“横幅徽标”下面，选择“删除上传的徽标”。 单击“添加属性”打开属性对话框。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_attribute_04.png)
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_attribute_05.png)
   
    b. 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。
    
    d. 单击“确定”

10. 对 name 属性执行以下步骤

    a.在“横幅徽标”下面，选择“删除上传的徽标”。 单击该属性打开“编辑属性”窗口。

    ![配置单一登录](./media/active-directory-saas-linkedinsalesnavigator-tutorial/url_update.png)

    b.在“磁贴徽标”下面，选择“删除上传的徽标”。 从“命名空间”中删除 URL 值。
    
    c. 单击“确定”保存设置。

11. 在“SAML 签名证书”部分中，单击“元数据 XML”，然后在计算机上保存 XML 文件。

    ![配置单一登录](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_certificate.png) 

12. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_400.png)

13. 转到“LinkedIn 管理设置”分区。 单击“上传 XML 文件”上传从 Azure 门户下载的元数据 XML 文件。

    ![配置单一登录](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

14. 单击“打开”启用 SSO。 SSO 状态将从“未连接”更改为“已连接”

    ![配置单一登录](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)


> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”并单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinsalesnavigator-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-linkedin-sales-navigator-test-user"></a>创建 LinkedIn Sales Navigator 测试用户

LinkedIn Sales Navigator 应用程序支持适时用户 (JIT) 预配，且进行身份验证后，将在应用程序中自动创建用户。 激活“自动分配许可证”以便为用户分配许可证。
   
   ![创建 Azure AD 测试用户](./media/active-directory-saas-linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过向 Britta Simon 授予对 LinkedIn Sales Navigator 的访问权限使她能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 LinkedIn Sales Navigator，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“LinkedIn Sales Navigator”。

    ![配置单一登录](./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“LinkedIn Sales Navigator”磁贴时，应会重定向到组织页，需要在其中提供你的个人 LinkedIn 帐户详细信息。 你的个人帐户将与你的 LinkedIn 业务帐户相链接。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://msdn.microsoft.com/library/dn308586)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-linkedinsalesnavigator-tutorial/tutorial_general_203.png


