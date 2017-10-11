---
title: "教程：Azure Active Directory 与 Procore SSO 集成 | Microsoft 文档"
description: "了解如何在 Azure Active Directory 和 Procore SSO 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: 042a41eaa6bb21670b4c12068f1b017222f64b99
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>教程：Azure Active Directory 与 Procore SSO 的集成

在本教程中，了解如何将 Procore SSO 与 Azure Active Directory (Azure AD) 集成。

将 Procore SSO 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Procore SSO
- 可让用户通过其 Azure AD 帐户自动登录到 Procore SSO（单一登录）
- 可在一个中心位置（即 Azure 管理门户）管理帐户

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](active-directory-appssoaccess-whatis.md)。

<!--## Overview

To enable single sign-on with Procore SSO, it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Procore SSO.

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Procore SSO 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Procore SSO 单一登录的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

测试本教程中的步骤应遵循以下建议：

- 不应使用生产环境，除非有此必要。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Procore SSO
2. 配置并测试 Azure AD 单一登录

## <a name="adding-procore-sso-from-the-gallery"></a>从库添加 Procore SSO
要配置 Procore SSO 与 Azure AD 的集成，需要从库中将 Procore SSO 添加到托管 SaaS 应用列表。

**若要从库中添加 Procore SSO，请执行以下步骤：**

1. 在 **[Azure 管理门户](https://portal.azure.com)**的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 单击对话框顶部的“添加”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Procore SSO”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_search.png)

5. 在结果窗格中，选择“Procore SSO”，并单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
本部分需根据名为“Britta Simon”的测试用户的情况，配置和测试 Procore SSO 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Procore SSO 用户。 换句话说，需要建立 Azure AD 用户与 Procore SSO 中相关用户之间的链接关系。

通过将 Azure AD 中“用户名”的值分配为 Procore SSO 中“用户名”的值来建立此链接关系。

若要配置和测试 Procore SSO 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Procore SSO 测试用户](#creating-a-procore-sso-test-user)** -若要链接到的 Azure AD 表示形式她的 Procore sso 具有 Britta 人 Simon 的副本。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分会在 Azure 管理门户中启用 Azure AD 单一登录并在 Procore SSO 应用程序中配置单一登录。

**若要配置 Procore SSO 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 管理门户的“Procore SSO”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_samlbase.png)

3. 在“Procore SSO 域和 URL”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成。

    ![配置单一登录](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_url.png)

4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存 XML 文件。

    ![配置单一登录](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-procoresso-tutorial/tutorial_general_400.png)

6. 在“Procore SSO 配置”部分，单击“配置 Procore SSO”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_configure.png) 

7. 若要在 **Procore SSO**端配置单一登录，请以管理员身份登录 procore 公司站点。

8. 在工具箱下拉菜单中，单击“管理员”以打开 SSO 设置页。

    ![配置单一登录](./media/active-directory-saas-procoresso-tutorial/procore_tool_admin.png)

9. 按如下所述在框中粘贴值-

    ![配置单一登录](./media/active-directory-saas-procoresso-tutorial/procore_setting_admin.png)    

    a. 在“单一登录颁发者 URL”中，粘贴从 Azure 门户复制的 SAML 实体 ID。

    b. 在“SAML 单一登录目标 URL”中，粘贴从 Azure 门户复制的 SAML 单一登录服务 URL。

    c. 现在打开上面从 Azure 门户下载的**元数据 XML**，并复制名为 **X509Certificate** 的标记中的证书。 将复制的值粘贴到“单一登录 x509 证书”框中。

10. 单击“保存更改”。

11. 进行这些设置之后，需要将用于登录到 Procore 的**域名**（例如 **contoso.com**）发送给 [Procore 支持团队](https://support.procore.com/)，他们会为该域激活联合 SSO。

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 管理门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-procoresso-tutorial/create_aaduser_01.png) 

2. 转到“用户和组”，单击“所有用户”显示用户列表。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-procoresso-tutorial/create_aaduser_02.png) 

3. 在对话框顶部单击“添加”，打开“用户”对话框。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-procoresso-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-procoresso-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b.保留“数据库类型”设置，即设置为“共享”。 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d.单击“下一步”。 单击“创建” 。
 
### <a name="creating-a-procore-sso-test-user"></a>创建 Procore SSO 测试用户

请按照以下步骤在其端上创建 Procore 测试用户。

1. 以管理员身份登录到 procore 公司站点。  

2. 从工具箱下拉菜单中，单击“目录”以打开公司目录页。

    ![配置单一登录](./media/active-directory-saas-procoresso-tutorial/Procore_sso_directory.png)

3. 单击**“添加人员”**选项以打开窗体，然后执行以下选项 -

    ![配置单一登录](./media/active-directory-saas-procoresso-tutorial/Procore_user_add.png)

    a. 在“名字”文本框中，输入用户的名字（如 **Britta**）。

    b. 在“姓氏”文本框中，输入用户的姓氏（如 **Simon**）。

    c. 在“电子邮件地址”文本框中，输入用户的电子邮件地址（如 **BrittaSimon@contoso.com**）。

    d.单击“下一步”。 对“权限模板”选择“稍后应用权限模板”。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，并单击“确定”。 单击“创建” 。

4. 检查并更新新添加的联系人的详细信息。

    ![配置单一登录](./media/active-directory-saas-procoresso-tutorial/Procore_user_check.png)

5. 单击“保存和发送邀请”（如果需要通过邮件进行邀请）或“保存”（直接保存）以完成用户注册。
    
    ![配置单一登录](./media/active-directory-saas-procoresso-tutorial/Procore_user_save.png)    

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Procore SSO 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Procore SSO，请执行以下步骤：**

1. 在 Azure 管理门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Procore SSO”。

    ![配置单一登录](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://msdn.microsoft.com/library/dn308586)（访问面板简介）。 当在访问面板中单击 Procore SSO 磁贴时，应当会自动登录到 Procore SSO 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_203.png

