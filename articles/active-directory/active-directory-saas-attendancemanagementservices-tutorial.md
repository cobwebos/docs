---
title: 教程：Azure Active Directory 与 Attendance Management Services 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Attendance Management Services 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.openlocfilehash: abf882d7b06083080ad5cb3c2a20390a76a48139
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>教程：Azure Active Directory 与 Attendance Management Services 的集成

本教程介绍了如何将 Attendance Management Services 与 Azure Active Directory (Azure AD) 进行集成。

将 Attendance Management Services 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制有权访问 Attendance Management Services 的人员。
- 可让用户通过 Azure AD 帐户自动登录到 Attendance Management Services（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

要配置 Azure AD 与 Attendance Management Services 的集成，需具备以下项：

- Azure AD 订阅
- 启用了 Attendance Management Services 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Attendance Management Services
2. 配置和测试 Azure AD 单一登录

## <a name="adding-attendance-management-services-from-the-gallery"></a>从库中添加 Attendance Management Services
要配置 Attendance Management Services 与 Azure AD 的集成，需要从库将 Attendance Management Services 添加到托管 SaaS 应用列表。

**要从库中添加 Attendance Management Services，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Attendance Management Services”，从结果面板中选择“Attendance Management Services”，然后单击“添加”按钮添加该应用程序。

    ![结果列表上的 Attendance Management Services](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Attendance Management Services 的 Azure AD 单一登录。

要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Attendance Management Services 用户。 换句话说，需在 Azure AD 用户与 Attendance Management Services 中的相关用户间建立链接关系。

若要配置和测试 Attendance Management Services 的 Azure AD 单一登录，需完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Attendance Management Services 测试用户](#create-an-attendance-management-service-test-user)**：在 Attendance Management Services 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分中的步骤在 Azure 门户中启用 Azure AD 单一登录并在 Attendance Management Services 应用程序中配置单一登录。

**若要配置 Attendance Management Services 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户的“Attendance Management Services”页面上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_samlbase.png)

3. 在“Attendance Management Services 域和 URL”部分，执行以下步骤：

    ![Attendance Management Services 域和 URL 单一登录信息](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://id.obc.jp/<tenant information >/`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Attendance Management Services 客户支持团队](http://www.obcnet.jp/)来获取这些值。

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_400.png)

6. 在“Attendance Management Services 配置”部分，单击“配置 Attendance Management Services”，打开“配置单一登录”窗口。 从“快速参考”部分中复制“SAML 实体 ID 和 SAML 单一登录服务 URL”。

    ![Attendance Management Services 配置](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_configure.png) 

7. 在其他浏览器窗口中，以管理员身份登录 Attendance Management Services 公司网站。

8. 单击“安全管理”部分下的“SAML 身份验证”。

    ![Attendance Management Services 配置](./media/active-directory-saas-attendancemanagementservices-tutorial/user1.png)

9. 执行以下步骤：

    ![Attendance Management Services 配置](./media/active-directory-saas-attendancemanagementservices-tutorial/user2.png)

    a. 选择“使用 SAML 身份验证”。

    b. 在“标识符”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。 

    c. 在“身份验证终结点 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。

    d. 单击“选择文件”按钮，上传从 Azure AD 下载的证书。

    e. 选择“禁用密码身份验证”。

    f. 单击“注册”

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！ 从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-attendancemanagementservices-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-an-attendance-management-services-test-user"></a>创建 Attendance Management Services 测试用户

必须将 Azure AD 用户预配到 Attendance Management Services 中，这些用户才能登录到此服务。 对于 Attendance Management Services，必须进行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录 Attendance Management Services 公司网站。

2. 单击“安全管理”部分下的“用户管理”。

    ![添加员工](./media/active-directory-saas-attendancemanagementservices-tutorial/user5.png)

3. 单击“新规则登录”。

    ![添加员工](./media/active-directory-saas-attendancemanagementservices-tutorial/user3.png)

4. 在“OBCiD 信息”部分执行以下步骤：

    ![添加员工](./media/active-directory-saas-attendancemanagementservices-tutorial/user4.png)

    a. 在“OBCiD”文本框中，键入用户的电子邮件，例如 BrittaSimon@contoso.com。

    b. 在“密码”文本框中，键入用户的密码。

    c. 单击“注册”


### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予访问 Attendance Management Services 的权限，使她能够使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配给 Attendance Management Services，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Attendance Management Services”。

    ![应用程序列表中的 Attendance Management Services 链接](./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 Attendance Management Services 磁贴时，应该会自动登录 Attendance Management Services 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-attendancemanagementservices-tutorial/tutorial_general_203.png

