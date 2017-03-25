---
title: "教程：Azure Active Directory 与 Evernote 的集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 与 Evernote 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: e4e2ca40ae270e7d3bfe4e828fcd571b5594ffc8
ms.lasthandoff: 03/09/2017


---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>教程：Azure Active Directory 与 Evernote 的集成

本教程介绍如何将 Evernote 与 Azure Active Directory (Azure AD) 集成。

将 Evernote 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Evernote
- 可让用户使用其 Azure AD 帐户自动登录到 Evernote（单一登录）
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Evernote 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Evernote 单一登录的订阅


> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。


测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。


## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Evernote
2. 配置和测试 Azure AD 单一登录


## <a name="adding-evernote-from-the-gallery"></a>从库中添加 Evernote
若要配置 Evernote 与 Azure AD 的集成，需要将库中的 Evernote 添加到托管的 SaaS 应用列表。

**若要从库中添加 Evernote，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入 **Evernote**。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search01.png)

5. 在结果窗格中，选择“Evernote”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_search_result01.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Evernote 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Evernote 用户。 换句话说，需要建立 Azure AD 用户与 Evernote 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值分配为 Evernote 中“用户名”的值来建立此链接关系。

若要通过 Evernote 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
4. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分将在 Azure 管理门户中启用 Azure AD 单一登录并在 Evernote 应用程序中配置单一登录。

**若要配置 Evernote 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 管理门户的“Evernote”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_samlbase.png)

3. 在“Evernote 域和 URL”部分中，如果要在“IDP 发起的模式”下配置应用程序，无需执行任何步骤。

    ![配置单一登录](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_idp.png)
    
4. 在“Evernote 域和 URL”部分中，如果要在“SP 发起的模式”下配置应用程序，请执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sp_01.png)
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“显示高级 URL 设置”选项

    b.保留“数据库类型”设置，即设置为“共享”。 在“登录 URL”文本框中，键入登录 URL：`https://www.evernote.com/Login.action`

5. 在“SAML 签名证书”部分中，单击“创建新证书”。

    ![配置单一登录](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_cert.png)     

6. 在“创建新证书”对话框中，单击日历图标，然后选择“到期日期”。 然后单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-evernote-tutorial/tutorial_general_300.png)

7. 在“SAML 签名证书”部分中，选择“激活新证书”，然后单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_makecertactive.png)

8. 在弹出的“滚动更新证书”窗口中，单击“确定”。

    ![配置单一登录](./media/active-directory-saas-evernote-tutorial/tutorial_general_400.png)

9. 在“SAML 签名证书”部分中，单击“证书(Base64)”，然后在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certificate.png) 

10. 在“Evernote 配置”部分，单击“配置 Evernote”打开“配置登录”窗口。

    ![配置单一登录](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configure.png) 

    ![配置单一登录](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configuresignon.png)

11. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Evernote 公司站点。

12. 转到“管理控制台”

    ![Admin-Console](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

13. 在“管理控制台”中，转到“安全性”并选择“单一登录”

    ![SSO-Setting](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sso.png)

14. 配置以下值：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  **启用 SSO：**SSO 默认已启用（单击“禁用单一登录”可以取消 SSO 要求）

    b.保留“数据库类型”设置，即设置为“共享”。 **SAML HTTP 请求 URL** - 输入 Azure AD 上的“配置 Evernote”部分中显示的“SAML 单一登录服务 URL”

    c. **X.509 证书** - 在记事本中打开从 Azure AD 下载的证书，并复制包括“BEGIN CERTIFICATE”和“END CERTIFICATE”在内的内容

    ![Certificate-Setting](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certx.png)

    d. 单击“保存更改” 


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-evernote-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-evernote-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-evernote-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-evernote-tutorial/create_aaduser_04.png) 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。 



### <a name="creating-an-evernote-test-user"></a>创建 Evernote 测试用户

为了使 Azure AD 用户能够登录到 Evernote，必须将其预配到 Evernote 中。  
对于 Evernote，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 Evernote 公司站点。

2. 单击“管理控制台”。

    ![Admin-Console](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

3. 在“管理控制台”中，转到“添加用户”。

    ![Add-testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0001.png)

4. 在“电子邮件”文本框中的“添加团队成员”内，键入用户帐户的电子邮件地址，然后单击“邀请”。

    ![Add-testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0002.png)
    
5. 发送邀请后，Azure Active Directory 帐户持有人将收到邀请电子邮件。   


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分将通过向 Britta Simon 授予对 Evernote 的访问权限使她能够使用 Azure 单一登录。

![分配用户][200] 

**若要将 Britta Simon 分配到 Evernote，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，然后单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Evernote”。

    ![配置单一登录](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    


### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Evernote 磁贴时，应会登录到 Evernote 应用程序。 一开始将以组织帐户登录，但随后需要使用个人帐户登录。


## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_203.png

