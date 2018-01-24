---
title: "教程：Azure Active Directory 与 Vodeclic 的集成 | Microsoft Docs"
description: "了解如何在 Azure Active Directory 与 Vodeclic 之间配置单一登录。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d77a0f53-e3a3-445e-ab3e-119cef6e2e1d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: jeedes
ms.openlocfilehash: 940c7bb5040fb91a03b01dc43ee07d52e3d4e63b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="tutorial-azure-active-directory-integration-with-vodeclic"></a>教程：Azure Active Directory 与 Vodeclic 的集成

本教程介绍如何将 Vodeclic 与 Azure Active Directory (Azure AD) 集成。

将 Vodeclic 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Vodeclic。
- 可以让用户使用其 Azure AD 帐户自动登录到 Vodeclic（单一登录或 SSO）。
- 可在一个中心位置（即 Azure 门户）管理帐户。

如果想要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [What is application access and single sign-on with Azure Active Directory?](active-directory-appssoaccess-whatis.md)（什么是使用 Azure Active Directory 的应用程序访问和单一登录？）。

## <a name="prerequisites"></a>先决条件

要配置 Azure AD 与 Vodeclic 的集成，需要准备好以下各项：

- Azure AD 订阅
- 已启用 Vodeclic SSO 的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，请获取[免费试用一个月的版本](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Vodeclic
2. 配置和测试 Azure AD 单一登录

## <a name="add-vodeclic-from-the-gallery"></a>从库中添加 Vodeclic
要配置 Vodeclic 与 Azure AD 的集成，需要从库中将 Vodeclic 添加到托管 SaaS 应用列表。

**要从库中添加 Vodeclic，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 转到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 要添加新应用程序，请选择对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在“搜索框”中键入 Vodeclic。 在结果窗格中，选择“Vodeclic”，然后选择“添加”按钮添加该应用程序。

    ![结果列表中的 Vodeclic](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户配置并测试 Vodeclic 的 Azure AD 单一登录。

要运行单一登录，Azure AD 需要知道与 Azure AD 用户对应的 Vodeclic 用户。 换句话说，需要在 Azure AD 用户与 Vodeclic 中的相关用户之间建立链接。

在 Vodeclic 中，赋予“用户名”与 Azure AD 中的“用户名”相同的值。 现在，已关联这两个用户。

要配置并测试 Vodeclic 的 Azure AD 单一登录，请完成以下构建基块：

1. [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
2. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 Vodeclic 测试用户](#create-a-vodeclic-test-user)，在 Vodeclic 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
5. [测试单一登录](#test-single-sign-on)，以验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

本部分在 Azure 门户中启用 Azure AD 单一登录，并在 Vodeclic 应用程序中配置单一登录。

**要配置 Vodeclic 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Vodeclic”应用程序集成页上，选择“单一登录”。

    ![配置单一登录链接][4]

2. 在“单一登录”对话框中，在“单一登录模式”下选择“基于 SAML 的登录”，启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_samlbase.png)

3. 如果要在“IDP”发起的模式下配置应用程序，请在“Vodeclic 域和 URL”部分中执行下列步骤：

    ![Vodeclic 域和 URL 单一登录信息](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url.png)

    a. 在“标识符”框中，使用以下模式键入 URL：`https://<companyname>.lms.vodeclic.net/auth/saml`

    b. 在“回复 URL”框中，使用以下模式键入 URL：`https://<companyname>.lms.vodeclic.net/auth/saml/callback`

4. 如果要在“SP”发起的模式下配置应用程序，请选中“显示高级 URL 设置”复选框，并执行以下步骤：

    ![Vodeclic 域和 URL 单一登录信息](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_url1.png)

    在“登录 URL”框中，使用以下模式键入 URL：`https://<companyname>.lms.vodeclic.net/auth/saml`
     
    > [!NOTE] 
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [Vodeclic 客户端支持团队](mailto:hotline@vodeclic.com)获取这些值。

5. 在“SAML 签名证书”部分中，选择“元数据 XML”。 然后，将元数据文件保存到计算机。

    ![证书下载链接](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_certificate.png) 

6. 选择“保存”。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-vodeclic-tutorial/tutorial_general_400.png)
    
7. 要在 Vodeclic 端配置单一登录，请将下载的元数据 XML 发送给 [Vodeclic 支持团队](mailto:hotline@vodeclic.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了。 从“Active Directory” > “企业应用程序”部分添加该应用后，选择“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在 [Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)中阅读有关嵌入式文档功能的详细信息。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_01.png)

2. 若要显示用户列表，请转到“用户和组”。 然后选择“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_02.png)

3. 若要打开“用户”对话框，请在“所有用户”对话框顶部选择“添加”。

    ![“添加”按钮](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_03.png)

4. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/active-directory-saas-vodeclic-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d.单击“下一步”。 选择“创建”。
 
### <a name="create-a-vodeclic-test-user"></a>创建一个 Vodeclic 测试用户

本部分在 Vodeclic 中创建一个名为 Britta Simon 的用户。 请与 [Vodeclic 支持团队](mailto:hotline@vodeclic.com)协作，将该用户添加到 Vodeclic 平台中。 使用单一登录前，必须先创建并激活用户。

> [!NOTE]
> 根据应用程序要求，可能需要获取计算机白名单。 对于这种情况，需要与 [Vodeclic 支持团队](mailto:hotline@vodeclic.com)共享公用 IP 地址。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 Vodeclic 的访问权限支持其使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 Vodeclic，请执行以下步骤：**

1. 在 Azure 门户中，打开应用程序视图，并转到目录视图。 然后，转到“企业应用”，并选择“所有应用”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Vodeclic”。

    ![应用程序列表中的 Vodeclic 链接](./media/active-directory-saas-vodeclic-tutorial/tutorial_vodeclic_app.png)  

3. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接][202]

4. 选择“添加”按钮。 然后，在“添加分配”对话框中，选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”。

6. 在“用户和组”对话框中，选择“选择”按钮。

7. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“Vodeclic”磁贴时，应会自动登录到 Vodeclic 应用程序。

有关访问面板的详细信息，请参阅[访问面板简介](active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-vodeclic-tutorial/tutorial_general_203.png

