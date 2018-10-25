---
title: 教程：Azure Active Directory 与 Expensify 的集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 Expensify 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2018
ms.author: jeedes
ms.openlocfilehash: c9ee0af3cbf2c1aa7b24d2f4cf5fba9d664bc087
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2018
ms.locfileid: "48248034"
---
# <a name="tutorial-azure-active-directory-integration-with-expensify"></a>教程：Azure Active Directory 与 Expensify 的集成

本教程介绍了如何将 Expensify 与 Azure Active Directory (Azure AD) 进行集成。

将 Expensify 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Expensify。
- 可以让用户使用其 Azure AD 帐户自动登录到 Expensify（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Expensify 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了 Expensify 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Expensify
1. 配置和测试 Azure AD 单一登录

## <a name="adding-expensify-from-the-gallery"></a>从库中添加 Expensify

要配置 Expensify 与 Azure AD 的集成，需要从库中将 Expensify 添加到托管 SaaS 应用列表。

**若要从库中添加 Expensify，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![图像](./media/expensify-tutorial/selectazuread.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![图像](./media/expensify-tutorial/a_select_app.png)
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![图像](./media/expensify-tutorial/a_new_app.png)

4. 在搜索框中，键入“Expensify”，在结果面板中选择“Expensify”，然后单击“添加”按钮添加该应用程序。

     ![图像](./media/expensify-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为“Britta Simon”的测试用户配置并测试 Expensify 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Expensify 用户。 换句话说，需要在 Azure AD 用户与 Expensify 中的相关用户之间建立链接关系。

通过将 Azure AD 中“用户名”的值指定为 Expensify 中“用户名”的值来建立此链接关系。

若要配置并测试 Expensify 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
1. **[创建 Expensify 测试用户](#create-an-expensify-test-user)** - 在 Expensify 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
1. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Expensify 应用程序中配置单一登录。

**若要配置 Expensify 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com/)中的“Expensify”应用程序集成页上，选择“单一登录”。

    ![图像](./media/expensify-tutorial/b1_b2_select_sso.png)

2. 单击屏幕顶部的“更改单一登录模式”，以选择 **SAML** 模式。

      ![图像](./media/expensify-tutorial/b1_b2_saml_ssso.png)

3. 在“选择单一登录方法”对话框中，对 **SAML** 模式单击“选择”以启用单一登录。

    ![图像](./media/expensify-tutorial/b1_b2_saml_sso.png)

4. 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“基本 SAML 配置”对话框。

    ![图像](./media/expensify-tutorial/b1-domains_and_urlsedit.png)

5. 在“基本 SAML 配置”部分中，执行以下步骤：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“登录 URL”文本框中，键入 URL：`https://www.expensify.com/authentication/saml/login`

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://www.<companyname>.expensify.com`

    ![图像](./media/expensify-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > 将标识符 URL 的 <companyname> 部分替换为公司的域。 请参阅上面的示例 `https://contoso.expensify.com`。 在 Expensify 中，这是“设置”>“域控制”中显示的域名。

    ![Expensify 域信息](./media/expensify-tutorial/tutorial_expensify_domain.png)

6. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“下载”以根据要求下载相应的证书并将其保存在计算机上。

    ![图像](./media/expensify-tutorial/certificatebase64.png)

7. 若要在 Expensify 中启用 SSO，首先需要在应用程序中启用“域控制”。 可以通过[此处](http://help.expensify.com/domain-control)列出的步骤在应用程序中启用“域控制”。 有关其他支持，请与 [Expensify 客户端支持团队](mailto:help@expensify.com)协作。 在启用“域控制”后，执行以下步骤：
   
    ![配置单一登录](./media/expensify-tutorial/tutorial_expensify_51.png)
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 登录到 Expensify 应用程序。
    
    b. 在左侧面板中，单击“设置”，然后导航至 **SAML**。
    
    c. 将“SAML 登录”选项切换为“已启用”。
    
    d. 在记事本中打开来自 Azure AD 的已下载联合元数据，然后复制其内容并粘贴到“标识提供者元数据”文本框。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户 

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”和“用户”，然后选择“所有用户”。

    ![图像](./media/expensify-tutorial/d_users_and_groups.png)

2. 选择屏幕顶部的“新建用户”。

    ![图像](./media/expensify-tutorial/d_adduser.png)

3. 在用户属性中执行以下步骤。

    ![图像](./media/expensify-tutorial/d_userproperties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
 
### <a name="create-an-expensify-test-user"></a>创建 Expensify 测试用户

在本部分中，会在 Expensify 中创建一个名为 Britta Simon 的用户。 若要在 Expensify 平台中添加用户，请与 [Expensify 客户端支持团队](mailto:help@expensify.com)协作。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

本部分将通过向 Britta Simon 授予访问 Expensify 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![图像](./media/expensify-tutorial/d_all_applications.png)

2. 在应用程序列表中，选择“Expensify”。

    ![图像](./media/expensify-tutorial/d_all_proapplications.png)

3. 在左侧菜单中，选择“用户和组”。

    ![图像](./media/expensify-tutorial/d_leftpaneusers.png)

4. 选择“添加”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![图像](./media/expensify-tutorial/d_assign_user.png)

4. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后选择屏幕底部的“选择”按钮。

5. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Expensify 磁贴时，应当会自动登录到 Expensify 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)




