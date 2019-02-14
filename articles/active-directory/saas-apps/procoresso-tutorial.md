---
title: 教程：Azure Active Directory 与 Procore SSO 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Procore SSO 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d72f4ac39ac992d0386b3971cd4d3bc6616f0a28
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56186448"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>教程：Azure Active Directory 与 Procore SSO 的集成

在本教程中，了解如何将 Procore SSO 与 Azure Active Directory (Azure AD) 集成。

将 Procore SSO 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Procore SSO。
- 可让用户通过其 Azure AD 帐户自动登录到 Procore SSO（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Procore SSO 的集成，需要以下项：

- Azure AD 订阅
- 启用了单一登录的 Procore SSO 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Procore SSO
2. 配置和测试 Azure AD 单一登录

## <a name="adding-procore-sso-from-the-gallery"></a>从库添加 Procore SSO

要配置 Procore SSO 与 Azure AD 的集成，需要从库中将 Procore SSO 添加到托管 SaaS 应用列表。

**若要从库中添加 Procore SSO，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“Procore SSO”，在结果面板中选择“Procore SSO”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Procore SSO](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分需根据名为“Britta Simon”的测试用户的情况，配置和测试 Procore SSO 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Procore SSO 用户。 换句话说，需要建立 Azure AD 用户与 Procore SSO 中相关用户之间的链接关系。

若要配置和测试 Procore SSO 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Procore SSO 测试用户](#creating-a-procore-sso-test-user)** - 在 Procore SSO 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Procore SSO 应用程序中配置单一登录。

**若要配置 Procore SSO 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **Procore SSO** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](common/tutorial_general_301.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](common/editconfigure.png)

4. 在“基本 SAML 配置”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成。

    ![Procore SSO 域和 URL 单一登录信息](./media/procoresso-tutorial/tutorial_procoresso_url.png)

5. 在“SAML 签名证书”页的“SAML 签名证书”部分，单击“下载”以下载“联合元数据 XML”并将元数据文件保存在计算机上。

    ![证书下载链接](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

6. 在“设置 Procore SSO”部分中，根据你的要求复制相应的 URL。

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![Procore SSO 配置](common/configuresection.png)

7. 若要在 **Procore SSO**端配置单一登录，请以管理员身份登录 procore 公司站点。

8. 在工具箱下拉菜单中，单击“管理员”以打开 SSO 设置页。

    ![配置单一登录](./media/procoresso-tutorial/procore_tool_admin.png)

9. 按如下所述在框中粘贴值-

    ![配置单一登录](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. 在“单一登录颁发者 URL”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。

    b. 在“SAML 登录目标 URL”框中，粘贴从 Azure 门户复制的“登录 URL”值。

    c. 现在打开上面从 Azure 门户下载的**联合元数据 XML**，并复制名为 **X509Certificate** 的标记中的证书。 将复制的值粘贴到“单一登录 x509 证书”框中。

10. 单击“保存更改”。

11. 进行这些设置之后，需要将用于登录到 Procore 的**域名**（例如 **contoso.com**）发送给 [Procore 支持团队](https://support.procore.com/)，他们会为该域激活联合 SSO。

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](common/create_aaduser_01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](common/create_aaduser_02.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
       例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。

### <a name="creating-a-procore-sso-test-user"></a>创建 Procore SSO 测试用户

请按照以下步骤在 Procore SSO 端创建一个 Procore 测试用户。

1. 以管理员身份登录到 procore 公司站点。  

2. 从工具箱下拉菜单中，单击“目录”以打开公司目录页。

    ![配置单一登录](./media/procoresso-tutorial/Procore_sso_directory.png)

3. 单击 **“添加人员”** 选项以打开窗体，然后执行以下选项 -

    ![配置单一登录](./media/procoresso-tutorial/Procore_user_add.png)

    a. 在“名字”文本框中，输入用户的名字（如 **Britta**）。

    b. 在“姓氏”文本框中，输入用户的姓氏（如 **Simon**）。

    c. 在“电子邮件地址”文本框中，输入用户的电子邮件地址（如 **BrittaSimon@contoso.com**）。

    d. 对“权限模板”选择“稍后应用权限模板”。

    e. 单击“创建”。

4. 检查并更新新添加的联系人的详细信息。

    ![配置单一登录](./media/procoresso-tutorial/Procore_user_check.png)

5. 单击“保存和发送邀请”（如果需要通过邮件进行邀请）或“保存”（直接保存）以完成用户注册。
    
    ![配置单一登录](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予对 Procore SSO 的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Procore SSO”。

    ![配置单一登录](./media/procoresso-tutorial/tutorial_procoresso_app.png)

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Procore SSO 磁贴时，应当会自动登录到 Procore SSO 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
