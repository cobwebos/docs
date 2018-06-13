---
title: 教程：Azure Active Directory 与 OrgChart Now 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 OrgChart Now 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.openlocfilehash: c46a2e68699365d818c83be2b1b0ff2ce2ef88ab
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34349193"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>教程：Azure Active Directory 与 OrgChart Now 集成

本教程介绍如何将 OrgChart Now 与 Azure Active Directory (Azure AD) 集成。

将 OrgChart Now 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 OrgChart Now。
- 可以让用户通过其 Azure AD 帐户自动登录到 OrgChart Now（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 OrgChart Now 的集成，需要准备好以下各项：

- Azure AD 订阅
- 已启用 OrgChart Now 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 OrgChart Now
2. 配置和测试 Azure AD 单一登录

## <a name="adding-orgchart-now-from-the-gallery"></a>从库中添加 OrgChart Now
要配置 OrgChart Now 与 Azure AD 的集成，需要从库中将 OrgChart Now 添加到托管 SaaS 应用列表。

**若要从库中添加 OrgChart Now，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“OrgChart Now”，在结果面板中选择“OrgChart Now”，然后单击“添加”按钮添加应用程序。

    ![结果列表中的 OrgChart Now](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 OrgChart Now 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 OrgChart Now 用户。 换句话说，需要建立 Azure AD 用户与 OrgChart Now 中相关用户之间的链接关系。

若要配置和测试 OrgChart Now 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 OrgChart Now 测试用户](#create-an-orgchart-now-test-user)** - 在 OrgChart Now 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 OrgChart Now 应用程序中配置单一登录。

**若要配置 OrgChart Now 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 OrgChart Now 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_samlbase.png)

3. 在“OrgChart Now 域和 URL”部分中，如果要在“IDP 启动的模式”下配置应用程序，请执行以下步骤：

    ![OrgChart Now 域和 URL 单一登录信息](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_url.png)

    在“标识符”文本框中，键入一个 URL：`https://sso2.orgchartnow.com`

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![OrgChart Now 域和 URL 单一登录信息](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`
     
    > [!NOTE]
    > `<YourEntityID>` 是从教程下文中介绍的“快速参考”部分中复制的 SAML 实体 ID。

5. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_400.png)
    
7. 在“OrgChart Now 配置”部分中，单击“配置 OrgChart Now”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID”，并使用它完成“OrgChart Now 域和 URL”部分中的“登录 URL”。

    ![OrgChart Now 配置](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_configure.png) 

8. 若要在 **OrgChart Now** 端配置单一登录，需要将下载的**元数据 XML** 发送给 [OrgChart Now 支持团队](mailto:ocnsupport@officeworksoftware.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-orgchartnow-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-an-orgchart-now-test-user"></a>创建 OrgChart Now 测试用户

若要让 Azure AD 用户登录 OrgChart Now，必须将其预配到 OrgChart Now 中。 

1. OrgChart Now 支持在默认情况下启用的实时预配。 尝试访问 OrgChart Now 期间，如果尚不存在用户，则会创建一个新用户。 仅当 SSO 请求来自已识别的 IDP，并且在用户列表中未找到 SAML 断言中的电子邮件时，实时用户预配功能才会创建**只读**用户。 对于此自动预配功能，需要在 OrgChart Now 中创建标题为“常规”的访问组。 请遵循以下步骤创建访问组：

    a. 单击 UI 右上角的**齿轮**图标后，转到“管理组”选项。

    ![OrgChart Now 组](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_manage.png)  

    b. 选择“添加”图标，将组命名为“常规”，然后单击“确定”。 

    ![OrgChart Now 添加](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. 选择常规用户或只读用户能够访问的文件夹：

    ![OrgChart Now 文件夹](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **锁定**文件夹，以便只有管理员用户可以修改它们。 然后按“确定”。

    ![OrgChart Now 锁定](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. 若要创建**管理员**用户和**读/写**用户，必须手动创建用户，以通过 SSO 获取对其特权级别的访问。 若要预配用户帐户，请执行以下步骤：

    a. 以安全管理员身份登录到 OrgChart Now。

    b.  单击右上角的“设置”，然后导航到“管理用户”。

    ![OrgChart Now 设置](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. 单击“添加”并执行以下步骤：

    ![OrgChart Now 管理](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * 在“用户 ID”文本框中输入用户 ID，例如 **brittasimon@contoso.com**。

    * 在“电子邮件地址”文本框中，输入用户的电子邮件地址，例如 **brittasimon@contoso.com**。

    * 单击 **“添加”**。
    
### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 OrgChart Now 的权限，使其能够使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 OrgChart Now，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“OrgChart Now”。

    ![应用程序列表中的 OrgChart Now 链接](./media/active-directory-saas-orgchartnow-tutorial/tutorial_orgchartnow_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“OrgChart Now”磁贴时，应会自动登录到 OrgChart Now 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-orgchartnow-tutorial/tutorial_general_203.png

