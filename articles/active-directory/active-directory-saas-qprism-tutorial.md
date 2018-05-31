---
title: 教程：Azure Active Directory 与 QPrism 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 QPrism 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: ffd7c828087162f83812da445b0eeb71545b59db
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34348978"
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>教程：Azure Active Directory 与 QPrism 集成

在本教程中，了解如何将 QPrism 与 Azure Active Directory (Azure AD) 集成。

将 QPrism 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 QPrism。
- 可以让用户使用其 Azure AD 帐户自动登录到 QPrism（单一登录）。
- 可在一个中心位置（即 Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅[什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 QPrism 的集成，需要以下项：

- Azure AD 订阅
- 已启用 QPrism 单一登录的订阅

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 QPrism
2. 配置和测试 Azure AD 单一登录

## <a name="add-qprism-from-the-gallery"></a>从库中添加 QPrism
若要配置 QPrism 与 Azure AD 的集成，需要从库中将 QPrism 添加到托管 SaaS 应用列表。

**若要从库中添加 QPrism：**

1. 在 [Azure 门户](https://portal.azure.com) 的左窗格中，选择“Azure Active Directory”。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请在对话框顶部选择“新建应用程序”。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，键入“QPrism”，在结果面板中选择“QPrism”。 然后，单击“添加”按钮以添加该应用程序。

    ![结果列表中的 QPrism](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户配置和测试 QPrism 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 QPrism 用户。 换句话说，必须在 Azure AD 用户与 QPrism 中的相关用户之间建立链接关系。

若要建立此链接关系，请将 Azure AD 中“用户名”的值指定为 QPrism 中“用户名”的值。

若要配置和测试 QPrism 的 Azure AD 单一登录，请完成以下构建基块：

1. [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
2. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 QPrism 测试用户](#create-a-qprism-test-user)，在 QPrism 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
5. [测试单一登录](#test-single-sign-on)，以验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 QPrism 应用程序中配置单一登录。

1. 在 Azure 门户中，在 **QPrism** 应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_samlbase.png)

3. 在“QPrism 域和 URL”部分中，执行以下操作：

    ![QPrism 域和 URL 单一登录信息](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<customer domain>.qmyzone.com/login`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > 这些不是实际值。 必须使用实际的标识符和登录 URL 更新这些值。 请联系 [QPrism 客户端支持团队](mailto:qsupport-ce@quatrro.com)获取这些值。 

4. 在“SAML 签名证书”部分上，单击”复制”按钮来复制**应用联合元数据 URL**，并将其粘贴到记事本。

     ![证书下载链接](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_certificate.png)

5. 选择“保存”。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-qprism-tutorial/tutorial_general_400.png)
    
6. 若要在“QPrism”端配置单一登录，需将应用联合元数据 URL 发送给 [QPrism 支持团队](mailto:qsupport-ce@quatrro.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户：**

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-qprism-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-qprism-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，请在“所有用户”对话框顶部选择“添加”。

    ![“添加”按钮](./media/active-directory-saas-qprism-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中执行以下操作：

    ![“用户”对话框](./media/active-directory-saas-qprism-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择**创建**。
 
### <a name="create-a-qprism-test-user"></a>创建 QPrism 测试用户

在本部分中，将在 QPrism 中创建一个名为“Britta Simon”的用户。 请与 [QPrism 支持团队](mailto:qsupport-ce@quatrro.com)协作，将用户添加到 QPrism 平台中。 使用单一登录前，必须先创建并激活用户。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 QPrism 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 QPrism：**

1. 在 Azure 门户中，打开应用程序视图，然后导航到目录视图。 转到“企业应用程序”，并选择“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“QPrism”。

    ![应用程序列表中的 QPrism 链接](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_app.png)  

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接][202]

4. 选择 **添加** 。 然后，在“添加分配”下，选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框中，在“用户”列表中选择“Britta Simon”。

6. 在“用户和组”对话框中，选择“选择”。

7. 在“添加分配”下，选择“分配”。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中，选择 QPrism 磁贴时，应该会自动登录到 QPrism 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_203.png

