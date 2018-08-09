---
title: 教程：Azure Active Directory 与 FactSet 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 FactSet 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: f41072d1-b2a4-4c4d-9deb-101b52e29096
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: ca66ddb60be962ddbaf45af2932022e45340c1f8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438971"
---
# <a name="tutorial-azure-active-directory-integration-with-factset"></a>教程：Azure Active Directory 与 FactSet 的集成

本教程介绍如何将 FactSet 与 Azure Active Directory (Azure AD) 进行集成。

将 FactSet 与 Azure AD 进行集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 FactSet。
- 可让用户使用其 Azure AD 帐户自动登录到 FactSet（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

要配置 Azure AD 与 FactSet 的集成，需要以下项：

- Azure AD 订阅
- 已启用 FactSet 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 FactSet
1. 配置和测试 Azure AD 单一登录

## <a name="adding-factset-from-the-gallery"></a>从库中添加 FactSet
要配置 FactSet 与 Azure AD 的集成，需要从库中将 FactSet 添加到托管 SaaS 应用列表。

**若要从库中添加 FactSet，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中，键入**FactSet**，在结果面板中选择**FactSet**，然后单击**添加**按钮添加该应用程序。

    ![结果列表中的 FactSet](./media/factset-tutorial/tutorial_factset_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

本部分基于名为“Britta Simon”的测试用户配置和测试 FactSet 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 FactSet 用户。 换句话说，需要建立 Azure AD 用户与 FactSet 中相关用户之间的链接关系。

可通过将 Azure AD 中“用户名”的值指定为 FactSet 中“用户名”的值来建立此链接关系。

若要配置和测试 FactSet 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 FactSet 测试用户](#create-a-factset-test-user)** - 在 FactSet 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分将在 Azure 门户中启用 Azure AD 单一登录并在 FactSet 应用程序中配置单一登录。

**若要配置 FactSet 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中，在**FactSet**应用程序集成页上，单击**单一登录**。

    ![配置单一登录链接][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/factset-tutorial/tutorial_factset_samlbase.png)

1. 在“FactSet 域和 URL”部分，执行以下步骤：

    ![FactSet 域和 URL 单一登录信息](./media/factset-tutorial/tutorial_factset_url.png)

    a. 在“登录 URL”文本框中，使用以下模式键入 URL： `https://login.factset.com/services/auth-test?idpid=<GUID>`

    b. 在“标识符”文本框中，键入一个 URL：`https://login.factset.com`

    > [!NOTE] 
    > 此登录 URL 值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [FactSet 客户端支持团队](mailto:support@factset.com)获取此值。 
 
1. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/factset-tutorial/tutorial_factset_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/factset-tutorial/tutorial_general_400.png)

1. 若要在“FactSet”端配置单一登录，需要将下载的“元数据 XML”发送给 [FactSet 支持团队](mailto:support@factset.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/factset-tutorial/create_aaduser_01.png)

1. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/factset-tutorial/create_aaduser_02.png)

1. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/factset-tutorial/create_aaduser_03.png)

1. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/factset-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-factset-test-user"></a>创建 FactSet 测试用户

本部分将在 FactSet 中创建一个名为 Britta Simon 的用户。 请与 [FactSet 支持团队](mailto:support@factset.com)协作，将用户添加到 FactSet 平台中。 使用单一登录前，必须先创建并激活用户。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分通过向 Britta Simon 授予 FactSet 的访问权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 FactSet，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“FactSet”。

    ![应用程序列表中的 FactSet 链接](./media/factset-tutorial/tutorial_factset_app.png)  

1. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击“访问面板”中的“FactSet”磁贴时，应会自动登录到 FactSet 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/factset-tutorial/tutorial_general_01.png
[2]: ./media/factset-tutorial/tutorial_general_02.png
[3]: ./media/factset-tutorial/tutorial_general_03.png
[4]: ./media/factset-tutorial/tutorial_general_04.png

[100]: ./media/factset-tutorial/tutorial_general_100.png

[200]: ./media/factset-tutorial/tutorial_general_200.png
[201]: ./media/factset-tutorial/tutorial_general_201.png
[202]: ./media/factset-tutorial/tutorial_general_202.png
[203]: ./media/factset-tutorial/tutorial_general_203.png

