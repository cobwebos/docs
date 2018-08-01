---
title: 教程：Azure Active Directory 与 Hornbill 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Hornbill 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 173061e4-ac1d-458f-bb9b-e9a2493aab0e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2018
ms.author: jeedes
ms.openlocfilehash: 30fdb55758d5fbac41452236ebaa9f96ab9bba6b
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163520"
---
# <a name="tutorial-azure-active-directory-integration-with-hornbill"></a>教程：Azure Active Directory 与 Hornbill 的集成

在本教程中，了解如何将 Hornbill 与 Azure Active Directory (Azure AD) 集成。

将 Hornbill 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Hornbill。
- 可以让用户使用其 Azure AD 帐户自动登录到 Hornbill（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Hornbill 的集成，需要以下项：

- Azure AD 订阅
- 启用了 Hornbill 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Hornbill
2. 配置和测试 Azure AD 单一登录

## <a name="adding-hornbill-from-the-gallery"></a>从库中添加 Hornbill
要配置 Hornbill 与 Azure AD 的集成，需要从库中将 Hornbill 添加到托管 SaaS 应用列表。

**若要从库中添加 Hornbill，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Hornbill”，在结果面板中选择“Hornbill”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Hornbill](./media/hornbill-tutorial/tutorial_hornbill_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Hornbill 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Hornbill 用户。 换句话说，需要建立 Azure AD 用户与 Hornbill 中相关用户之间的链接关系。

若要配置并测试 Hornbill 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Hornbill 测试用户](#create-a-hornbill-test-user)** - 在 Hornbill 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Hornbill 应用程序中配置单一登录。

**若要配置 Hornbill 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **Hornbill** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/hornbill-tutorial/tutorial_hornbill_samlbase.png)

3. 在“Hornbill 域和 URL”部分中，执行以下步骤：

    ![Hornbill 域和 URL 单一登录信息](./media/hornbill-tutorial/tutorial_hornbill_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/lib/saml/auth/simplesaml/module.php/saml/sp/metadata.php/saml`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 请联系 [Hornbill 客户端支持团队](https://www.hornbill.com/support/?request/)获取这些值。 

4. 在“SAML 签名证书”部分上，单击”复制”按钮来复制**应用联合元数据 URL**，并将其粘贴到记事本。

    ![证书下载链接](./media/hornbill-tutorial/tutorial_hornbill_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/hornbill-tutorial/tutorial_general_400.png)
 
6. 在另一个 Web 浏览器窗口中，以安全管理员身份登录 Hornbill。

7. 在主页上单击“系统”。

    ![Hornbill 系统](./media/hornbill-tutorial/tutorial_hornbill_system.png)

8. 导航到“安全性”。

    ![Hornbill 安全性](./media/hornbill-tutorial/tutorial_hornbill_security.png)

9. 单击“SSO 配置文件”。

    ![Hornbill 单一登录](./media/hornbill-tutorial/tutorial_hornbill_sso.png)

10. 在页面右侧，单击“添加徽标”。

    ![Hornbill 添加](./media/hornbill-tutorial/tutorial_hornbill_addlogo.png)

11. 在“配置文件详细信息”栏上，单击“导入 SAML Meta 徽标”。

    ![Hornbill 徽标](./media/hornbill-tutorial/tutorial_hornbill_logo.png)

12. 在弹出页面上的“URL”文本框中，粘贴从 Azure 门户复制的“应用联合元数据 URL”，然后单击“处理”。

    ![Hornbill 处理](./media/hornbill-tutorial/tutorial_hornbill_process.png)

13. 单击“处理”后，值将自动填充到“配置文件详细信息”部分下面。

    ![Hornbill 页面 1](./media/hornbill-tutorial/tutorial_hornbill_ssopage.png)

    ![Hornbill 页面 2](./media/hornbill-tutorial/tutorial_hornbill_ssopage1.png)

    ![Hornbill 页面 3](./media/hornbill-tutorial/tutorial_hornbill_ssopage2.png)

14. 单击“保存更改”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/hornbill-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/hornbill-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/hornbill-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/hornbill-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-hornbill-test-user"></a>创建 Hornbill 测试用户

本部分的目的是在 Hornbill 中创建名为“Britta Simon”的用户。 Hornbill 支持在默认情况下启用的实时预配。 此部分不存在任何操作项。 尝试访问 Hornbill 期间，如果尚不存在用户，则会创建一个新用户。

> [!Note]
> 如果需要手动创建用户，请联系 [Hornbill 客户端支持团队](https://www.hornbill.com/support/?request/)。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Hornbill 的访问权限支持她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Hornbill，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Hornbill”。

    ![应用程序列表中的 Hornbill 链接](./media/hornbill-tutorial/tutorial_hornbill_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Hornbill 磁贴时，应会自动登录到 Hornbill 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hornbill-tutorial/tutorial_general_01.png
[2]: ./media/hornbill-tutorial/tutorial_general_02.png
[3]: ./media/hornbill-tutorial/tutorial_general_03.png
[4]: ./media/hornbill-tutorial/tutorial_general_04.png

[100]: ./media/hornbill-tutorial/tutorial_general_100.png

[200]: ./media/hornbill-tutorial/tutorial_general_200.png
[201]: ./media/hornbill-tutorial/tutorial_general_201.png
[202]: ./media/hornbill-tutorial/tutorial_general_202.png
[203]: ./media/hornbill-tutorial/tutorial_general_203.png

