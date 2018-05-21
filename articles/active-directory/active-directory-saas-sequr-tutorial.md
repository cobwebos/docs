---
title: 教程：Azure Active Directory 与 Sequr 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Sequr 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a491e2ce-b4e8-41b8-8f4a-a2e263e462c3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.openlocfilehash: f3e245f609d96cc247f10ef3c184b7fe1de53756
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-sequr"></a>教程：Azure Active Directory 与 Sequr 的集成

本教程介绍了如何将 Sequr 与 Azure Active Directory (Azure AD) 进行集成。

将 Sequr 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Sequr
- 可以让用户使用其 Azure AD 帐户自动登录到 Sequr（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Sequr 的集成，需要具有以下各项：

- Azure AD 订阅
- 启用了单一登录的 Sequr 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Sequr
2. 配置和测试 Azure AD 单一登录

## <a name="adding-sequr-from-the-gallery"></a>从库中添加 Sequr
若要配置 Sequr 与 Azure AD 的集成，需要从库中将 Sequr 添加到托管 SaaS 应用列表。

**若要从库中添加 Sequr，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“Sequr”，在结果面板中选择“Sequr”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Sequr](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Sequr 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Sequr 用户。 换句话说，需要在 Azure AD 用户与 Sequr 中相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值指定为 Sequr 中“用户名”的值来建立此链接关系。

若要配置和测试 Sequr 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Sequr 测试用户](#create-a-sequr-test-user)** - 在 Sequr 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Sequr 应用程序中配置单一登录。

**若要配置 Sequr 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在 **Sequr** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_samlbase.png)

3. 在“Sequr 域和 URL”部分中，如果要在 **IDP** 发起的模式下配置应用程序，请执行以下步骤：

    ![Sequr 域和 URL 单一登录信息](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_url.png)

    在“标识符”文本框中，键入 URL：`https://login.sequr.io`

4. 如果要在 SP 发起的模式下配置应用程序，请选中“显示高级 URL 设置”，并执行以下步骤：

    ![Sequr 域和 URL 单一登录信息](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_url1.png)

    a. 在“登录 URL”文本框中，键入 URL：`https://login.sequr.io`

    b. 你将从“中继状态”文本框中获取此值，本教程下文中进行了说明。
     
5. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![证书下载链接](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_certificate.png) 

6. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-sequr-tutorial/tutorial_general_400.png)
    
7. 在“Sequr 配置”部分中，单击“配置 Sequr”打开“配置登录”窗口。 从“快速参考”部分中复制“SAML 单一登录服务 URL”

    ![Sequr 配置](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_configure.png)

8. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Sequr 公司站点。

9. 单击左侧导航面板中的“集成”。

    ![Sequr 配置](./media/active-directory-saas-sequr-tutorial/configure1.png)

10. 向下滚动到“单一登录”部分并单击“管理”。

    ![Sequr 配置](./media/active-directory-saas-sequr-tutorial/configure2.png)

11. 在“管理单一登录”部分中执行以下步骤：

    ![Sequr 配置](./media/active-directory-saas-sequr-tutorial/configure3.png)

    a. 在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户中复制的“SAML 单一登录服务 URL”值。

    b. 拖放已从 Azure 门户中下载的**证书**文件，或者手动输入证书的内容。

    c. 在保存配置后，将会生成中继状态值。 复制“中继状态”值并将其粘贴到 Azure 门户中“Sequr 域和 URL”部分中的“中继状态”文本框。

    d. 单击“ **保存**”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-sequr-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-sequr-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/active-directory-saas-sequr-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-sequr-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-sequr-test-user"></a>创建 Sequr 测试用户

在本部分中，将在 Sequr 中创建一个名为 Britta Simon 的用户。 请与 [Sequr 客户端支持团队](mailto:support@sequr.io)协作来在 Sequr 平台中添加用户。 使用单一登录前，必须先创建并激活用户。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Sequr 的权限，允许其使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Sequr，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Sequr”。

    ![应用程序列表中的 Sequr 链接](./media/active-directory-saas-sequr-tutorial/tutorial_sequr_app.png)  

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Sequr 磁贴时，应该会自动登录到 Sequr 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sequr-tutorial/tutorial_general_203.png

