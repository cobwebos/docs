---
title: 教程：Azure Active Directory 与 Nimblex 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Nimblex 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d3e165a5-f062-4b50-ac0b-b400838e99cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: jeedes
ms.openlocfilehash: 63b70e38f4c4f804c9be520480c7562834d42598
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37870066"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>教程：Azure Active Directory 与 Nimblex 的集成

在本教程中，了解如何将 Nimblex 与 Azure Active Directory (Azure AD) 集成。

将 Nimblex 与 Azure AD 集成可以提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Nimblex。
- 可以让用户使用其 Azure AD 帐户自动登录到 Nimblex（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Nimblex 的集成，需要以下项：

- Azure AD 订阅
- 启用了 Nimblex 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Nimblex
2. 配置和测试 Azure AD 单一登录

## <a name="adding-nimblex-from-the-gallery"></a>从库中添加 Nimblex
若要配置 Nimblex 与 Azure AD 的集成，需要从库中将 Nimblex 添加到托管的 SaaS 应用列表。

**若要从库中添加 Nimblex，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]

1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Nimblex”，在结果面板中选择“Nimblex”，然后单击“添加”按钮以添加该应用程序。

    ![结果列表中的 Nimblex](./media/nimblex-tutorial/tutorial_nimblex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户配置和测试 Nimblex 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Nimblex 用户。 换句话说，需要建立 Azure AD 用户与 Nimblex 中相关用户之间的链接关系。

若要配置和测试 Nimblex 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Nimblex 测试用户](#create-a-nimblex-test-user)** - 在 Nimblex 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Nimblex 应用程序中配置单一登录。

**若要配置 Nimblex 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Nimblex”应用程序集成页上单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框](./media/nimblex-tutorial/tutorial_nimblex_samlbase.png)

3. 在“Nimblex 域和 URL”部分中执行以下步骤：

    ![Nimblex 域和 URL 单一登录信息](./media/nimblex-tutorial/tutorial_nimblex_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<YOUR APPLICATION PATH>/Login.aspx`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<YOUR APPLICATION PATH>/`

    c. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<path-to-application>/SamlReply.aspx`

    > [!NOTE] 
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [Nimblex 客户端支持团队](mailto:support@ebms.com.au)获取这些值。

4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/nimblex-tutorial/tutorial_nimblex_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/nimblex-tutorial/tutorial_general_400.png)

6. 在“Nimblex 配置”部分，单击“配置 Nimblex”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”

    ![Nimblex 配置](./media/nimblex-tutorial/tutorial_nimblex_configure.png) 

7. 在另一个 Web 浏览器窗口中，以安全管理员身份登录 Nimblex。

9. 单击页面右上角的“设置”标志。

    ![Nimblex 设置](./media/nimblex-tutorial/tutorial_nimblex_settings.png)

10. 在“控制面板”页面上，单击“安全”部分下的“单一登录”。

    ![Nimblex 设置](./media/nimblex-tutorial/tutorial_nimblex_single.png)

11. 在“管理单一登录”页面上，选择实例名称并单击“编辑”。

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_saml.png)

12. 在“编辑 SSO 提供程序”页面上执行以下步骤：

    ![Nimblex saml](./media/nimblex-tutorial/tutorial_nimblex_sso.png)

    a. 在“说明”文本框中，键入实例名称。

    b. 在记事本中，打开从 Azure 门户下载的 base-64 编码证书，复制其内容，然后将其粘贴到“证书”框中。

    c. 在“标识提供者 SSO 目标 URL”文本框中，粘贴从 Azure 门户中复制的“SAML 单一登录服务 URL”。

    d. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/nimblex-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/nimblex-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/nimblex-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/nimblex-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-nimblex-test-user"></a>创建 Nimblex 测试用户

本部分是要在 Nimblex 中创建名为“Britta Simon”的用户。 Nimblex 支持实时预配（默认情况下为启用状态）。 此部分不存在任何操作项。 在尝试访问 Nimblex 期间，如果该用户尚不存在，则将创建一个新用户。

>[!Note]
>如果需要手动创建用户，请联系 [Nimblex 客户端支持团队](mailto:support@ebms.com.au)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，向 Britta Simon 授予 Nimblex 的访问权限，从而支持她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Nimblex，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Nimblex”。

    ![应用程序列表中的 Nimblex 链接](./media/nimblex-tutorial/tutorial_nimblex_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Nimblex 磁贴时，应当会自动登录到 Nimblex 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/nimblex-tutorial/tutorial_general_01.png
[2]: ./media/nimblex-tutorial/tutorial_general_02.png
[3]: ./media/nimblex-tutorial/tutorial_general_03.png
[4]: ./media/nimblex-tutorial/tutorial_general_04.png

[100]: ./media/nimblex-tutorial/tutorial_general_100.png

[200]: ./media/nimblex-tutorial/tutorial_general_200.png
[201]: ./media/nimblex-tutorial/tutorial_general_201.png
[202]: ./media/nimblex-tutorial/tutorial_general_202.png
[203]: ./media/nimblex-tutorial/tutorial_general_203.png

