---
title: 教程：Azure Active Directory 与 Clarizen 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Clarizen 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: 8af9e7023142077fdeed0262e5f39921b158a14f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>教程：Azure Active Directory 与 Clarizen 的集成

本教程介绍了如何将 Azure Active Directory (Azure AD) 与 Clarizen 进行集成。 此集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Clarizen。
- 可以让用户使用其 Azure AD 帐户自动登录到 Clarizen（单一登录）。
- 可在一个中心位置（即 Azure 门户）管理帐户。

本教程中的方案包括两个主要任务：

1. 从库中添加 Clarizen。
2. 配置和测试 Azure AD 单一登录。

如果想要了解有关软件即服务 (SaaS) 应用与 Azure AD 集成的更多详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件
若要配置 Azure AD 与 Clarizen 的集成，需要以下项：

- Azure AD 订阅
- 一个启用了单一登录的 Clarizen 订阅

若要测试本教程中的步骤，请遵循以下建议：

- 在测试环境中测试 Azure AD 单一登录。 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 测试环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="add-clarizen-from-the-gallery"></a>从库中添加 Clarizen
要配置 Clarizen 与 Azure AD 的集成，需要从库中将 Clarizen 添加到托管 SaaS 应用列表。

1. 在 [Azure 门户](https://portal.azure.com)中，在左窗格中，单击 **Azure Active Directory** 图标。

    ![Azure Active Directory 图标][1]

2. 单击“企业应用程序”。 然后单击“所有应用程序”。

    ![单击“企业应用程序”和“所有应用程序”][2]

3. 单击对话框顶部的“添加”按钮。

    ![“添加”按钮][3]

4. 在搜索框中，键入“Clarizen”。

    ![在搜索框中键入“Clarizen”](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_000.png)

5. 在结果窗格中，选择“Clarizen”，并单击“添加”以添加该应用程序。

    ![在结果窗格中选择 Clarizen](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在以下各部分中，将基于测试用户 Britta Simon 配置和测试 Clarizen 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Clarizen 用户。 换句话说，需要在 Azure AD 用户与 Clarizen 中相关用户之间建立链接关系。 可以通过将 Azure AD 中“用户名”的值分配为 Clarizen 中“用户名”的值来建立此链接关系。

若要配置和测试 Clarizen 的 Azure AD 单一登录，请完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)**，让用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)**，以便使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Clarizen 测试用户](#create-a-clarizen-test-user)**，在 Clarizen 中创建 Britta Simon 的对应用户，并将其链接到她的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)**，使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)**，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录
在 Azure 门户中启用 Azure AD 单一登录并在 Clarizen 应用程序中配置单一登录。

1. 在 Azure 门户中，在 **Clarizen** 应用程序集成页上，单击“单一登录”。

    ![单击“单一登录”][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的登录”作为“模式”以启用单一登录。

    ![选择“基于 SAML 的登录”](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_01.png)

3. 在“Clarizen 域和 URL”部分中，执行以下步骤：

    ![标识符和回复 URL 的框](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_02.png)

    a. 在“标识符”框中，键入以下值：**Clarizen**

    b. 在“回复 URL”框中，使用以下模式键入 URL：**https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx**

    > [!NOTE]
    > 这些不是实际值。 必须使用实际的标识符和回复 URL。 此处，我们建议使用唯一的字符串值作为标识符。 若要获取实际值，请与 [Clarizen 支持团队](https://success.clarizen.com/hc/en-us/requests/new)联系。

4. 在“SAML 签名证书”部分中，单击“创建新证书”。

    ![单击“创建新证书”](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_03.png)  

5. 在“创建新证书”对话框中，单击日历图标，并选择一个到期日期。 然后单击“保存”。

    ![选择并保存到期日期](./media/active-directory-saas-clarizen-tutorial/tutorial_general_300.png)

6. 在“SAML 签名证书”部分中，选择“激活新证书”，并单击“保存”。

    ![选中“激活新证书”复选框](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_04.png)

7. 在“滚动更新证书”对话框中，单击“确定”。

    ![单击“确定”以确认要激活证书](./media/active-directory-saas-clarizen-tutorial/tutorial_general_400.png)

8. 在“SAML 签名证书”部分中，单击“证书(Base64)”，并在计算机上保存证书文件。

    ![单击“证书(Base64)”以开始下载](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_05.png)

9. 在“Clarizen 配置”部分中，单击“配置 Clarizen”打开“配置登录”窗口。

    ![单击“配置 Clarizen”](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_06.png)

    ![“配置登录”窗口，其中包括了文件和 URL](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_07.png)

10. 在另一个 Web 浏览器窗口中，以管理员身份登录 Clarizen 公司站点。

11. 单击用户名，然后单击“设置”。

    ![单击用户名下的“设置”](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_001.png "设置")

12. 单击“全局设置”选项卡。然后，在“联合身份验证”旁边，单击“编辑”。

    ![“全局设置”选项卡](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_002.png "全局设置")

13. 在“联合身份验证”对话框中，执行以下步骤：

    ![“联合身份验证”对话框](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_003.png "联合身份验证")

    a. 选择“启用联合身份验证”。

    b. 单击“上传”以上传所下载的证书。

    c. 在“登录 URL”框中，输入 Azure AD 应用程序配置窗口中“SAML 单一登录服务 URL”的值。

    d. 在“注销 URL”框中，输入 Azure AD 应用程序配置窗口中“注销 URL”的值。

    e. 选择“使用 POST”。

    f. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在 Azure 门户中，创建一个名为 Britta Simon 的测试用户。

![Azure AD 测试用户的姓名和电子邮件地址][100]

1. 在 Azure 门户中，在左窗格中，单击 **Azure Active Directory** 图标。

    ![Azure Active Directory 图标](./media/active-directory-saas-clarizen-tutorial/create_aaduser_01.png)

2. 单击“用户和组”，并单击“所有用户”以显示用户列表。

    ![单击“用户和组”以及“所有用户”](./media/active-directory-saas-clarizen-tutorial/create_aaduser_02.png)

3. 在对话框顶部，单击“添加”以打开“用户”对话框。

    ![“添加”按钮](./media/active-directory-saas-clarizen-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框，其中填写了姓名、电子邮件地址和密码](./media/active-directory-saas-clarizen-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入 Britta Simon 帐户的电子邮件地址。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。

### <a name="create-a-clarizen-test-user"></a>创建 Clarizen 测试用户
若要使 Azure AD 用户能够登录到 Clarizen，必须预配用户帐户。 对于 Clarizen，需要手动执行预配。

1. 以管理员身份登录到 Clarizen 公司站点。

2. 单击“人员”。

    ![单击“人员”](./media/active-directory-saas-clarizen-tutorial/create_aaduser_001.png "人员")

3. 单击“邀请用户”。

    ![“邀请用户”按钮](./media/active-directory-saas-clarizen-tutorial/create_aaduser_002.png "邀请用户")

4. 在“邀请人员”对话框中，执行以下步骤：

    ![“邀请人员”对话框](./media/active-directory-saas-clarizen-tutorial/create_aaduser_003.png "邀请人员")

    a. 在“电子邮件”框中，键入 Britta Simon 帐户的电子邮件地址。

    b. 单击“邀请”。

    > [!NOTE]
    > Azure Active Directory 帐户持有者将收到一封电子邮件，并且将单击其中的链接以在激活帐户前确认帐户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户
通过向 Britta Simon 授予对 Clarizen 的访问权限使她能够使用 Azure 单一登录。

![分配的测试用户][200]

1. 在 Azure 门户中，打开应用程序视图，浏览到目录视图，单击“企业应用程序”，并单击“所有应用程序”。

    ![单击“企业应用程序”和“所有应用程序”][201]

2. 在应用程序列表中，选择“Clarizen”。

    ![在列表中选择 Clarizen](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_50.png)

3. 在左窗格中，单击“用户和组”。

    ![单击“用户和组”][202]

4. 单击“添加”按钮。 然后，在“添加分配”对话框中选择“用户和组”。

    ![“添加”按钮和“添加分配”对话框][203]

5. 在“用户和组”对话框中，在用户列表中选择“Britta Simon”。

6. 在“用户和组”对话框中，单击“选择”按钮。

7. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="test-single-sign-on"></a>测试单一登录
使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Clarizen 磁贴时，应当会自动登录 Clarizen 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_203.png
