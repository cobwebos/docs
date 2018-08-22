---
title: 教程：Azure Active Directory 与 N2F - Expense reports 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 N2F - Expense reports 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f56d53d7-5a08-490a-bfb9-78fefc2751ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 27fb299bc3bbbbf75bdf40ae02eac627763ce6d4
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2018
ms.locfileid: "40007046"
---
# <a name="tutorial-azure-active-directory-integration-with-n2f---expense-reports"></a>教程：Azure Active Directory 与 N2F - Expense reports 的集成

本教程介绍如何将 N2F - Expense reports 与 Azure Active Directory (Azure AD) 集成。

将 N2F - Expense reports 与 Azure AD 集成可带来以下优势：

- 可在 Azure AD 中控制谁有权访问 N2F - Expense reports。
- 可让用户使用其 Azure AD 帐户自动登录到 N2F - Expense reports（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 N2F - Expense reports 的集成，需要准备好以下各项：

- Azure AD 订阅
- 已启用 N2F - Expense reports 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 N2F - Expense reports
2. 配置和测试 Azure AD 单一登录

## <a name="adding-n2f---expense-reports-from-the-gallery"></a>从库中添加 N2F - Expense reports

要配置 N2F - Expense reports 与 Azure AD 的集成，需要从库中将 N2F - Expense reports 添加到托管 SaaS 应用列表。

**若要从库中添加 N2F - Expense reports，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入 **N2F - Expense reports**，在结果面板中选择“N2F - Expense reports”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 N2F - Expense reports](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分需根据名为“Britta Simon”的测试用户的情况，配置和测试 N2F - Expense reports 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 N2F - Expense reports 用户。 换句话说，需要建立 Azure AD 用户与 N2F - Expense reports 中相关用户之间的链接关系。

若要配置和测试 N2F - Expense reports 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 N2F - Expense reports 测试用户](#create-a-n2f---expense-reports-test-use)** - 在 N2F - Expense reports 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 身份。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录，并在 N2F - Expense reports 应用程序中配置单一登录。

**若要配置 N2F - Expense reports 的 Azure AD 单一登录，请执行以下步骤**：

1. 在 Azure 门户中的“N2F - Expense reports”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_samlbase.png)

3. 在“N2F - Expense reports 域和 URL”部分，如果要在“IDP 发起的模式”下配置应用程序，用户无需执行任何步骤，因为应用已与 Azure 进行预集成：

    ![N2F - Expense reports 域和 URL 单一登录信息](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url1.png)

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![N2F - Expense reports 域和 URL 单一登录信息](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_url2.png)

    在“登录 URL”文本框中，键入 URL：`https://www.n2f.com/app/`

5. 在“SAML 签名证书”部分中，单击”复制”按钮来复制“应用联合元数据 URL”，并将其粘贴到记事本。

    ![证书下载链接](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_certificate.png)

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/n2f-expensereports-tutorial/tutorial_general_400.png)

7. 在“N2F - Expense reports”部分，单击“配置 N2F - Expense reports”打开“配置单一登录”窗口。 从“快速参考”部分复制“SAML 实体 ID”。

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_configure.png)

8. 在另一 Web 浏览器窗口中，以管理员身份登录到 N2F - Expense reports 公司站点。

9. 单击“设置”，然后从下拉列表中选择“高级设置”。

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/configure1.png)

10. 选择“帐户设置”选项卡。

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/configure2.png)

11. 依次选择“身份验证”、“+ 添加身份验证方法”选项卡。

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/configure3.png)

12. 选择“SAML Microsoft Office 365”作为身份验证方法。

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/configure4.png)

13. 在“身份验证方法”部分执行以下步骤：

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/configure5.png)

    a. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“SAML 实体 ID”值。

    b. 在“元数据 URL”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”值。

    c. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/n2f-expensereports-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/n2f-expensereports-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/n2f-expensereports-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/n2f-expensereports-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="create-a-n2f---expense-reports-test-user"></a>创建 N2F - Expense reports 测试用户

若要让 Azure AD 用户登录 N2F - Expense reports，必须将其预配到 N2F - Expense reports。 对于 N2F - Expense reports 而言，预配属于手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 N2F - Expense reports 公司站点。

2. 单击“设置”，然后从下拉列表中选择“高级设置”。

   ![N2F - Expense - 添加用户](./media/n2f-expensereports-tutorial/configure1.png)

3. 在左侧导航面板中选择“用户”选项卡。

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/user1.png)

4. 选择“+ 新建用户”选项卡。

   ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/user2.png)

5. 在“用户”部分执行以下步骤：

    ![N2F - Expense reports 配置](./media/n2f-expensereports-tutorial/user3.png)

    a. 在“电子邮件地址”文本框中，输入用户的电子邮件地址（例如 **brittasimon@contoso.com**）。

    b. 在“名字”文本框中，输入用户的名字（如“Britta”）。

    c. 在“名称”文本框中输入用户的姓名，例如“BrittaSimon”。

    d. 根据组织的要求选择“角色，直属经理(N + 1)”和“部门”。

    e. 单击“验证并发送邀请”。

    > [!NOTE]
    > 如果添加用户时遇到任何问题，请联系 [N2F - Expense reports 支持团队](mailto:support@n2f.com)

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们将通过授予 Britta Simon 访问 N2F - Expense reports 的权限，使其能够使用 Azure 单一登录。

![分配用户角色][200]

**若要将 Britta Simon 分配到 N2F - Expense reports，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“N2F - Expense reports”。

    ![应用程序列表中的 N2F - Expense reports 链接](./media/n2f-expensereports-tutorial/tutorial_n2f-expensereports_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 N2F - Expense reports 磁贴时，应会自动登录到 N2F - Expense reports 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/n2f-expensereports-tutorial/tutorial_general_01.png
[2]: ./media/n2f-expensereports-tutorial/tutorial_general_02.png
[3]: ./media/n2f-expensereports-tutorial/tutorial_general_03.png
[4]: ./media/n2f-expensereports-tutorial/tutorial_general_04.png

[100]: ./media/n2f-expensereports-tutorial/tutorial_general_100.png

[200]: ./media/n2f-expensereports-tutorial/tutorial_general_200.png
[201]: ./media/n2f-expensereports-tutorial/tutorial_general_201.png
[202]: ./media/n2f-expensereports-tutorial/tutorial_general_202.png
[203]: ./media/n2f-expensereports-tutorial/tutorial_general_203.png

