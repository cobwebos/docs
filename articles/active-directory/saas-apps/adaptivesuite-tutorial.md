---
title: 教程：Azure Active Directory 与 Adaptive Insights 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Adaptive Insights 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42c86ec262cd9d3d3db3035d252429e44c1208f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60285260"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>教程：Azure Active Directory 与 Adaptive Insights 集成

本教程介绍如何将 Adaptive Insights 与 Azure Active Directory (Azure AD) 集成。

将 Adaptive Insights 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Adaptive Insights。
- 可以让用户使用其 Azure AD 帐户自动登录到 Adaptive Insights（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>必备组件

若要配置 Azure AD 与 Adaptive Insights 的集成，需要以下项：

- Azure AD 订阅
- 启用了 Adaptive Insights 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Adaptive Insights
2. 配置和测试 Azure AD 单一登录

## <a name="adding-adaptive-insights-from-the-gallery"></a>从库中添加 Adaptive Insights
若要配置 Adaptive Insights 与 Azure AD 的集成，需要从库中将 Adaptive Insights 添加到托管 SaaS 应用列表。

**若要从库中添加 Adaptive Insights，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![image](./media/adaptivesuite-tutorial/selectazuread.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![image](./media/adaptivesuite-tutorial/a_select_app.png)
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![image](./media/adaptivesuite-tutorial/a_new_app.png)

4. 在搜索框中，键入“Adaptive Insights”，在结果面板中选择“Adaptive Insights”，然后单击“添加”按钮添加该应用程序。

     ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，根据名为“Britta Simon”的测试用户的指示配置和测试 Adaptive Insights 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Adaptive Insights 用户。 换句话说，需要在 Azure AD 用户与 Adaptive Insights 中相关用户之间建立链接关系。

若要配置和测试 Adaptive Insights 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Adaptive Insights 测试用户](#create-an-adaptive-insights-test-user)** - 在 Adaptive Insights 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Adaptive Insights 应用程序中配置单一登录。

若要配置 Adaptive Insights 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，在 Adaptive Insights 应用程序集成页上，单击“单一登录”。

    ![image](./media/adaptivesuite-tutorial/B1_B2_Select_SSO.png)

2. 在“选择单一登录方法”对话框中，选择 SAML 模式以启用单一登录。

    ![image](./media/adaptivesuite-tutorial/b1_b2_saml_sso.png)

3. 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“基本 SAML 配置”对话框。

    ![image](./media/adaptivesuite-tutorial/b1-domains_and_urlsedit.png)

4. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > 可以从 Adaptive Insights 的“SAML SSO 设置”页获得“标识符(实体 ID)”和“回复 URL”值。
 
5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“下载”以下载“证书 (Base64)”并将其保存在计算机上。

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certficate.png) 

6. 在“设置 Adaptive Insights”部分中，根据要求复制相应的 URL。

    请注意，URL 可能指明以下信息：

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![image](./media/adaptivesuite-tutorial/d1_samlsonfigure.png) 

7. 在其他 Web 浏览器窗口中，以管理员身份登录 Adaptive Insights 公司站点。

8. 转到“管理员”。

    ![管理员](./media/adaptivesuite-tutorial/IC805644.png "管理员")

9. 在“用户和角色”部分中，单击“管理 SAML SSO 设置”。

    ![管理 SAML SSO 设置](./media/adaptivesuite-tutorial/IC805645.png "管理 SAML SSO 设置")

10. 在“SAML SSO 设置”页上，执行以下步骤：

    ![SAML SSO 设置](./media/adaptivesuite-tutorial/IC805646.png "SAML SSO 设置")

    a. 在“标识提供者名称”文本框中，键入配置名称。

    b. 将从 Azure 门户复制的“Azure AD 标识符”值粘贴到“标识提供者实体 ID”文本框。

    c. 将从 Azure 门户复制的“登录 URL”值粘贴到“标识提供者程序 SSO URL”文本框。

    d. 将从 Azure 门户复制的“注销 URL”值粘贴到“自定义注销 URL”文本框。

    e. 若要上传所下载的证书，请单击“选择文件”。

    f. 针对各对象，选择以下项：

    * 对于“SAML 用户 ID”，请选择“用户的 Adaptive Insights 用户名称”。

    * 对于“SAML 用户 ID 位置”，请选择“主题 NameID 中的用户 ID”。

    * 对于“SAML NameID 格式”，请选择“电子邮件地址”。

    * 对于“启用 SAML”，请选择“允许 SAML SSO 和直接 Adaptive Insights 登录”。

    g. 复制 **Adaptive Insights SSO URL** 并粘贴到 Azure 门户中“Adaptive Insights 域和 URL”部分的“标识符(实体 ID)”和“回复 URL”文本框中。

    h. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![image](./media/adaptivesuite-tutorial/d_users_and_groups.png)

2. 选择屏幕顶部的“新建用户”。

    ![image](./media/adaptivesuite-tutorial/d_adduser.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![image](./media/adaptivesuite-tutorial/d_userproperties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中，键入 brittasimon\@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
 
### <a name="create-an-adaptive-insights-test-user"></a>创建 Adaptive Insights 测试用户

要使 Azure AD 用户能够登录 Adaptive Insights，必须将其预配到 Adaptive Insights 中。 就 Adaptive Insights 来说，预配任务需要手动完成。

**若要配置用户设置，请执行以下步骤：** 

1. 以管理员身份登录 **Adaptive Insights** 公司站点。
2. 转到“管理员”。

   ![管理员](./media/adaptivesuite-tutorial/IC805644.png "管理员")

3. 在“用户和角色”部分中，单击“添加用户”。

   ![添加用户](./media/adaptivesuite-tutorial/IC805648.png "添加用户")
   
4. 在“新建用户”部分中，执行以下步骤：

   ![提交](./media/adaptivesuite-tutorial/IC805649.png "提交")

   a. 键入希望在相关文本框中预配的有效 Azure Active Directory 用户的“姓名”、“登录名”、“电子邮件”、“密码”。

   b. 选择“角色”。

   c. 单击“提交”。

>[!NOTE]
>可以使用其他任何 Adaptive Insights 用户帐户创建工具或 Adaptive Insights 提供的 API 来预配 AAD 用户帐户。
>

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Adaptive Insights 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![image](./media/adaptivesuite-tutorial/d_all_applications.png)

2. 在应用程序列表中，选择“Adaptive Insights”。

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. 在左侧菜单中，选择“用户和组”。

    ![image](./media/adaptivesuite-tutorial/d_leftpaneusers.png)

4. 选择“添加”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![image](./media/adaptivesuite-tutorial/d_assign_user.png)

4. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

5. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Adaptive Insights”磁贴时，应自动登录到 Adaptive Insights 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
