---
title: 教程：将 Azure Active Directory 与 Box 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Box 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2017
ms.author: jeedes
ms.openlocfilehash: daad9104798dc02b479b4e022287c3630e4a67a0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34336864"
---
# <a name="integrate-azure-active-directory-with-box"></a>将 Azure Active Directory 与 Box 应用相集成

本教程介绍如何将 Box 与 Azure Active Directory (Azure AD) 集成。

将 Azure AD 与 Box 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Box。
- 可以让用户使用其 Azure AD 帐户自动登录到 Box（单一登录，简称 SSO）。
- 可在一个中心位置（即 Azure 门户）管理帐户。

有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Box 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Box SSO 的订阅

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，会在测试环境中测试 Azure AD 单一登录。 

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Box
2. 配置和测试 Azure AD 单一登录

## <a name="add-box-from-the-gallery"></a>从库中添加 Box
要配置 Box 与 Azure AD 的集成，请执行以下操作，将 Box 从库添加到托管 SaaS 应用列表：

1. 在 [Azure 门户](https://portal.azure.com) 的左窗格中，选择“Azure Active Directory”。 

    ![“Azure Active Directory”按钮][1]

2. 选择“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”窗口][2]
    
3. 要添加新应用程序，请选择窗口顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入 **Box**，在结果列表中选择“Box”，然后选择“添加”。

    ![结果列表中的 Box](./media/active-directory-saas-box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分，我们使用名为“Britta Simon”的测试用户配置和测试 Box 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 中的用户相对应的 Box 用户。 换句话说，必须在 Azure AD 用户与 Box 中的相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Box 中“用户名”的值来建立此链接关系。

要配置和测试 Box 的 Azure AD 单一登录，请完成下面 5 个部分中的构建基块。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

执行以下操作，在 Azure 门户中启用 Azure AD 单一登录并在 Box 应用程序中配置单一登录：

1. 在 Azure 门户上的“Box”应用程序集成窗口中，单击“单一登录”。

    ![“单一登录”链接][4]

2. 在“单一登录”窗口中的“单一登录模式”框内，选择“基于 SAML 的登录”。
 
    ![“单一登录”窗口](./media/active-directory-saas-box-tutorial/tutorial_box_samlbase.png)

3. 在“Box 域和 URL”下，执行以下操作：

    ![Box 域和 URL 单一登录信息](./media/active-directory-saas-box-tutorial/url3.png)

    a. 在“登录 URL”框中，键入采用以下格式的 URL：*https://\<子域>.box.com*。

    b. 在“标识符”文本框中，键入 **box.net**。
     
    > [!NOTE] 
    > 上面的值不是实际值。 使用实际登录 URL 和标识符更新这些值。 若要获取这些值，请联系 [Box 客户端支持团队](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire)。 

4. 在“SAML 签名证书”下，选择“元数据 XML”，并将元数据文件保存到计算机上。

    ![证书下载链接](./media/active-directory-saas-box-tutorial/tutorial_box_certificate.png) 

5. 选择“保存”。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-box-tutorial/tutorial_general_400.png)
    
6. 若要为应用程序配置 SSO，请遵循[自行设置 SSO](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown) 中的过程。

> [!NOTE] 
> 如果无法为 Box 帐户启用 SSO 设置，可能需要联系 [Box 客户端支持团队](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire)并提供下载的 XML 文件。

> [!TIP]
> 设置应用时，可以在 [Azure 门户](https://portal.azure.com)中阅读前述教程的简洁版本。 在“Active Directory” > “企业应用程序”部分添加此应用后，请选择“单一登录”选项卡，然后在底部的“配置”部分访问嵌入式文档。 有关嵌入式文档功能的详细信息，请参阅 [Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)。
>

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将执行以下操作，在 Azure 门户中创建一个名为 Britta Simon 的测试用户：

![创建 Azure AD 测试用户][100]

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”。

    ![“Azure Active Directory”链接](./media/active-directory-saas-box-tutorial/create_aaduser_01.png)

2. 若要显示当前用户列表，请选择“用户和组” > “所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-box-tutorial/create_aaduser_02.png)

3. 在“所有用户”窗口的顶部，选择“添加”。

    ![“添加”按钮](./media/active-directory-saas-box-tutorial/create_aaduser_03.png)

    此时会打开“用户”窗口。

4. 在“用户”窗口中执行以下操作：

    ![“用户”窗口](./media/active-directory-saas-box-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择**创建**。
 
### <a name="create-a-box-test-user"></a>创建 Box 测试用户

本部分在 Box 中创建测试用户 Britta Simon。 Box 支持在默认情况下启用的实时预配。 尝试访问 Box 时，如果不存在用户，则系统会创建一个新用户。 无需执行创建用户的操作。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，我们通过授予用户 Britta Simon 访问 Box 的权限，使其能够使用 Azure 单一登录。 为此，请执行以下操作：

![分配用户角色][200]

1. 在 Azure 门户中，打开“应用程序”视图，转到“目录”视图，然后选择“企业应用程序” > “所有应用程序”。

    ![“企业应用程序”和“所有应用程序”链接][201] 

2. 在“应用程序”列表中选择“Box”。

    ![Box 链接](./media/active-directory-saas-box-tutorial/tutorial_box_app.png)  

3. 在左窗格中，选择“用户和组”。

    ![“用户和组”链接][202]

4. 选择“添加”，然后在“添加分配”窗格中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”窗口中的“用户”列表内，选择“Britta Simon”。

6. 选择“选择”按钮。

7. 在“添加分配”窗口中，选择“分配”。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“Box”磁贴时，会打开用于登录 Box 应用程序的登录页。

## <a name="additional-resources"></a>其他资源

* [有关将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)
* [配置用户预配](active-directory-saas-box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-box-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-box-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-box-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-box-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-box-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-box-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-box-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-box-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-box-tutorial/tutorial_general_203.png

