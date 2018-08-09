---
title: 教程：Azure Active Directory 与 New Relic 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 New Relic 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3186b9a8-f4d8-45e2-ad82-6275f95e7aa6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: jeedes
ms.openlocfilehash: 80bd77504f1b2ab5b6e5c781eadb7c2cd4c99220
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440739"
---
# <a name="tutorial-azure-active-directory-integration-with-new-relic"></a>教程：Azure Active Directory 与 New Relic 集成

本教程介绍如何将 New Relic 与 Azure Active Directory (Azure AD) 集成。

将 New Relic 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 New Relic。
- 可让用户使用其 Azure AD 帐户自动登录到 New Relic（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 New Relic 的集成，需备齐以下项：

- Azure AD 订阅
- 启用 New Relic 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 New Relic
1. 配置和测试 Azure AD 单一登录

## <a name="adding-new-relic-from-the-gallery"></a>从库中添加 New Relic
若要配置 New Relic 与 Azure AD 的集成，需要从库中将 New Relic 添加到托管 SaaS 应用列表。

若要从库中添加 New Relic，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中，键入“New Relic”，在结果面板中选择“New Relic”，然后单击“添加”按钮添加应用程序。

    ![结果列表中的 New Relic](./media/new-relic-tutorial/tutorial_new-relic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于一个名为“Britta Simon”的测试用户配置和测试 New Relic 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 New Relic 用户。 换句话说，需要建立 Azure AD 用户与 New Relic 中相关用户之间的关联关系。

可通过将 Azure AD 中“用户名”的值指定为 New Relic 中“用户名”的值来建立此关联关系。

若要配置和测试 New Relic 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 New Relic 测试用户](#create-a-new-relic-test-user)** - 在 New Relic 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 New Relic 应用程序中配置单一登录。

若要配置 New Relic 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的 New Relic 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/new-relic-tutorial/tutorial_new-relic_samlbase.png)

1. 在“New Relic 域和 URL”部分中，执行以下步骤：

    ![New Relic 域和 URL 单一登录信息](./media/new-relic-tutorial/tutorial_new-relic_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://rpm.newrelic.com/accounts/{acc_id}/sso/saml/login` - 请务必替换为你自己的 New Relic 帐户 ID。

    b. 在“标识符”文本框中，键入值 `rpm.newrelic.com`

1. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/new-relic-tutorial/tutorial_new-relic_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/new-relic-tutorial/tutorial_general_400.png)

1. 在“New Relic 配置”部分中，单击“配置 New Relic”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL 和 SAML 单一登录服务 URL”。

    ![New Relic 配置](./media/new-relic-tutorial/tutorial_new-relic_configure.png) 

1. 在其他 Web 浏览器窗口中，以管理员身份登录 **New Relic** 公司站点。

1. 在顶部菜单中，单击“帐户设置”。
   
    ![帐户设置](./media/new-relic-tutorial/ic797036.png "帐户设置")

1. 单击“安全性和身份验证”选项卡，并单击“单一登录”选项卡。
   
    ![单一登录](./media/new-relic-tutorial/ic797037.png "单一登录")

1. 在 SAML 对话框页上执行以下步骤：
   
    ![SAML](./media/new-relic-tutorial/ic797038.png "SAML")
   
   a. 单击“选择文件”，上传已下载的 Azure Active Directory 证书。

   b. 在“远程登录 URL”文本框中，粘贴从 Azure 门户复制的“SAML 单一登录服务 URL”值。
   
   c. 在“注销登录 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”值。

   d. 单击“保存所做的更改”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/new-relic-tutorial/create_aaduser_01.png)

1. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/new-relic-tutorial/create_aaduser_02.png)

1. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/new-relic-tutorial/create_aaduser_03.png)

1. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/new-relic-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-new-relic-test-user"></a>创建 New Relic 测试用户

为了使 Azure Active Directory 用户能够登录到 New Relic，必须将其预配到 New Relic 中。 使用 New Relic 时，预配属手动任务。

**要将用户帐户预配到 New Relic，请执行以下步骤：**

1. 以管理员身份登录到 **New Relic** 公司站点。

1. 在顶部菜单中，单击“帐户设置”。
   
    ![帐户设置](./media/new-relic-tutorial/ic797040.png "帐户设置")

1. 在左侧的“帐户”窗格中，单击“摘要”，并单击“添加用户”。
   
    ![帐户设置](./media/new-relic-tutorial/ic797041.png "帐户设置")

1. 在“活动用户”对话框中，执行以下步骤：
   
    ![活动用户](./media/new-relic-tutorial/ic797042.png "活动用户")
   
    a. 在“电子邮件”文本框中，键入要预配的有效 Azure Active Directory 用户的电子邮件地址。

    b. 选择“用户”作为“角色”。

    c. 单击“添加此用户”。

>[!NOTE]
>可以使用任何其他 New Relic 用户帐户创建工具或 New Relic 提供的 API 来预配 AAD 用户帐户。
> 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 New Relic 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

若要将 Britta Simon 分配到 New Relic，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“New Relic”。

    ![应用程序列表中的 New Relic 链接](./media/new-relic-tutorial/tutorial_new-relic_app.png)  

1. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 New Relic 磁贴时，应当会自动登录到 New Relic 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/new-relic-tutorial/tutorial_general_01.png
[2]: ./media/new-relic-tutorial/tutorial_general_02.png
[3]: ./media/new-relic-tutorial/tutorial_general_03.png
[4]: ./media/new-relic-tutorial/tutorial_general_04.png

[100]: ./media/new-relic-tutorial/tutorial_general_100.png

[200]: ./media/new-relic-tutorial/tutorial_general_200.png
[201]: ./media/new-relic-tutorial/tutorial_general_201.png
[202]: ./media/new-relic-tutorial/tutorial_general_202.png
[203]: ./media/new-relic-tutorial/tutorial_general_203.png

