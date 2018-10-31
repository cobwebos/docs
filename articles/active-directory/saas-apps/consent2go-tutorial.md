---
title: 教程：Azure Active Directory 与 Consent2Go 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Consent2Go 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: ea93bc02-58ca-4468-84ff-359888fc6183
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2018
ms.author: jeedes
ms.openlocfilehash: 89fc0b69a3a7ca3f795b4ae0e79b11a4bcd9c9fb
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2018
ms.locfileid: "49432420"
---
# <a name="tutorial-azure-active-directory-integration-with-consent2go"></a>教程：Azure Active Directory 与 Consent2Go 集成

本教程介绍如何将 Consent2Go 与 Azure Active Directory (Azure AD) 集成。

将 Consent2Go 与 Azure AD 集成提供以下优势：

- 可在 Azure AD 中控制谁有权访问 Consent2Go。
- 可使用户通过其 Azure AD 帐户自动登录 Consent2Go（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Consent2Go 的集成，需要以下项：

- Azure AD 订阅
- 启用了 Consent2Go 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 Consent2Go
2. 配置和测试 Azure AD 单一登录

## <a name="adding-consent2go-from-the-gallery"></a>从库添加 Consent2Go
要配置 Consent2Go 与 Azure AD 的集成，需要从库中将 Consent2Go 添加到托管 SaaS 应用列表。

若要从库中添加 Consent2Go，请执行以下步骤：

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![图像](./media/consent2go-tutorial/selectazuread.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![图像](./media/consent2go-tutorial/a_select_app.png)
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![图像](./media/consent2go-tutorial/a_new_app.png)

4. 在搜索框中，键入“Consent2Go”，在结果面板中选择“Consent2Go”，然后单击“添加”按钮添加该应用程序。

     ![图像](./media/consent2go-tutorial/tutorial_consent2go_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Consent2Go 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 Consent2Go 用户。 换句话说，需要建立 Azure AD 用户与 Consent2Go 中相关用户之间的链接关系。

若要配置和测试 Consent2Go 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Consent2Go 测试用户](#create-a-consent2go-test-user)** - 在 Consent2Go 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Consent2Go 应用程序中配置单一登录。

若要配置 Consent2Go 的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中的“Consent2Go”应用程序集成页上，选择“单一登录”。

    ![图像](./media/consent2go-tutorial/b1_b2_select_sso.png)

2. 在“选择单一登录方法”对话框中，对 SAML 模式单击“选择”以启用单一登录。

    ![图像](./media/consent2go-tutorial/b1_b2_saml_sso.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”按钮以打开“基本 SAML 配置”对话框。

    ![图像](./media/consent2go-tutorial/b1-domains_and_urlsedit.png)

4. 在“基本 SAML 配置”部分中，执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`   https://www.mcbschools.com/Login`

    ![图像](./media/consent2go-tutorial/tutorial_consent2go_url.png)

5. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，单击“复制”图标，以复制“应用联合元数据 URL”，并将它保存在计算机上。

    ![图像](./media/consent2go-tutorial/tutorial_consent2go_certificate.png) 

6. 若要在 Consent2Go 端配置单一登录，需要将复制的应用联合元数据 URL 发送给 [Consent2Go 支持团队](mailto:support@consent2go.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左窗格中，依次选择“Azure Active Directory”、“用户”、“所有用户”。

    ![图像](./media/consent2go-tutorial/d_users_and_groups.png)

2. 选择屏幕顶部的“新建用户”。

    ![图像](./media/consent2go-tutorial/d_adduser.png)

3. 在“用户属性”中，执行以下步骤。

    ![图像](./media/consent2go-tutorial/d_userproperties.png)

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
 
### <a name="create-a-consent2go-test-user"></a>创建 Consent2Go 测试用户

本部分需在 Consent2Go 中创建名为“Britta Simon”的用户。 请与 [Consent2Go 支持团队](mailto:support@consent2go.com)协作，将用户添加到 Consent2Go 平台中。 使用单一登录前，必须先创建并激活用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Consent2Go 的权限，支持其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![图像](./media/consent2go-tutorial/d_all_applications.png)

2. 在应用程序列表中，选择“Consent2Go”。

    ![图像](./media/consent2go-tutorial/tutorial_consent2go_app.png)

3. 在左侧菜单中，选择“用户和组”。

    ![图像](./media/consent2go-tutorial/d_leftpaneusers.png)

4. 选择“添加”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![图像](./media/consent2go-tutorial/d_assign_user.png)

4. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

5. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 Consent2Go 磁贴时，应当会自动登录到 Consent2Go 应用程序。
有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](../active-directory-saas-access-panel-introduction.md)（访问面板简介）。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)


