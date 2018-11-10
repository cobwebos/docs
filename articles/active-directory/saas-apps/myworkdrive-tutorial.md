---
title: 教程：Azure Active Directory 与 MyWorkDrive 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 MyWorkDrive 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 7310d300c68399c31d9580f070602aa3adbc75e3
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094050"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>教程：Azure Active Directory 与 MyWorkDrive 的集成

在本教程中，你将了解如何将 MyWorkDrive 与 Azure Active Directory (Azure AD) 进行集成。

将 MyWorkDrive 与 Azure AD 集成可提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 MyWorkDrive。
- 可以让用户使用其 Azure AD 帐户自动登录到 MyWorkDrive（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 MyWorkDrive 的集成，需要具有以下项：

- Azure AD 订阅
- 启用了单一登录的 MyWorkDrive 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 MyWorkDrive
2. 配置和测试 Azure AD 单一登录

## <a name="adding-myworkdrive-from-the-gallery"></a>从库中添加 MyWorkDrive
要配置 MyWorkDrive 与 Azure AD 的集成，需要从库中将 MyWorkDrive 添加到托管 SaaS 应用列表。

**若要从库中添加 MyWorkDrive，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![图像](./media/myworkdrive-tutorial/selectazuread.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![图像](./media/myworkdrive-tutorial/a_select_app.png)
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![图像](./media/myworkdrive-tutorial/a_new_app.png)

4. 在搜索框中，键入“MyWorkDrive”，在结果面板中选择“MyWorkDrive”，然后单击“添加”按钮添加该应用程序。

     ![图像](./media/myworkdrive-tutorial/tutorial_myworkdrive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 Britta Simon 的测试用户配置和测试 MyWorkDrive 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 MyWorkDrive 用户。 换句话说，需要在 Azure AD 用户与 MyWorkDrive 中相关用户之间建立链接关系。

若要配置和测试 MyWorkDrive 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 MyWorkDrive 测试用户](#create-a-myworkdrive-test-user)** - 在 MyWorkDrive 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 MyWorkDrive 应用程序中配置单一登录。

**若要配置 MyWorkDrive 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **MyWorkDrive** 应用程序集成页上，选择“单一登录”。

    ![图像](./media/myworkdrive-tutorial/B1_B2_Select_SSO.png)

2. 在“选择单一登录方法”对话框中，选择 SAML 模式以启用单一登录。

    ![图像](./media/myworkdrive-tutorial/b1_b2_saml_sso.png)

3. 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“基本 SAML 配置”对话框。

    ![图像](./media/myworkdrive-tutorial/b1-domains_and_urlsedit.png)

4. 如果要在 **IDP** 发起的模式下配置应用程序，请在“基本 SAML 配置”部分中执行以下步骤：

    ![图像](./media/myworkdrive-tutorial/tutorial_myworkdrive_url.png)

    在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

5. 如果要在 **SP** 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    ![图像](./media/myworkdrive-tutorial/tutorial_myworkdrive_url1.png)

    在“登录 URL”文本框中，使用以下模式键入 URL： `https://<SERVER.DOMAIN.COM>/Account/Login-saml` 

    > [!NOTE]
    > 这些不是实际值。 请使用实际的回复 URL 和注销 URL 更新这些值。  输入你自己公司的 MyWorkDrive 服务器主机名，例如：
    > 
    > 回复 URL：`https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > 登录 URL：`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > 如果不确定如何设置自己的主机名和 SSL 证书的这些值，请联系 MyWorkDrive 客户端支持团队。

6. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，单击“复制”图标，以复制“应用联合元数据 URL”，并单击“下载”以下载“证书(Base64)”并将其保存在计算机上。

    ![图像](./media/myworkdrive-tutorial/tutorial_myworkdrive_certficate.png) 

7. 在“设置 MyWorkDrive”部分中，根据要求复制相应的 URL。

    请注意，URL 可能指明以下信息：

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![图像](./media/myworkdrive-tutorial/d1_samlsonfigure.png) 

8. 若要在 MyWorkDrive 端配置单一登录，请下载**证书(Base64)、注销 URL、SAML 实体 ID 和 SAML 单一登录服务 URL** 并手动在 MyWorkDrive 服务器上配置它们，或者将 Azure **应用联合元数据 URL** 复制并粘贴到 MyWorkDrive 服务器管理面板 SAML Azure AD 配置屏幕中。 有关更多信息，请联系 [MyWorkDrive 支持团队](mailto:support@myworkdrive.com)。

    
### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![图像](./media/myworkdrive-tutorial/d_users_and_groups.png)

2. 选择屏幕顶部的“新建用户”。

    ![图像](./media/myworkdrive-tutorial/d_adduser.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![图像](./media/myworkdrive-tutorial/d_userproperties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
 
### <a name="create-a-myworkdrive-test-user"></a>创建 MyWorkDrive 测试用户

在本部分中，将在 MyWorkDrive 中创建一个名为 Britta Simon 的用户。 与  [MyWorkDrive 支持团队](mailto:support@myworkdrive.com)合作以在 MyWorkDrive 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予对 MyWorkDrive 的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![图像](./media/myworkdrive-tutorial/d_all_applications.png)

2. 在应用程序列表中，选择“MyWorkDrive”。

    ![图像](./media/myworkdrive-tutorial/tutorial_myworkdrive_app.png)

3. 在左侧菜单中，选择“用户和组”。

    ![图像](./media/myworkdrive-tutorial/d_leftpaneusers.png)

4. 选择“添加”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![图像](./media/myworkdrive-tutorial/d_assign_user.png)

4. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

5. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 MyWorkDrive 磁贴时，应当会自动登录到 MyWorkDrive 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
