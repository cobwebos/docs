---
title: 教程：Azure Active Directory 与 Manabi Pocket 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Manabi Pocket 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8e521099-bf7d-43ab-a0e0-86aa1c9e577e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 0116cac7d0e44efee0112d57aedd4f5ee02833b3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430709"
---
# <a name="tutorial-azure-active-directory-integration-with-manabi-pocket"></a>教程：Azure Active Directory 与 Manabi Pocket 集成

在本教程中，了解如何将 Manabi Pocket 与 Azure Active Directory (Azure AD) 集成。

将 Manabi Pocket 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Manabi Pocket。
- 可以让你的用户通过其 Azure AD 帐户自动登录到 Manabi Pocket（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Manabi Pocket 的集成，需要具有以下各项：

- Azure AD 订阅
- 已启用 Manabi Pocket 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Manabi Pocket
1. 配置和测试 Azure AD 单一登录

## <a name="adding-manabi-pocket-from-the-gallery"></a>从库添加 Manabi Pocket
若要配置 Manabi Pocket 与 Azure AD 的集成，需要从库中将 Manabi Pocket 添加到托管 SaaS 应用列表。

**若要从库中添加 Manabi Pocket，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中，键入“Manabi Pocket”，在结果面板中选择“Manabi Pocket”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Manabi Pocket](./media/manabipocket-tutorial/tutorial_manabipocket_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Manabi Pocket 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Manabi Pocket 用户。 换句话说，需要建立 Azure AD 用户与 Manabi Pocket 中相关用户之间的关联关系。

若要使用 Manabi Pocket 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 Manabi Pocket 测试用户](#create-a-manabi-pocket-test-user)** - 在 Manabi Pocket 中创建 Britta Simon 的对应用户，使其与 Azure AD 中表示 Britta Simon 的用户相关联。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Manabi Pocket 应用程序中配置单一登录。

**若要使用 Manabi Pocket 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Manabi Pocket”应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。

    ![“单一登录”对话框](./media/manabipocket-tutorial/tutorial_manabipocket_samlbase.png)

1. 在“Manabi Pocket 域和 URL”部分中，执行以下步骤：

    ![Manabi Pocket 域和 URL 单一登录信息](./media/manabipocket-tutorial/tutorial_manabipocket_url.png)

    a. 在“登录 URL”文本框中，键入 URL：`https://ed-cl.com/`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<SERVER-NAME>.ed-cl.com/<TENANT-ID>/idp/provider`

    > [!NOTE]
    > 标识符非实际值。 使用实际标识符更新此值。 请联系 [Manabi Pocket 客户端支持团队](mailto:info-ed-cl@ntt.com)获取此值。

1. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/manabipocket-tutorial/tutorial_manabipocket_certificate.png) 

1. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/manabipocket-tutorial/tutorial_general_400.png)

1. 若要在“Manabi Pocket”端配置单一登录，需要将下载的“元数据 XML”发送给 [Manabi Pocket 支持团队](mailto:info-ed-cl@ntt.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，单击“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/manabipocket-tutorial/create_aaduser_01.png)

1. 若要显示用户列表，请转到“用户和组”，然后单击“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/manabipocket-tutorial/create_aaduser_02.png)

1. 若要打开“用户”对话框，在“所有用户”对话框顶部单击“添加”。

    ![“添加”按钮](./media/manabipocket-tutorial/create_aaduser_03.png)

1. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/manabipocket-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 单击“创建”。
 
### <a name="create-a-manabi-pocket-test-user"></a>创建 Manabi Pocket 测试用户

在本部分中，在 Manabi Pocket 中创建一个名为 Britta Simon 的用户。 与 [Manabi Pocket 支持团队](mailto:info-ed-cl@ntt.com)协作，将用户添加到 Manabi Pocket 平台。 使用单一登录前，必须先创建并激活用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授权对 Manabi Pocket 的访问权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 Manabi Pocket，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“Manabi Pocket”。

    ![应用程序列表中的 Manabi Pocket 链接](./media/manabipocket-tutorial/tutorial_manabipocket_app.png)  

1. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202]

1. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中单击“分配”按钮。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Manabi Pocket 磁贴时，应当会自动登录到 Manabi Pocket 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/manabipocket-tutorial/tutorial_general_01.png
[2]: ./media/manabipocket-tutorial/tutorial_general_02.png
[3]: ./media/manabipocket-tutorial/tutorial_general_03.png
[4]: ./media/manabipocket-tutorial/tutorial_general_04.png

[100]: ./media/manabipocket-tutorial/tutorial_general_100.png

[200]: ./media/manabipocket-tutorial/tutorial_general_200.png
[201]: ./media/manabipocket-tutorial/tutorial_general_201.png
[202]: ./media/manabipocket-tutorial/tutorial_general_202.png
[203]: ./media/manabipocket-tutorial/tutorial_general_203.png