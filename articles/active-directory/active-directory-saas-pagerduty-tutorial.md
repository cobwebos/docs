---
title: "教程：Azure Active Directory 与 PagerDuty 集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 与 PagerDuty 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: a5a2e3a9d8aa32894e07fb8e96389167d5bb96f9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>教程：Azure Active Directory 与 PagerDuty 集成

在本教程中，了解如何将 PagerDuty 与 Azure Active Directory (Azure AD) 集成。

将 PagerDuty 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 PagerDuty
- 可以让用户使用其 Azure AD 帐户自动登录到 PagerDuty（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 PagerDuty 的集成，需要以下项：

- Azure AD 订阅
- 已启用 PagerDuty 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 PagerDuty
2. 配置和测试 Azure AD 单一登录

## <a name="adding-pagerduty-from-the-gallery"></a>从库添加 PagerDuty
若要配置 PagerDuty 与 Azure AD 的集成，需要从库中将 PagerDuty 添加到托管 SaaS 应用列表。

**若要从库中添加 PagerDuty，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

4. 在搜索框中键入 **PagerDuty**，在结果面板中选择“PagerDuty”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 PagerDuty 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 PagerDuty 用户。 换句话说，需要建立 Azure AD 用户与 PagerDuty 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值指定为 PagerDuty 中“用户名”的值来建立此链接关系。

若要配置和测试 PagerDuty 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 PagerDuty 测试用户](#create-a-pagerduty-test-user)** - 在 PagerDuty 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将介绍如何在 Azure 门户中启用 Azure AD 单一登录并在 PagerDuty 应用程序中配置单一登录。

**若要配置 PagerDuty 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **PagerDuty** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_samlbase.png)

3. 在“PagerDuty 域和 URL”部分中，执行以下步骤：

    ![PagerDuty 域和 URL 单一登录信息](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenant-name>.pagerduty.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<tenant-name>.pagerduty.com`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [PagerDuty 客户端支持团队](https://www.pagerduty.com/support/)获取这些值。 

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-pagerduty-tutorial/tutorial_general_400.png)

6. 在“PagerDuty 配置”部分，单击“配置 PagerDuty”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![PagerDuty 配置](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_configure.png) 

7. 在另一 Web 浏览器窗口中，以管理员身份登录到 Pagerduty 公司站点。

8. 在顶部菜单中，单击“帐户设置”。
   
    ![帐户设置](./media/active-directory-saas-pagerduty-tutorial/ic778535.png "帐户设置")

9. 单击“单一登录”。
   
    ![单一登录](./media/active-directory-saas-pagerduty-tutorial/ic778536.png "单一登录")

10. 在“启用单一登录(SSO)”页上，执行以下步骤：
   
    ![启用单一登录](./media/active-directory-saas-pagerduty-tutorial/ic778537.png "启用单一登录")
   
    a. 在记事本中打开从 Azure 门户下载的 base-64 编码的证书，将其内容复制到剪贴板，再将其粘贴到“X.509 证书”文本框中
  
    b. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”。
  
    c. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”。
 
    d.单击“下一步”。 选择“启用单一登录”。
 
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 单击“保存更改”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![“添加”按钮](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![“用户”对话框](./media/active-directory-saas-pagerduty-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="create-a-pagerduty-test-user"></a>创建 PagerDuty 测试用户

若要让 Azure AD 用户登录 PagerDuty，必须将其预配到 PagerDuty 中。  
使用 PagerDuty 时，预配属手动任务。

>[!NOTE]
>可以使用任何其他 PagerDuty 用户帐户创建工具或 PagerDuty 提供的 API 来预配 Azure Active Directory 用户帐户。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **Pagerduty** 租户。

2. 在顶部菜单中，单击“用户”。

3. 单击“添加用户”。
   
    ![添加用户](./media/active-directory-saas-pagerduty-tutorial/ic778539.png "添加用户")

4.  在“邀请团队”对话框中，执行以下步骤：
   
    ![邀请团队](./media/active-directory-saas-pagerduty-tutorial/ic778540.png "邀请团队")

    a. 键入用户的**名字和姓氏**，例如 **Britta Simon**。 
   
    b. 输入用户的**电子邮件**地址，例如 **brittasimon@contoso.com**。
   
    c. 依次单击“添加”、“发送邀请”。
   
    >[!NOTE]
    >所有已添加的用户都会收到创建 PagerDuty 帐户的邀请。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 PagerDuty 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200]

**若要将 Britta Simon 分配到 PagerDuty，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“PagerDuty”。

    ![应用程序列表中的 PagerDuty 链接](./media/active-directory-saas-pagerduty-tutorial/tutorial_pagerduty_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 PagerDuty 磁贴时，应当会自动登录到 PagerDuty 应用程序。

有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pagerduty-tutorial/tutorial_general_203.png

