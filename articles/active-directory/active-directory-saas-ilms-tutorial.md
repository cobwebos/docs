---
title: "教程：Azure Active Directory 与 iLMS 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 iLMS 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2017
ms.author: jeedes
ms.openlocfilehash: 2764a109e92d3eabf2b7064ce7cd2e428256c8b8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>教程：Azure Active Directory 与 iLMS 的集成

在本教程中，了解如何将 iLMS 与 Azure Active Directory (Azure AD) 集成。

将 iLMS 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 iLMS
- 可以让用户使用其 Azure AD 帐户自动登录到 iLMS（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 iLMS 的集成，需要以下项：

- Azure AD 订阅
- 已启用 iLMS 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 iLMS
2. 配置和测试 Azure AD 单一登录

## <a name="adding-ilms-from-the-gallery"></a>从库中添加 iLMS
要配置 iLMS 与 Azure AD 的集成，需要从库中将 iLMS 添加到托管 SaaS 应用列表。

**若要从库中添加 iLMS，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新的应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **iLMS**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_search.png)

5. 在结果窗格中，选择“iLMS”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置并测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 iLMS 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 iLMS 用户。 换句话说，需要建立 Azure AD 用户与 iLMS 中相关用户之间的链接关系。

可以通过将 Azure AD 中“用户名”的值分配为 iLMS 中“用户名”的值来建立此链接关系。

若要配置并测试 iLMS 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 iLMS 测试用户](#creating-an-ilms-test-user)** - 在 iLMS 中创建 Britta Simon 的对应用户，并将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，会在 Azure 门户中启用 Azure AD 单一登录并在 iLMS 应用程序中配置单一登录。

**若要配置 iLMS 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 **iLMS** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_samlbase.png)

3. 在“iLMS 域和 URL”部分中，如果要在“IDP”发起的模式下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url.png)

    a. 在“标识符”文本框中，粘贴从 iLMS 管理门户上“SAML 设置”的“服务提供程序”部分中复制的“标识符”值。

    b. 在“回复 URL”文本框中，粘贴从 iLMS 管理门户上“SAML 设置”的“服务提供程序”部分中复制的、采用 `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx` 模式的“终结点(URL)”值

    >[!Note]
    >其中的“123456”是标识符的示例值。

4. 如果要在“SP”发起的模式下配置应用程序，请选中“显示高级 URL 设置”：

    ![配置单一登录](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_url1.png)

    在“登录 URL”文本框中，粘贴从 iLMS 管理门户上“SAML 设置”的“服务提供程序”部分中复制的、采用 `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx` 模式的“终结点(URL)”值     

5. 若要启用 JIT 预配，iLMS 应用程序需要采用特定格式的 SAML 断言。 请为此应用程序配置以下声明。 可以在应用程序集成页的“用户属性”部分管理这些属性的值。 以下屏幕截图显示一个示例。
    
    ![配置单一登录](./media/active-directory-saas-ilms-tutorial/4.png)
    
    创建“部门”、“区域”和“分支”属性，并添加这些属性在 iLMS 中的名称。 上面所示的所有这些属性都是必需的。  

    > [!NOTE] 
    > 必须在 iLMS 中启用“创建不可识别的用户帐户”才能映射这些属性。 遵照[此处](http://support.inspiredelearning.com/customer/portal/articles/2204526)的说明获取有关属性配置的思路。

6. 在“单一登录”对话框的“用户属性”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 属性名称 | 属性值 |
    | ---------------| --------------- |    
    | division | user.department |
    | region | user.state |
    | department | user.jobtitle |

    a. 单击“添加属性”，打开“添加属性”对话框。

    ![配置单一登录](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_04.png)

    ![配置单一登录](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_05.png)
    
    b.保留“数据库类型”设置，即设置为“共享”。 在“名称”文本框中，键入为该行显示的属性名称。
    
    c. 在“值”列表中，选择为该行显示的属性值。
    
    d. 单击“确定”

7. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存 XML 文件。

    ![配置单一登录](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_certificate.png) 

8. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-iLMS-tutorial/tutorial_general_400.png)

9. 在另一个 Web 浏览器窗口中，以管理员身份登录到 **iLMS 管理门户**。

10. 单击“设置”选项卡下面的“SSO:SAML”打开“SAML 设置”并执行以下步骤：
    
    ![配置单一登录](./media/active-directory-saas-ilms-tutorial/1.png) 

    a. 展开“服务提供程序”部分，并复制“标识符”和“终结点(URL)”值。

    ![配置单一登录](./media/active-directory-saas-ilms-tutorial/2.png) 

    b. 在“标识提供者”部分下面，单击“导入元数据”。
    
    c. 在“SAML 签名证书”部分中选择从 Azure 门户下载的“元数据”文件。

    ![配置单一登录](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig1.png) 

    d. 如果想要启用 JIT 预配以便为不可识别的用户创建 iLMS 帐户，请执行以下步骤：
        
       - 选中“创建不可识别的用户帐户”。
       
       ![配置单一登录](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_ssoconfig2.png)

       -  将 Azure AD 中的属性映射到 iLMS 中的属性。 在属性列中，指定属性名称或默认值。

    e. 转到“业务规则”选项卡并执行以下步骤： 
        
       ![配置单一登录](./media/active-directory-saas-ilms-tutorial/5.png)

       - 选中“创建不可识别的区域、分支和部门”，创建单一登录时尚不存在的区域、分支和部门。
        
       - 选中“登录期间更新用户配置文件”，指定是否在每次单一登录时都更新用户的配置文件。 
        
       - 如果已选中“更新用户配置文件中非必填字段的空白值”选项，登录时空白的可选配置文件字段也会导致用户的 iLMS 配置文件包含这些字段的空白值。
        
       - 选中“发送错误通知电子邮件”，并输入要向其发送错误通知电子邮件的用户显示的电子邮件内容。

11. 单击“保存”按钮保存设置。

    ![配置单一登录](./media/active-directory-saas-ilms-tutorial/save.png)

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
    
### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-ilms-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-ilms-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-ilms-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-ilms-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-an-ilms-test-user"></a>创建 iLMS 测试用户

应用程序支持实时用户预配，且进行身份验证后，会在应用程序中自动创建用户。 如果在 iLMS 管理门户中设置 SAML 配置期间已单击“创建不可识别的用户帐户”复选框，则 JIT 会正常工作。

如果需要手动创建用户，请执行以下步骤：

1. 以管理员身份登录到 iLMS 公司站点。

2. 单击“用户”选项卡下面的“注册用户”打开“注册用户”页。 
   
   ![添加员工](./media/active-directory-saas-ilms-tutorial/3.png)

3. 在“注册用户”页上执行以下步骤。

    ![添加员工](./media/active-directory-saas-ilms-tutorial/create_testuser_add.png)

    a. 在“名字”文本框中键入名字 Britta。
   
    b. 在“姓氏”文本框键入姓氏 Simon。

    c. 在“电子邮件 ID”文本框中，键入 Britta Simon 帐户的电子邮件地址。

    d. 在“区域”下拉列表中，选择区域的值。

    e. 在“分支”下拉列表中，选择分支的值。

    f. 在“部门”下拉列表中，选择部门的值。

    g. 单击“保存” 。

    > [!NOTE] 
    > 选中“发送注册电子邮件”复选框可向用户发送注册邮件。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 iLMS 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 iLMS，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“iLMS”。

    ![配置单一登录](./media/active-directory-saas-ilms-tutorial/tutorial_ilms_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 iLMS 磁贴时，应会自动登录到 iLMS 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ilms-tutorial/tutorial_general_203.png

