---
title: "教程：Azure Active Directory 与 Cezanne HR Software 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 和 Cezanne HR Software 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: fb8f95bf-c3c1-4e1f-b2b3-3b67526be72d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 623c438edfce5f98c2d32d8bb25a97d86aa77909
ms.contentlocale: zh-cn
ms.lasthandoff: 08/10/2017

---
# <a name="tutorial-integrate-azure-active-directory-with-cezanne-hr-software"></a>教程：Azure Active Directory 与 Cezanne HR Software 的集成

在本教程中，了解如何将 Cezanne HR Software 与 Azure Active Directory (Azure AD) 集成。

将 Cezanne HR Software 与 Azure AD 集成可提供以下优势。 可以：

- 在 Azure AD 中控制谁有权访问 Cezanne HR Software。
- 让用户使用其 Azure AD 帐户自动登录到 Cezanne HR Software 单一登录 (SSO)。
- 在一个中心位置（Azure 门户）管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 的集成，请参阅 [Azure Active Directory 的应用程序访问与 SSO 是什么？](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Cezanne HR Software 的集成，需要以下项：

- 一个 Azure AD 订阅
- 已启用 Cezanne HR Software SSO 的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，会在测试环境中测试 Azure AD SSO。 

本教程中概述的方案包括两个主要构建基块：

* 从库中添加 Cezanne HR Software
* 配置和测试 Azure AD SSO

## <a name="add-cezanne-hr-software-from-the-gallery"></a>从库中添加 Cezanne HR Software
若要配置 Cezanne HR Software 与 Azure AD 的集成，需从库中将 Cezanne HR Software 添加到托管 SaaS 应用列表。

若要从库中添加 Cezanne HR Software，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”按钮。 

    ![“Azure Active Directory”按钮][1]

2. 选择“企业应用程序” > “所有应用程序”。

    ![“所有应用程序”链接][2]
    
3. 若要添加新应用程序，请在“所有应用程序”对话框顶部，选择“新增应用程序”。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Cezanne HR Software”。

    ![搜索框](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_search.png)

5. 在结果列表中，选择“Cezanne HR Software”，并单击“添加”按钮添加该应用程序。

    ![结果列表](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户配置和测试 Cezanne HR Software 的 Azure AD SSO。

若要允许 SSO，Azure AD 需要知道与 Azure AD 用户相对应的 Cezanne HR Software 用户。 换句话说，必须在 Azure AD 用户与 Cezanne HR Software 中的相关用户之间建立链接关系。

若要建立链接关系，请将 Cezanne HR software“用户名”值指定为 Azure AD“用户名”值。

若要使用 Cezanne HR Software 来配置和测试 Azure AD SSO，请完成以下构建基块。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在本部分，可通过以下操作，在 Azure 门户中启用 Azure AD SSO，并在 Cezanne HR Software 应用程序中配置 SSO：

1. 在 Azure 门户中的 Cezanne HR Software 应用程序集成页上，选择“单一登录”。

    ![“单一登录”命令][4]

2. 若要启用 SSO，请在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”。
 
    ![“模式”框](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_samlbase.png)

3. 在“Cezanne HR Software 域和 URL”下，执行以下操作：

    ![“Cezanne HR Software 域和 URL”部分](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_url.png)

    a. 在“登录 URL”框中，键入具有以下语法的 URL：`https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`

    b. 在“回复 URL”框中，键入具有以下语法的 URL：`https://w3.cezanneondemand.com:443/<tenantid>`    
     
    > [!NOTE] 
    > 上面的值不是实际值。 请使用实际的回复 URL 和登录 URL 更新这些值。 若要获取这些值，请联系 [Cezanne HR Software 客户端支持团队](mailto:info@cezannehr.com)。

4. 在“SAML 签名证书”下，选择“证书(Base64)”，然后将证书文件保存到计算机。

    ![“SAML 签名证书”部分](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_certificate.png) 

5. 选择“保存”。

    ![“保存”按钮](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_400.png)
    
6. 在“Cezanne HR Software 配置”下，选择“配置 Cezanne HR Software”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![“Cezanne HR Software 配置”部分](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_configure.png) 

7. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Cezanne HR Software 租户。

8. 在左窗格中，选择“系统设置”。 选择“安全设置” > “单一登录配置”。

    ![“单一登录配置”链接](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

9. 在“允许用户使用以下单一登录 (SSO) 服务进行登录”面板中，选择“SAML 2.0”复选框并选择“高级配置”选项。

    ![单一登录服务选项](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

10. 选择“新增”。

    ![“新增”按钮](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

11. 在“SAML 2.0 标识提供者”下，执行下列操作：

    ![“SAML 2.0 标识提供者”部分](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)
    
    a. 在“显示名称”框中，输入标识提供者的名称。

    b. 在“实体标识符”框中，粘贴从 Azure 门户复制的“SAML 实体 ID”。 

    c. 在“SAML 绑定”列表框中，选择 “POST”。

    d.单击“下一步”。 在“安全令牌服务终结点”框中，粘贴从 Azure 门户复制的“SAML 单一登录服务”URL。 
    
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 在“用户 ID 属性名称”框中，输入 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`。
    
    f. 若要从 Azure AD 上传已下载的证书，请选择“上传”按钮。
    
    g. 选择“确定”。 

12. 选择“保存”。

    ![“保存”按钮](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

> [!TIP]
> 设置应用时，可以在 [Azure 门户](https://portal.azure.com)中阅读前述教程的简洁版本。 从“Active Directory” > “企业应用程序”部分添加应用后，请选择“单一登录”选项卡。然后从“配置”部分访问嵌入式文档。 

可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)。
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，将在 Azure 门户中创建一个名为 Britta Simon 的测试用户。

![测试用户 Britta Simon][100]

若要在 Azure AD 中创建测试用户，请执行以下步骤：

1. 在“Azure 门户”的左窗格中，选择“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请选择“用户和组” > “所有用户”。
    
    ![“所有用户”链接](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_02.png) 
    
    “所有用户”对话框随即打开。

3. 若要打开“用户”对话框，请选择“添加”。
 
    ![“添加”按钮](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框中执行以下操作：
 
    ![“用户”对话框](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png) 

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选择“显示密码”复选框，然后记下“密码”框中生成的值。

    d.单击“下一步”。 选择“创建” 。
 
### <a name="create-a-cezanne-hr-software-test-user"></a>创建 Cezanne HR Software 测试用户

为了使 Azure AD 用户能够登录到 Cezanne HR Software，必须将其预配到 Cezanne HR Software 中。 对于 Cezanne HR Software，需要手动执行预配。

通过执行以下操作预配用户帐户：

1.  以管理员身份登录到 Cezanne HR Software 公司站点。

2.  在左窗格中，依次选择“系统设置” > “管理用户” > “添加新用户”。

    ![“添加新用户”链接](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "新用户")

3.  在“人员详细信息”下，执行下列操作：

    ![“人员详细信息”部分](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "新用户")
    
    a. 将“内部用户”设置为“关闭”。
    
    b. 在“名字”框中，键入用户的名，例如“Britta”。  
 
    c. 在“姓氏”框中，键入用户的姓，例如“Simon”。
    
    d.单击“下一步”。 在“电子邮件”框中，键入用户的电子邮件地址，例如 Brittasimon@contoso.com。

4.  在“帐户信息”下，执行下列操作：

    ![“帐户信息”部分](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "新用户")
    
    a. 在“用户名”框中，键入用户的电子邮件地址，例如 Brittasimon@contoso.com。
    
    b. 在“密码”框中，键入用户的密码。
    
    c. 在“安全角色”框中，选择“HR 专业人员”。
    
    d.单击“下一步”。 选择“确定”。

5. 在“单一登录”选项卡的“SAML 2.0 标识符”部分中，选择“新增”。

    ![“新增”按钮](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "用户")

6. 在“标识提供者”列表框中，选择你的标识提供者。 在“用户标识符”框中，输入测试用户 Britta Simon 帐户的电子邮件地址。

    ![“标识提供者”和“用户标识符”框](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "用户")
    
7. 选择“保存”。

    ![“保存”按钮](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "用户")

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予测试用户 Britta Simon 访问 Cezanne HR Software 的权限，允许其使用 Azure SSO。

![测试用户访问][200] 

1. 在 Azure 门户中，打开应用程序视图，然后转到目录视图。 选择“企业应用程序” > “所有应用程序”。

    ![“所有应用程序”链接][201] 

2. 在应用程序列表中，选择“Cezanne HR Software”。

    ![“应用程序”列表](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_app.png) 

3. 在左侧菜单中，选择“用户和组”。

    ![分配用户][202] 

4. 选择“添加”。 然后，在“添加分配”对话框中选择“用户和组”。

    ![“用户和组”链接][203]

5. 在“用户和组”对话框中的“用户”列表内，选择“Britta Simon”。

6. 在“用户和组”对话框中，选择“选择”。

7. 在“添加分配”对话框中选择“分配”。
    
### <a name="test-sso"></a>测试 SSO

在本部分，我们将使用访问面板测试 Azure AD SSO 配置。

在访问面板中选择 Cezanne HR Software 磁贴时，将自动登录到 Cezanne HR Software 应用程序。

## <a name="next-steps"></a>后续步骤

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与 SSO 是什么？](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png


