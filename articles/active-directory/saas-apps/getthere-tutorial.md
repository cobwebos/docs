---
title: 教程：Azure Active Directory 与 GetThere 集成 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 和 GetThere 之间的单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0441087e-953f-4b51-9842-316da7b72392
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: jeedes
ms.openlocfilehash: bcefa3966a6c854f02ce7b3a75306b3d1c888ecd
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49432132"
---
# <a name="tutorial-azure-active-directory-integration-with-getthere"></a>教程：Azure Active Directory 与 GetThere 集成

本教程介绍如何将 GetThere 与 Azure Active Directory (Azure AD) 集成。

将 GetThere 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 GetThere。
- 可让用户使用其 Azure AD 帐户自动登录到 GetThere（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 GetThere 的集成，需要以下项目：

- Azure AD 订阅
- 启用了 GetThere 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 GetThere
2. 配置和测试 Azure AD 单一登录

## <a name="adding-getthere-from-the-gallery"></a>从库中添加 GetThere
要配置 GetThere 与 Azure AD 的集成，需要从库中将 GetThere 添加到托管 SaaS 应用列表。

若要从库中添加 GetThere，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![图像](./media/getthere-tutorial/selectazuread.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![图像](./media/getthere-tutorial/a_select_app.png)
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![图像](./media/getthere-tutorial/a_new_app.png)

4. 在搜索框中，键入“GetThere”，在结果面板中选择“GetThere”，然后单击“添加”按钮添加该应用程序。

     ![图像](./media/getthere-tutorial/tutorial_getthere_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 GetThere 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 GetThere 用户。 换句话说，需要建立 Azure AD 用户与 GetThere 中相关用户之间的链接关系。

若要配置和测试 GetThere 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. [创建 GetThere 测试用户](#create-a-getthere-test-user) - 在 GetThere 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 GetThere 应用程序中配置单一登录。

若要配置 GetThere 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“GetThere”应用程序集成页上，选择“单一登录”。

    ![图像](./media/getthere-tutorial/B1_B2_Select_SSO.png)

2. 在“选择单一登录方法”对话框中，选择“SAML”模式以启用单一登录。

    ![图像](./media/getthere-tutorial/b1_b2_saml_sso.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“基本 SAML 配置”对话框。

    ![图像](./media/getthere-tutorial/b1-domains_and_urlsedit.png)

4. 在“基本 SAML 配置”部分中，执行以下步骤：

    ![图像](./media/getthere-tutorial/tutorial_getthere_url.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“标识符”文本框中，键入以下任意一个 URL：
    | |
    |--|
    | `getthere.com` |
    | `http://idp.getthere.com` |

    b. 在“回复 URL”文本框中，键入以下任意一个 URL：
    | |
    |--|
    | `https://wx1.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/post.act` |
    | `https://gtx2-gcte2.getthere.net/login/saml/ssoaasvalidate.act` |
    | `https://wx1.getthere.net/login/saml/ssoaavalidate.act` |
    
5. GetThere 应用程序在 username 声明中需要的唯一的 Username 值。 用户可以为 Username 声明映射正确的值。 在下面的示例中，我们已将 Username 映射到 user.mail，但可以根据组织设置更改映射。 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“用户属性”对话框。

    ![图像](./media/getthere-tutorial/i4-attribute.png)

6. 在“用户属性”对话框的“用户声明”部分中，按上图所示配置 SAML 令牌属性，并执行以下步骤：
    
    | 名称 |  源属性 |  命名空间 |
    | ---------------| --------------- | --------------- |
    | Sitename | “根据组织提供值” | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sitename |
    | 用户名 |  user.mail | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/username |
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 单击“添加新声明”以打开“管理用户声明”对话框。

    ![图像](./media/getthere-tutorial/i2-attribute.png)

    ![图像](./media/getthere-tutorial/i3-attribute.png)

    b. 在“名称”文本框中，键入为该行显示的属性名称。

    c. 在“命名空间”文本框中，键入为该行显示的属性命名空间。

    d. 选择“源”作为“属性”。

    e. 在“源属性”列表中，键入为该行显示的属性值。

    f. 单击“ **保存**”。

7. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“下载”以下载“证书 (Base64)”并将其保存在计算机上。

    ![图像](./media/getthere-tutorial/tutorial_getthere_certficate.png) 

8. 在“设置 GetThere”部分中，根据要求复制相应的 URL。

    请注意，URL 可能指明以下信息：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![图像](./media/getthere-tutorial/d1_samlsonfigure.png) 

9. 若要在 GetThere 端配置单一登录，你需要将已下载的“证书 (Base64)”和已复制的“登录 URL、Azure AD 标识符、注销 URL”发送到 [GetThere 客户端支持团队](mailto:dataintegration@sabre.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”。

    ![图像](./media/getthere-tutorial/d_users_and_groups.png)

2. 选择屏幕顶部的“新建用户”。

    ![图像](./media/getthere-tutorial/d_adduser.png)

3. 在“用户属性”中，执行以下步骤。

    ![图像](./media/getthere-tutorial/d_userproperties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
 
### <a name="create-a-getthere-test-user"></a>创建 GetThere 测试用户

在本部分中，将在 GetThere 中创建一个名为 Britta Simon 的用户。 请与 [GetThere 客户端支持团队](mailto:dataintegration@sabre.com)协作，将用户添加到 GetThere 平台中。 使用单一登录前，必须先创建并激活用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予 GetThere 的访问权限支持其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![图像](./media/getthere-tutorial/d_all_applications.png)

2. 在应用程序列表中，选择“GetThere”。

    ![图像](./media/getthere-tutorial/tutorial_getthere_app.png)

3. 在左侧菜单中，选择“用户和组”。

    ![图像](./media/getthere-tutorial/d_leftpaneusers.png)

4. 选择“添加”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![图像](./media/getthere-tutorial/d_assign_user.png)

4. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

5. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“GetThere”磁贴，即可自动登录到 GetThere 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
