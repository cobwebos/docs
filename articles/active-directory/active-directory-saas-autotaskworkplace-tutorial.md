---
title: 教程：Azure Active Directory 与 Autotask Workplace 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Autotask Workplace 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a9a7ff71-c389-4169-aafd-d7a505244797
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 1468d48c7a60176638d6c0be1a0072b2e5e1ba82
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34347867"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>教程：Azure Active Directory 与 Autotask Workplace 集成

在本教程中，了解如何将 Autotask Workplace 与 Azure Active Directory (Azure AD) 进行集成。

将 Autotask Workplace 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权限访问 Autotask Workplace
- 可以让用户使用其 Azure AD 帐户自动登录到 Autotask Workplace（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Autotask Workplace 的集成，需要具有以下项：

- Azure AD 订阅
- 已启用 Autotask Workplace 单一登录的订阅
- 必须是 Workplace 的管理员或超级管理员。
- 必须具有 Azure AD 中的管理员帐户。
- 将利用此功能的用户必须在 Workplace 和 Azure AD 中都有帐户，并且这两个帐户的电子邮件地址必须匹配。

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Autotask Workplace
2. 配置和测试 Azure AD 单一登录

## <a name="adding-autotask-workplace-from-the-gallery"></a>从库中添加 Autotask Workplace
若要配置 Autotask Workplace 与 Azure AD 的集成，需要从库中将 Autotask Workplace 添加到托管 SaaS 应用列表。

**若要从库中添加 Autotask Workplace，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“Autotask Workplace”，在结果面板中选择“Autotask Workplace”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Autotask Workplace](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Autotask Workplace 的 Azure AD 单一登录。

若要使用单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Autotask Workplace 用户。 换句话说，需要在 Azure AD 用户与 Autotask Workplace 中相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Autotask Workplace 中“用户名”的值来建立此链接关系。

若要配置和测试 Autotask Workplace 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Autotask Workplace 测试用户](#create-an-autotask-workplace-test-user)** - 在 Autotask Workplace 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将介绍如何在 Azure 门户中启用 Azure AD 单一登录并在 Autotask Workplace 应用程序中配置单一登录。

若要配置 Autotask Workplace 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的“Autotask Workplace”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_samlbase.png)

3. 在“Autotask Workplace 域和 URL”部分，如果要在 IDP 发起的模式下配置应用程序，请执行以下步骤：

    ![IDP 的 Autotask workplace 域和 URL 单一登录信息](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata`

    b. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO`

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![SP 的 Autotask workplace 域和 URL 单一登录信息](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<subdomain>.awp.autotask.net/loginsso`
     
    > [!NOTE] 
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 若要获取这些值，请与 [Autotask Workplace 客户端支持团队](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)联系。 

5. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_400.png)

7. 在另一个 Web 浏览器窗口中，使用管理员凭据登录到 Workplace Online。

    >[!Note]
    >配置 IdP 时，需要指定子域。 若要确认正确的子域，请登录到 Workplace Online。 登录后，请记下 URL 中的子域。
    >子域是 “https://” 和 “.awp.autotask.net/” 之间的部分，应为 us、eu、ca 或 au。

8. 转到“配置” > “单一登录”并执行以下步骤：

    ![Autotask 单一登录配置](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskssoconfig1.png)
 
    a. 选择“XML 元数据文件”选项，然后上传从 Azure 门户下载的**元数据 XML**。

    b. 单击“启用 SSO”。
    
    ![Autotask 单一登录批准配置](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskssoconfig2.png)

    c. 选中“我确认此信息正确，并且我信任此 IdP”复选框。

    d. 单击“批准”。
     
>[!Note]
>如果需要有关配置 Autotask Workplace 的帮助，请参阅[此页](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)获取与 Workplace 帐户相关的帮助。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-autotaskworkplace-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="create-an-autotask-workplace-test-user"></a>创建 Autotask Workplace 测试用户

本部分会在 Autotask 中创建名为“Britta Simon”的用户。 请与 [Autotask Workplace 支持团队](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)协作，以将用户添加到 Autotask Workplace 平台。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Autotask Workplace 的权限，以支持其使用 Azure 单一登录。

![分配用户角色][200] 

若要将 Britta Simon 分配到 Autotask Workplace，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Autotask Workplace”。

    ![应用程序列表中的“Autotask Workplace”链接](./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_autotaskworkplace_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Autotask Workplace”磁贴时，你应自动登录到 Autotask Workplace 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-autotaskworkplace-tutorial/tutorial_general_203.png

