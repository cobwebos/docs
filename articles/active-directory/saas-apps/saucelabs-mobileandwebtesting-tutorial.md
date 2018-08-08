---
title: 教程：Azure Active Directory 与 Sauce Labs - Mobile and Web Testing 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Sauce Labs - Mobile and Web Testing 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: jeedes
ms.openlocfilehash: 55d84256f408e80600308ede22dbaa903b070d90
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39266733"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>教程：Azure Active Directory 与 Sauce Labs - Mobile and Web Testing 的集成

本教程介绍如何将 Sauce Labs - Mobile and Web Testing 与 Azure Active Directory (Azure AD) 集成。

将 Sauce Labs - Mobile and Web Testing 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Sauce Labs - Mobile and Web Testing。
- 可让用户使用其 Azure AD 帐户自动登录到 Sauce Labs - Mobile and Web Testing（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Sauce Labs - Mobile and Web Testing 的集成，需要做好以下准备：

- Azure AD 订阅
- 已启用 Sauce Labs - Mobile and Web Testing 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Sauce Labs - Mobile and Web Testing
2. 配置和测试 Azure AD 单一登录

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>从库中添加 Sauce Labs - Mobile and Web Testing
若要配置 Sauce Labs - Mobile and Web Testing 与 Azure AD 的集成，需要从库中将 Sauce Labs - Mobile and Web Testing 添加到托管 SaaS 应用列表。

**若要从库中添加 Sauce Labs - Mobile and Web Testing，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入 **Sauce Labs - Mobile and Web Testing**，在结果面板中选择“Sauce Labs - Mobile and Web Testing”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Sauce Labs - Mobile and Web Testing](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们将基于名为“Britta Simon”的测试用户配置和测试 Sauce Labs - Mobile and Web Testing 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Sauce Labs - Mobile and Web Testing 用户。 换句话说，需要建立 Azure AD 用户与 Sauce Labs - Mobile and Web Testing 中相关用户之间的链接关系。

若要使用 Sauce Labs - Mobile and Web Testing 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Sauce Labs - Mobile and Web Testing 测试用户](#create-a-sauce-labs---mobile-and-web-testing-test-user)** - 在 Sauce Labs - Mobile and Web Testing 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，我们将在 Azure 门户中启用 Azure AD 单一登录并在 Sauce Labs - Mobile and Web Testing 应用程序中配置单一登录。

**若要配置 Sauce Labs - Mobile and Web Testing 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Sauce Labs - Mobile and Web Testing”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_samlbase.png)

3. 在“Sauce Labs - Mobile and Web Testing 域和 URL”部分，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成。

    ![Sauce Labs - Mobile and Web Testing 域和 URL 单一登录信息](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_url.png)

4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_certificate.png)

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_400.png)

6. 在另一 Web 浏览器窗口中，以管理员身份登录到 Sauce Labs - Mobile and Web Testing 公司站点。

7. 单击“用户”图标并选择“团队管理”选项卡。

    ![配置单一登录](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

8. 在文本框中输入**域名**。

    ![配置单一登录](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

9. 单击“配置”选项卡。

    ![配置单一登录](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

10. 在“配置单一登录”部分执行以下步骤。

    ![配置单一登录](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. 单击“浏览”并上传从 Azure AD 下载的元数据文件。

    b. 选中“允许实时预配”复选框。

    c. 单击“保存”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/saucelabs-mobileandwebtesting-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
  
### <a name="create-a-sauce-labs---mobile-and-web-testing-test-user"></a>创建 Sauce Labs - Mobile and Web Testing 测试用户

本部分的目的是在 Sauce Labs - Mobile and Web Testing 中创建名为 Britta Simon 的用户。 Sauce Labs - Mobile and Web Testing 支持默认启用的实时预配。 此部分不存在任何操作项。 如果尚不存在用户，则在尝试访问 Sauce Labs - Mobile and Web Testing 期间会创建一个新用户。
>[!Note]
>如需手动创建用户，请联系 [Sauce Labs - Mobile and Web Testing 支持团队](mailto:support@saucelabs.com)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予 Britta Simon 访问 Sauce Labs - Mobile and Web Testing 的权限，使其能够使用 Azure 单一登录。

![分配用户角色][200]

**若要将 Britta Simon 分配到 Sauce Labs - Mobile and Web Testing，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“Sauce Labs - Mobile and Web Testing”。

    ![应用程序列表中的 Sauce Labs - Mobile and Web Testing 链接](./media/saucelabs-mobileandwebtesting-tutorial/tutorial_saucelabs_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Sauce Labs - Mobile and Web Testing”磁贴时，应会自动登录到 Sauce Labs - Mobile and Web Testing 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_01.png
[2]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_02.png
[3]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_03.png
[4]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_04.png

[100]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_100.png

[200]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_200.png
[201]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_201.png
[202]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_202.png
[203]: ./media/saucelabs-mobileandwebtesting-tutorial/tutorial_general_203.png