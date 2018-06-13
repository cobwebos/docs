---
title: 教程：Azure Active Directory 与 Trakstar 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Trakstar 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 411cb8c3-95c6-4138-acf2-ffc7f663e89a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: 28ce4cf68b6d3204e42bcf4db38846f262c9bc9e
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346088"
---
# <a name="tutorial-azure-active-directory-integration-with-trakstar"></a>教程：Azure Active Directory 与 Trakstar 集成

本教程介绍如何将 Trakstar 与 Azure Active Directory (Azure AD) 集成。

将 Trakstar 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Trakstar
- 可以让用户使用其 Azure AD 帐户自动登录到 Trakstar（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Trakstar 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Trakstar 单一登录的订阅
    - SSO 是 Trakstar 中的付费功能。 若要为组织启用该功能，请联系 [Trakstar 客户端支持团队](mailto:support@trakstar.com)。

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以在[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月的试用版。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Trakstar
2. 配置和测试 Azure AD 单一登录

## <a name="adding-trakstar-from-the-gallery"></a>从库中添加 Trakstar
要配置 Trakstar 与 Azure AD 的集成，需要从库中将 Trakstar 添加到托管 SaaS 应用列表。

**若要从库中添加 Trakstar，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![Active Directory][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![应用程序][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![应用程序][3]

4. 在搜索框中，键入“Trakstar”。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-trakstar-tutorial/tutorial_trakstar_search.png)

5. 在结果面板中，选择“Trakstar”，然后单击“添加”按钮添加该应用程序。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-trakstar-tutorial/tutorial_trakstar_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，将基于名为“Britta Simon”的测试用户配置和测试 Trakstar 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Trakstar 用户。 换句话说，需要在 Azure AD 用户与 Trakstar 中相关用户之间建立链接关系。

可通过将 Azure AD 中“用户名”的值指定为 Trakstar 中“用户名”的值来建立此链接关系。

若要配置和测试 Trakstar 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Trakstar 测试用户](#creating-a-trakstar-test-user)** - 在 Trakstar 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Trakstar 应用程序中配置单一登录。

**若要配置 Trakstar 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 **Trakstar** 应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![配置单一登录](./media/active-directory-saas-trakstar-tutorial/tutorial_trakstar_samlbase.png)

3. 在“Trakstar 域和 URL”部分中，执行以下步骤：

    ![配置单一登录](./media/active-directory-saas-trakstar-tutorial/tutorial_trakstar_url.png)

    a. 在“登录 URL”文本框中，复制在 Trakstar 的“ACS (使用者) URL”中找到的值（“设置”>“身份验证”和“SSO”），格式为：`https://app.trakstar.com/auth/saml/callback?namespace=<YOUR_NAMESPACE>`

    b. 在“标识符”文本框中，保留默认值：`https://app.trakstar.com`

    > [!NOTE] 
    > 这些不是实际值。 必须使用实际登录 URL 和标识符更新这些值。 以管理员身份登录到 Trakstar，获取这些值。
    > 如果在“设置”中看不到“身份验证和 SSO”选项卡，则可能没有该功能
 
4. 在“SAML 签名证书”部分中，单击“证书(base64)”，并在计算机上保存证书文件。

    ![配置单一登录](./media/active-directory-saas-trakstar-tutorial/tutorial_trakstar_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录](./media/active-directory-saas-trakstar-tutorial/tutorial_general_400.png)

6. 在“Trakstar 配置”部分中，单击“配置 Trakstar”打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![配置单一登录](./media/active-directory-saas-trakstar-tutorial/tutorial_trakstar_configure.png) 

7. 若要在 **Trakstar** 端配置单一登录，需要以管理员身份登录并输入“证书 (Base64)”、“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。 

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![创建 Azure AD 测试用户](./media/active-directory-saas-trakstar-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/active-directory-saas-trakstar-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-trakstar-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![创建 Azure AD 测试用户](./media/active-directory-saas-trakstar-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="creating-a-trakstar-test-user"></a>创建 Trakstar 测试用户

本部分的目的是在 Trakstar 中创建名为“Britta Simon”的用户。


### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Trakstar 的权限，允许她使用 Azure 单一登录。

![分配用户][200] 

**要将 Britta Simon 分配到 Trakstar，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Trakstar”。

    ![配置单一登录](./media/active-directory-saas-trakstar-tutorial/tutorial_trakstar_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="testing-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD 单一登录配置。  
单击访问面板中的“Trakstar”磁贴时，用户应自动登录到 Trakstar 应用程序。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-trakstar-tutorial/tutorial_general_203.png

