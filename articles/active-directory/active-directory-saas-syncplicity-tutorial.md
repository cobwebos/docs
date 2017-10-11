---
title: "教程：Azure Active Directory 与 Syncplicity 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 与 Syncplicity 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 1321fa71bcd625d6ea754432bfb402d3919e38f3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>教程：Azure Active Directory 与 Syncplicity 集成

本教程介绍如何将 Syncplicity 与 Azure Active Directory (Azure AD) 集成。

将 Syncplicity 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Syncplicity
- 可以让用户使用其 Azure AD 帐户自动登录到 Syncplicity（单一登录）
- 可以在一个中心位置（即 Azure 门户）中管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Syncplicity 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 Syncplicity 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Syncplicity
2. 配置和测试 Azure AD 单一登录

## <a name="adding-syncplicity-from-the-gallery"></a>从库添加 Syncplicity
若要配置 Syncplicity 与 Azure AD 的集成，需要从库中将 Syncplicity 添加到托管 SaaS 应用列表。

**若要从库添加 Syncplicity，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **Syncplicity**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_search.png)

5. 在结果窗格中选择“Syncplicity”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分，将基于名为“Britta Simon”的测试用户配置并测试 Syncplicity 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Syncplicity 用户。 换句话说，需要建立 Azure AD 用户与 Syncplicity 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Syncplicity 中“用户名”的值来建立此链接关系。

若要配置和测试 Syncplicity 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Syncplicity 测试用户](#creating-a-syncplicity-test-user)** -若要在 Syncplicity 链接到用户的 Azure AD 表示具有 Britta 人 Simon 的副本。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，将在 Azure 门户中启用 Azure AD 单一登录并在 Syncplicity 应用程序中配置单一登录。

**若要配置 Syncplicity 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Syncplicity”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

3. 在“Syncplicity 域和 URL”部分中执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.syncplicity.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Syncplicity 客户端支持团队](https://www.syncplicity.com/contact-us)获取这些值。 
 

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-syncplicity-tutorial/tutorial_general_400.png)

6. 在“Syncplicity 配置”部分，单击“配置 Syncplicity”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_configure.png) 

7. 登录到 **Syncplicity** 租户。

8. 在顶部菜单中，单击“管理员”，选择“设置”，并单击“自定义域和单一登录”。
   
    ![Syncplicity](./media/active-directory-saas-syncplicity-tutorial/ic769545.png "Syncplicity")

9. 在“单一登录(SSO)”对话框页上，执行以下步骤：
   
    ![单一登录\(\)SSO](./media/active-directory-saas-syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. 在“自定义域”文本框中，键入域名。
  
    b. 选择“已启用”作为“单一登录状态”。

    c. 将从 Azure 门户复制的“SAML 实体 ID”值粘贴到“实体 ID”文本框中。

    d. 在“登录页 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”。

    e. 在“注销页 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”。

    f. 在“标识提供者证书”中单击“选择文件”，然后上传已从 Azure 门户下载的证书。 

    g. 单击“保存更改”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-syncplicity-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-syncplicity-test-user"></a>创建 Syncplicity 测试用户
要使 AAD 用户能够登录，必须将这些用户预配到 Syncplicity 应用程序中。 本部分介绍如何在 Syncplicity 中创建 AAD 用户帐户。

**要将用户帐户预配到 Syncplicity，请执行以下步骤：**

1. 登录到 **Syncplicity** 租户（例如：`https://company.Syncplicity.com`）。

2. 单击“管理员”，然后选择“用户帐户”。

3. 单击“添加用户”。
   
    ![管理用户](./media/active-directory-saas-syncplicity-tutorial/ic769764.png "管理用户")

4. 键入要预配的 AAD 帐户的**电子邮件地址**，为“角色”选择“用户”，然后单击“下一步”。
   
    ![帐户信息](./media/active-directory-saas-syncplicity-tutorial/ic769765.png "帐户信息")
   
    >[!NOTE]
    >AAD 帐户持有者将收到一封电子邮件，其中包含用于确认和激活帐户的链接。 
    > 

5. 选择新用户应属于你的公司中的哪个组，然后单击“下一步”。
   
    ![组成员身份](./media/active-directory-saas-syncplicity-tutorial/ic769772.png "组成员身份")
   
    >[!NOTE]
    >如果没有列出任何组，请单击“下一步”。 
    > 

6. 选择你想要在用户的计算机上的 Syncplicity 控制之下，然后单击的文件夹**下一步**。
   
    ![Syncplicity 文件夹](./media/active-directory-saas-syncplicity-tutorial/ic769773.png "Syncplicity 文件夹")

>[!NOTE]
>可以使用 Syncplicity 提供的任何其他 Syncplicity 用户帐户创建工具或 API 来预配 AAD 用户帐户。 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，将通过授予 Britta Simon 访问 Syncplicity 的权限，使其能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Syncplicity，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Syncplicity”。

    ![配置单一登录](./media/active-directory-saas-syncplicity-tutorial/tutorial_syncplicity_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分的目的是使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Syncplicity 磁贴时，应会自动登录到 Syncplicity 应用程序。
## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-syncplicity-tutorial/tutorial_general_203.png

