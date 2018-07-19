---
title: 教程：Azure Active Directory 与 Kantega SSO for Bitbucket 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Kantega SSO for Bitbucket 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c41cdaaf-0441-493c-94c7-569615b7b1ab
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: ce88ff333cd24e331c0d9c9b3b58372d5b09fdec
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042888"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>教程：Azure Active Directory 与 Kantega SSO for Bitbucket 集成

本教程介绍如何将 Kantega SSO for Bitbucket 与 Azure Active Directory (Azure AD) 集成。

将 Kantega SSO for Bitbucket 与 Azure AD 集成具有以下优势：

- 可以在 Azure AD 中控制谁有权访问 Kantega SSO for Bitbucket
- 可以让用户通过其 Azure AD 帐户自动登录到 Kantega SSO for Bitbucket（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Kantega SSO for Bitbucket 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Kantega SSO for Bitbucket 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Kantega SSO for Bitbucket
2. 配置和测试 Azure AD 单一登录

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>从库中添加 Kantega SSO for Bitbucket
若要配置 Kantega SSO for Bitbucket 的集成（集成到 Azure AD 中），需从库中将 Kantega SSO for Bitbucket 添加到托管 SaaS 应用的列表中。

若要从库中添加 Kantega SSO for Bitbucket，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Kantega SSO for Bitbucket”。

    ![创建 Azure AD 测试用户](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_search.png)

5. 在结果面板中，选择“Kantega SSO for Bitbucket”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Kantega SSO for Bitbucket 的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 Kantega SSO for Bitbucket 用户。 换句话说，需要在 Azure AD 用户与 Kantega SSO for Bitbucket 中相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Kantega SSO for Bitbucket 中“用户名”的值来建立此链接关系。

若要配置并测试 Kantega SSO for Bitbucket 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Kantega SSO for Bitbucket 测试用户](#creating-a-kantega-sso-for-bitbucket-test-user) - 在 Kantega SSO for Bitbucket 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 身份。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分介绍了在 Azure 门户中启用 Azure AD 单一登录，并在 Kantega SSO for Bitbucket 应用程序中配置单一登录。

若要配置 Kantega SSO for Bitbucket 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“Kantega SSO for Bitbucket”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_samlbase.png)

3. 在“IDP”发起的模式下，在“Kantega SSO for Bitbucket 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url1.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“标识符”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

4. 在“SP”发起的模式下，请选中“显示高级 URL 设置”并执行以下步骤：

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_url2.png)
    
    在“登录 URL”文本框中，使用以下模式键入 URL： `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE] 
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 在配置 Bitbucket 插件的过程中，将接收这些值，这将在教程的后面部分进行说明。

5. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/tutorial_general_400.png)

7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Bitbucket 管理门户。

8. 单击齿轮，然后单击“查找新加载项”。

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon1.png)

9. 搜索“Kantega SSO for Bitbucket SAML & Kerberos”，然后单击“安装”按钮安装新的 SAML 插件。

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon2.png)

10. 插件安装随即开始。

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon31.png)

11. 安装完成后。 单击“关闭”。

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon33.png)

12. 单击“管理”。

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon34.png)
    
13. 单击“配置”配置新的插件。    

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon35.png)

14. 在“SAML”部分中。 从“添加标识提供者”下拉菜单选择“Azure Active Directory (Azure AD)”。

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon4.png)

15. 选择订阅级别为“基本”。

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon5.png)

16. 在“应用属性”部分，执行以下步骤：

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 复制“应用 ID URI”值并将其用作 Azure 门户中“Kantega SSO for Bitbucket 域和 URL”部分中的“标识符、回复 URL 和登录 URL”。

    b. 单击“下一步”。

17. 在“元数据导入”部分，执行以下步骤：

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择“我的计算机上的元数据文件”，上传从 Azure 门户下载的元数据文件。

    b. 单击“下一步”。

18. 在“名称和 SSO 位置”部分，执行以下步骤：

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“标识提供者名称”文本框中添加标识提供者名称（例如 Azure AD）。

    b. 单击“下一步”。

19. 验证签名证书，然后单击“下一步”。  

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon9.png)

20. 在“Bitbucket 用户帐户”部分中，执行以下步骤：

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择“根据需要在 Bitbucket 的内部目录中创建用户”，并输入用户的组的合适名称（可以为多个 组，用逗号隔开）。

    b. 单击“下一步”。

21. 单击“完成”。

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon11.png)

22. 在“Azure AD 的已知域”部分，执行以下步骤： 

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 从页的左侧面板中选择“已知域”。

    b. 在“已知域”文本框中输入域名。

    c. 单击“ **保存**”。  

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/kantegassoforbitbucket-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/kantegassoforbitbucket-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/kantegassoforbitbucket-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/kantegassoforbitbucket-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-kantega-sso-for-bitbucket-test-user"></a>创建 Kantega SSO for Bitbucket 测试用户

为了使 Azure AD 用户登录到 Bitbucket，必须将其预配到 Bitbucket。 在 Kantega SSO for Bitbucket 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Bitbucket 公司站点。

2. 单击“设置”图标。

    ![添加员工](./media/kantegassoforbitbucket-tutorial/user1.png) 

3. 在“管理”选项卡部分下，单击“用户”。

    ![添加员工](./media/kantegassoforbitbucket-tutorial/user2.png)

4. 单击“创建用户”。

    ![添加员工](./media/kantegassoforbitbucket-tutorial/user3.png)   

5. 在“创建用户”对话框页中，执行以下步骤：

    ![添加员工](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“用户名”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。
    
    b. 在“全名”文本框中，键入用户（例如 Britta Simon）的全名。
    
    c. 在“电子邮件地址”文本框中，键入用户的电子邮件地址（例如 Brittasimon@contoso.com）。

    d. 在“密码”文本框中，键入用户的密码。  

    e. 在“确认密码”文本框中，再次输入用户密码。

    f. 单击“创建用户”。   

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Kantega SSO for Bitbucket 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

若要将 Britta Simon 分配到 Kantega SSO for Bitbucket，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Kantega SSO for Bitbucket”。

    ![配置单一登录](./media/kantegassoforbitbucket-tutorial/tutorial_kantegassoforbitbucket_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Kantega SSO for Bitbucket”磁贴就会自动登录到 Kantega SSO for Bitbucket 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforbitbucket-tutorial/tutorial_general_203.png

