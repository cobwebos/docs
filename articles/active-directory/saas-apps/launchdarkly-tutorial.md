---
title: 教程：Azure Active Directory 与 LaunchDarkly 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 LaunchDarkly 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0e9cb37e-16bf-493b-bfc8-8d9840545a1e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: 54bf459f6acd7649f3ad1a546bef1d0429393161
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420753"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>教程：Azure Active Directory 与 LaunchDarkly 集成

本教程介绍如何将 LaunchDarkly 与 Azure Active Directory (Azure AD) 集成。

将 LaunchDarkly 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 LaunchDarkly。
- 可以让用户使用其 Azure AD 帐户自动登录到 LaunchDarkly（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 LaunchDarkly 的集成，需要以下各项：

- Azure AD 订阅
- 启用了 LaunchDarkly 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 LaunchDarkly
1. 配置和测试 Azure AD 单一登录

## <a name="adding-launchdarkly-from-the-gallery"></a>从库中添加 LaunchDarkly
要配置 LaunchDarkly 与 Azure AD 的集成，需要从库中将 LaunchDarkly 添加到托管 SaaS 应用列表。

若要从库中添加 LaunchDarkly，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中，键入“LaunchDarkly”，在结果面板中选择“LaunchDarkly”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 LaunchDarkly](./media/launchdarkly-tutorial/tutorial_launchdarkly_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 LaunchDarkly 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 LaunchDarkly 用户。 换句话说，需要建立 Azure AD 用户与 LaunchDarkly 中相关用户之间的关联关系。

若要配置和测试 LaunchDarkly 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 LaunchDarkly 测试用户](#create-a-launchdarkly-test-user)** - 在 LaunchDarkly 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 身份。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 LaunchDarkly 应用程序中配置单一登录。

若要配置 LaunchDarkly 的 Azure AD 单一登录，请执行以下步骤：

1. 在 Azure 门户中的 LaunchDarkly 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框](./media/launchdarkly-tutorial/tutorial_launchdarkly_samlbase.png)

1. 在“LaunchDarkly 域和 URL”部分中，如果要在 IDP 发起的模式下配置应用程序，请执行以下步骤：

    ![LaunchDarkly 域和 URL 单一登录信息](./media/launchdarkly-tutorial/tutorial_launchdarkly_url.png)

    a. 在“标识符(实体 ID)”文本框中，键入 URL：`app.launchdarkly.com`

    b. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`
    
    > [!NOTE]
    > 答复 URL 值不是真实值。 用户将使用实际的回复 URL 更新该值（本教程稍后将会介绍）。

1. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![LaunchDarkly 域和 URL 单一登录信息](./media/launchdarkly-tutorial/tutorial_launchdarkly_url1.png)

    在“登录 URL”文本框中，键入 URL：`https://app.launchdarkly.com`

1. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/launchdarkly-tutorial/tutorial_launchdarkly_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/launchdarkly-tutorial/tutorial_general_400.png)
    
1. 在“LaunchDarkly 配置”部分，单击“配置 LaunchDarkly”打开“配置登录”窗口。 从“快速参考”部分中复制“单一登录服务 URL”。

    ![LaunchDarkly 配置](./media/launchdarkly-tutorial/tutorial_launchdarkly_configure.png)

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 LaunchDarkly 公司站点。

1. 从左侧导航面板中选择“帐户设置”。

    ![LaunchDarkly 配置](./media/launchdarkly-tutorial/configure1.png)

1. 单击“安全”选项卡。

    ![LaunchDarkly 配置](./media/launchdarkly-tutorial/configure2.png)

1. 单击“启用 SSO”，然后单击“编辑 SAML 配置”。

    ![LaunchDarkly 配置](./media/launchdarkly-tutorial/configure3.png)

1. 在“编辑 SAML 配置”部分执行以下步骤：

    ![LaunchDarkly 配置](./media/launchdarkly-tutorial/configure4.png)

    a. 复制实例的“SAML 使用者服务 URL”并将其粘贴到 Azure 门户上“LaunchDarkly 域和 URL”部分的“回复 URL”文本框中。

    b. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“单一登录服务 URL”值。

    c. 在记事本中打开从 Azure 门户中下载的证书，复制其内容并将其粘贴至“X.509 证书”框，或者可以单击“上传证书”直接上传该证书。

    d. 单击“保存”

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/launchdarkly-tutorial/create_aaduser_01.png)

1. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/launchdarkly-tutorial/create_aaduser_02.png)

1. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/launchdarkly-tutorial/create_aaduser_03.png)

1. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/launchdarkly-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。

### <a name="create-a-launchdarkly-test-user"></a>创建一个 LaunchDarkly 测试用户

本部分的目的是在 LaunchDarkly 中创建名为 Britta Simon 的用户。 LaunchDarkly 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 尝试访问 LaunchDarkly 期间，如果尚不存在用户，则会创建一个新用户。
>[!Note]
>如果需要手动创建用户，请联系 [LaunchDarkly 客户端支持团队](mailto:support@launchdarkly.com)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 LaunchDarkly 的权限，支持其使用 Azure 单一登录。

![分配用户角色][200] 

若要将 Britta Simon 分配到 LaunchDarkly，请执行以下步骤：

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“LaunchDarkly”。

    ![应用程序列表中的 LaunchDarkly 链接](./media/launchdarkly-tutorial/tutorial_launchdarkly_app.png)  

1. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击 LaunchDarkly 磁贴时，应当会自动登录到 LaunchDarkly 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/launchdarkly-tutorial/tutorial_general_01.png
[2]: ./media/launchdarkly-tutorial/tutorial_general_02.png
[3]: ./media/launchdarkly-tutorial/tutorial_general_03.png
[4]: ./media/launchdarkly-tutorial/tutorial_general_04.png

[100]: ./media/launchdarkly-tutorial/tutorial_general_100.png

[200]: ./media/launchdarkly-tutorial/tutorial_general_200.png
[201]: ./media/launchdarkly-tutorial/tutorial_general_201.png
[202]: ./media/launchdarkly-tutorial/tutorial_general_202.png
[203]: ./media/launchdarkly-tutorial/tutorial_general_203.png

