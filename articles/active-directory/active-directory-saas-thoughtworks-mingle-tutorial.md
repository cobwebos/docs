---
title: 教程：Azure Active Directory 与 Thoughtworks Mingle 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Thoughtworks Mingle 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: b621abe1fa6dac3b9f618b7bd2e31b2bdf964ac6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>教程：Azure Active Directory 与 Thoughtworks Mingle 集成

本教程介绍如何将 Thoughtworks Mingle 与 Azure Active Directory (Azure AD) 集成。

将 Thoughtworks Mingle 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 Thoughtworks Mingle
- 可以让用户使用其 Azure AD 帐户自动登录到 Thoughtworks Mingle（单一登录）
- 可以在一个中心位置（即 Azure 门户）管理帐户

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Thoughtworks Mingle 的集成，需要以下项：

- Azure AD 订阅
- 已启用 Thoughtworks Mingle 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Thoughtworks Mingle
2. 配置和测试 Azure AD 单一登录

## <a name="adding-thoughtworks-mingle-from-the-gallery"></a>从库添加 Thoughtworks Mingle
要配置 Thoughtworks Mingle 与 Azure AD 的集成，需要从库中将 Thoughtworks Mingle 添加到托管 SaaS 应用列表。

**若要从库中添加 Thoughtworks Mingle，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“Thoughtworks Mingle”，从结果面板中选择“Thoughtworks Mingle”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 Thoughtworks Mingle 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Thoughtworks Mingle 用户。 换句话说，需要在 Azure AD 用户与 Thoughtworks Mingle 中相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值指定为 Thoughtworks Mingle 中“用户名”的值来建立此链接关系。

若要使用 Thoughtworks Mingle 配置和测试 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Thoughtworks Mingle 测试用户](#create-a-thoughtworks-mingle-test-user)** - 在 Thoughtworks Mingle 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Thoughtworks Mingle 应用程序中配置单一登录。

**若要使用 Thoughtworks Mingle 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“Thoughtworks Mingle”应用程序集成页上，单击“单一登录”。

    ![配置单一登录][4]

2. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”作为“模式”以启用单一登录。
 
    ![“单一登录”对话框](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_samlbase.png)

3. 在“Thoughtworks Mingle 域和 URL”部分中，执行以下步骤：

    ![Thoughtworks Mingle 域和 URL 单一登录信息](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_url.png)

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.mingle.thoughtworks.com`

    > [!NOTE] 
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请联系 [Thoughtworks Mingle 客户支持团队](https://support.thoughtworks.com/hc/categories/201743486-Mingle-Community-Support)获取此值。 
 
4. 在“SAML 签名证书”部分中，单击“元数据 XML”，并在计算机上保存元数据文件。

    ![证书下载链接](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_certificate.png) 

5. 单击“保存”按钮。

    ![配置单一登录“保存”按钮](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_400.png)

6. 以管理员身份登录 **Thoughtworks Mingle** 公司站点。

7. 单击“管理员”选项卡，并单击“SSO 配置”。
   
    ![管理员选项卡](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785157.png "SSO 配置")

8. 在“SSO 配置”部分中，执行以下步骤：
   
    ![SSO 配置](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785158.png "SSO Config")
    
    a. 若要上传元数据文件，请单击“选择文件”。 

    b. 单击“保存更改”。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory”>“企业应用程序”部分添加此应用后，只需单击“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在此处阅读有关嵌入式文档功能的详细信息：[ Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左侧导航窗格中，单击“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，单击“所有用户”。
    
    ![“用户和组”以及“所有用户”链接](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请在对话框顶部单击“添加”。
 
    ![“添加”按钮](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框页上，执行以下步骤：
 
    ![“用户”对话框](./media/active-directory-saas-thoughtworks-mingle-tutorial/create_aaduser_04.png) 

    a. 在“名称”文本框中，键入 **BrittaSimon**。

    b. 在“用户名”文本框中，键入 BrittaSimon 的“电子邮件地址”。

    c. 选择“显示密码”并记下“密码”的值。

    d. 单击“创建”。
 
### <a name="create-a-thoughtworks-mingle-test-user"></a>创建 Thoughtworks Mingle 测试用户

要使 Azure AD 用户能够登录，必须使用其 Azure Active Directory 用户名将其预配到 Thoughtworks Mingle 应用程序中。 对于 Thoughtworks Mingle，预配是一项手动任务。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录 Thoughtworks Mingle 公司站点。

2. 单击“配置文件”。
   
    ![第一个项目](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785160.png "Your First Project")

3. 单击“管理员”选项卡，并单击“用户”。
   
    ![用户](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785161.png "用户")

4. 单击“新建用户”。
   
    ![新建用户](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785162.png "New User")

5. 在“新建用户”对话框页上，执行以下步骤：
   
    ![新建用户对话框](./media/active-directory-saas-thoughtworks-mingle-tutorial/ic785163.png "新建用户")  
 
    a. 将要预配的有效 Azure AD 帐户的**登录名**、**显示名称**、**选择密码**、**确认密码**键入到相关文本框中。 

    b. 对于“用户类型”，请选择“全用户”。

    c. 单击“创建此配置文件”。

>[!NOTE]
>可以使用任何其他 Thoughtworks Mingle 用户帐户创建工具或 Thoughtworks Mingle 提供的 API 来预配 AAD 用户帐户。
> 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Thoughtworks Mingle 的权限，以支持其使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 Thoughtworks Mingle，请执行以下步骤：**

1. 在 Azure 门户中打开应用程序视图，导航到目录视图，接着转到“企业应用程序”，并单击“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“Thoughtworks Mingle”。

    ![应用程序列表中的 Thoughtworks Mingle 链接](./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_thoughtworksmingle_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![“用户和组”链接][202] 

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

5. 在“用户和组”对话框的“用户”列表中，选择“Britta Simon”。

6. 在“用户和组”对话框中单击“选择”按钮。

7. 在“添加分配”对话框中单击“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

本部分旨在使用“访问面板”测试 Azure AD 单一登录配置。

单击访问面板中的 Thoughtworks Mingle 磁贴，即可自动登录到 Thoughtworks Mingle 应用程序。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-thoughtworks-mingle-tutorial/tutorial_general_203.png

