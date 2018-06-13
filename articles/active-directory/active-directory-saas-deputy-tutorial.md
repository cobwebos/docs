---
title: 教程：Azure Active Directory 与 Deputy 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Deputy 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: b4e1239855756690be7d6c4d1c91eb58ce2aa11f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34340839"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>教程：Azure Active Directory 与 Deputy 的集成

本教程介绍如何将 Deputy 与 Azure Active Directory (Azure AD) 集成。

将 Deputy 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Deputy
- 可以让用户使用其 Azure AD 帐户自动登录到 Deputy（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Deputy 的集成，需要具有以下项：

- Azure AD 订阅
- 已启用 Deputy 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Deputy
2. 配置和测试 Azure AD 单一登录

## <a name="adding-deputy-from-the-gallery"></a>从库中添加 Deputy
要配置 Deputy 与 Azure AD 的集成，需要从库中将 Deputy 添加到托管 SaaS 应用列表。

**若要从库中添加 Deputy，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Deputy”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_search.png)

5. 在结果面板中，选择“Deputy”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分将基于一个名为“Britta Simon”的测试用户配置和测试 Deputy 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Deputy 用户。 换句话说，需要在 Azure AD 用户与 Deputy 中的相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值指定为 Deputy 中“用户名”的值来建立此链接关系。

若要配置并测试 Deputy 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Deputy 测试用户](#creating-a-deputy-test-user) - 在 Deputy 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Deputy 应用程序中配置单一登录。

**若要配置 Deputy 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Deputy”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_samlbase.png)

3. 在“Deputy 域和 URL”部分中，如果要在“IDP 发起的模式”下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_url1.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：
    |  |
    | ----|
    | `https://<subdomain>.<region>.au.deputy.com` |
    | `https://<subdomain>.<region>.ent-au.deputy.com` |
    | `https://<subdomain>.<region>.na.deputy.com`|
    | `https://<subdomain>.<region>.ent-na.deputy.com`|
    | `https://<subdomain>.<region>.eu.deputy.com` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com` |
    | `https://<subdomain>.<region>.as.deputy.com` |
    | `https://<subdomain>.<region>.ent-as.deputy.com` |
    | `https://<subdomain>.<region>.la.deputy.com` |
    | `https://<subdomain>.<region>.ent-la.deputy.com` |
    | `https://<subdomain>.<region>.af.deputy.com` |
    | `https://<subdomain>.<region>.ent-af.deputy.com` |
    | `https://<subdomain>.<region>.an.deputy.com` |
    | `https://<subdomain>.<region>.ent-an.deputy.com` |
    | `https://<subdomain>.<region>.deputy.com` |

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：
    | |
    |----|
    | `https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs.` |

4. 选中“显示高级 URL 设置”。 如果要在“SP”发起的模式下配置应用程序：

    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_url2.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<your-subdomain>.<region>.deputy.com`
    
    >[!NOTE]
    > Deputy 区域后缀是可选的，或者应当使用下列项之一：au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an

    > [!NOTE] 
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Deputy 支持团队](https://www.deputy.com/call-centers-customer-support-scheduling-software)获取这些值。 

5. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_general_400.png)
    
7. 在“Deputy 配置”部分，单击“配置 Deputy”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”

    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_configure.png) 

8. 导航到以下 URL：[https://(your-subdomain).deputy.com/exec/config/system_config]( https://(your-subdomain).deputy.com/exec/config/system_config)。 转到“安全设置”并单击“编辑”。
   
    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_004.png)

9. 在此“安全设置”页上，执行以下步骤。

    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_005.png)
    
    a. 启用**社交登录**。
   
    b. 在记事本中打开从 Azure 门户下载的 Base64 编码证书，将其内容复制到剪贴板，然后将其粘贴到“OpenSSL 证书”文本框。
   
    c. 在“SAML SSO URL”文本框中，键入 `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
    
    d. 在“SAML SSO URL”文本框中，将 `<your subdomain>` 替换为你的子域。
   
    e. 在“SAML SSO URL”文本框中，将 `<saml sso url>` 替换为从 Azure 门户中复制的“SAML 单一登录服务 URL”。
   
    f. 单击“保存设置”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-deputy-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-deputy-test-user"></a>创建 Deputy 测试用户

为了使 Azure AD 用户能够登录到 Deputy，必须将其预配到 Deputy 中。 对于 Deputy，需要手动执行预配。

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录到 Deputy 公司站点。

2. 在顶部的导航窗格中，单击“人员”。
   
   ![人员](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_001.png "人员")

3. 单击“添加人员”按钮，并单击“添加单个人员”。
   
   ![添加人员](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_002.png "添加人员")

4. 执行以下步骤并单击“保存并邀请”。
   
   ![新建用户](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_003.png "New User")

   a. 在“名称”文本框中，键入用户名称（例如“BrittaSimon”）。
   
   b. 在“电子邮件”文本框中，键入要预配的 Azure AD 帐户的电子邮件地址。
   
   c. 在“工作单位”文本框中，键入公司名称。
   
   d. 单击“保存并邀请”按钮。

5. AAD 帐户持有者将收到一封电子邮件，并打开其中的链接以在激活帐户前确认帐户。 可以使用 Deputy 提供的任何其他 Deputy 用户帐户创建工具或 API 来预配 AAD 用户帐户。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分将通过授予 Britta Simon 访问 Deputy 的权限，允许其使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Deputy，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Deputy”。

    ![配置单一登录](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD SSO 配置。

当在访问面板中单击 Deputy 磁贴时，应当会自动登录到 Deputy 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_203.png

