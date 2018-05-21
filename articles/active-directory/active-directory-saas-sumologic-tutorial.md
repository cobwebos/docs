---
title: 教程：Azure Active Directory 与 SumoLogic 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Sugar SumoLogic 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: 79db92a5ae9de6bb8f2d425c692f4d3e04ee1d3f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>教程：Azure Active Directory 与 SumoLogic 的集成

本教程介绍了如何将 SumoLogic 与 Azure Active Directory (Azure AD) 进行集成。

将 SumoLogic 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 SumoLogic
- 可以让用户使用其 Azure AD 帐户自动登录到 SumoLogic（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SumoLogic 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了单一登录的 SumoLogic 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 SumoLogic
2. 配置和测试 Azure AD 单一登录

## <a name="adding-sumologic-from-the-gallery"></a>从库中添加 SumoLogic
若要配置 SumoLogic 与 Azure AD 的集成，需要从库中将 SumoLogic 添加到托管 SaaS 应用列表。

**若要从库中添加 SumoLogic，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“SumoLogic”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_search.png)

5. 在结果窗格中，选择“SumoLogic”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 SumoLogic 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 SumoLogic 用户。 换句话说，需要在 Azure AD 用户与 SumoLogic 中的相关用户之间建立链接关系。

在 SumoLogic 中，通过将 Azure AD 中“用户名”的值指定为“用户名”的值来建立此链接关系。

若要配置和测试 SumoLogic 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 SumoLogic 测试用户](#creating-a-sumologic-test-user)** - 在 SumoLogic 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 SumoLogic 应用程序中配置单一登录。

**若要配置 SumoLogic 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **SumoLogic** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_samlbase.png)

3. 在“SumoLogic 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenantname>.SumoLogic.com`

    b. 在“标识符”文本框中，使用以下模式键入 URL：
    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [SumoLogic 客户端支持团队](https://www.sumologic.com/contact-us/)来获取这些值。 
 
4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-sumologic-tutorial/tutorial_general_400.png)

6. 在“SumoLogic 配置”部分中，单击“配置 SumoLogic”以打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_configure.png) 

7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 SumoLogic 公司站点。

8. 转到“管理”\>“安全性”。
   
    ![管理](./media/active-directory-saas-sumologic-tutorial/ic778556.png "管理")

9. 单击“SAML”。
   
    ![全局安全设置](./media/active-directory-saas-sumologic-tutorial/ic778557.png "全局安全设置")

10. 从“选择配置或创建新配置”列表中，选择“Azure AD”，并单击“配置”。
   
    ![配置 SAML 2.0](./media/active-directory-saas-sumologic-tutorial/ic778558.png "配置 SAML 2.0")

11. 在“配置 SAML 2.0”对话框上，执行以下步骤：
   
    ![配置 SAML 2.0](./media/active-directory-saas-sumologic-tutorial/ic778559.png "配置 SAML 2.0")
   
    a. 在“配置名称”文本框中，键入“Azure AD”。 

    b. 选择“调试模式”。

    c. 将从 Azure 门户复制的“SAML 实体 ID”的值粘贴到“颁发者”文本框中。 

    d. 在“身份验证请求 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    e. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将整个证书粘贴到“X.509 证书”文本框中。

    f. 对于“电子邮件属性”，选择“使用 SAML 主题”。  

    g. 选择“SP 发起的登录配置”。

    h. 在“登录路径”文本框中，键入“Azure”，并单击“保存”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-sumologic-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sumologic-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sumologic-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-sumologic-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-sumologic-test-user"></a>创建 SumoLogic 测试用户

要使 Azure AD 用户能够登录到 SumoLogic，必须将其预配到 SumoLogic 中。  

* 对于 SumoLogic，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **SumoLogic** 租户。

2. 转到“管理”\>“用户”。
   
    ![用户](./media/active-directory-saas-sumologic-tutorial/ic778561.png "用户")

3. 单击 **“添加”**。
   
    ![用户](./media/active-directory-saas-sumologic-tutorial/ic778562.png "用户")

4. 在“新建用户”对话框中，执行以下步骤：
   
    ![新建用户](./media/active-directory-saas-sumologic-tutorial/ic778563.png "New User") 
 
    a. 将要预配的 Azure AD 帐户的相关信息键入到“名字”、“姓氏”和“电子邮件”文本框中。
  
    b. 选择角色。
  
    c. 对于“状态”，选择“活动”。
  
    d. 单击“ **保存**”。

>[!NOTE]
>可以使用 SumoLogic 提供的任何其他 SumoLogic 用户帐户创建工具或 API 来预配 AAD 用户帐户。 
> 

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SumoLogic 的权限，允许其使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 SumoLogic，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“SumoLogic”。

    ![配置单一登录](./media/active-directory-saas-sumologic-tutorial/tutorial_sumologic_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD 单一登录配置。

当在访问面板中单击 SumoLogic 磁贴时，应当会自动登录到 SumoLogic 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sumologic-tutorial/tutorial_general_203.png

