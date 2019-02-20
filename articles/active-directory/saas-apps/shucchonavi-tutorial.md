---
title: 教程：Azure Active Directory 与 Shuccho Navi 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Shuccho Navi 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 32b6676c-d1ec-48c2-91b1-41990ed0560c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1709abdf1d5e90cb253e67b29c0e48f6aa29a712
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209670"
---
# <a name="tutorial-azure-active-directory-integration-with-shuccho-navi"></a>教程：Azure Active Directory 与 Shuccho Navi 集成

在本教程中，了解如何将 Shuccho Navi 与 Azure Active Directory (Azure AD) 集成。

将 Shuccho Navi 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 Shuccho Navi。
- 可让用户使用其 Azure AD 帐户自动登录到 Shuccho Navi（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Shuccho Navi 的集成，需备齐以下项目：

- Azure AD 订阅
- 启用了 Shuccho Navi 单一登录的订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 Shuccho Navi
2. 配置和测试 Azure AD 单一登录

## <a name="adding-shuccho-navi-from-the-gallery"></a>从库中添加 Shuccho Navi
若要配置 Shuccho Navi 与 Azure AD 的集成，需要从库中将 Shuccho Navi 添加到托管 SaaS 应用程序列表。

**从库中添加 Shuccho Navi，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![图像](./media/shucchonavi-tutorial/selectazuread.png)

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![图像](./media/shucchonavi-tutorial/a_select_app.png)
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![图像](./media/shucchonavi-tutorial/a_new_app.png)

4. 在搜索框中，键入“Shuccho Navi”，在结果面板中选择“Shuccho Navi”，然后单击“添加”按钮添加该应用程序。

     ![图像](./media/shucchonavi-tutorial/tutorial_shucchonavi_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于一个名为“Britta Simon”的测试用户使用 Shuccho Navi 配置和测试 Azure AD 单一登录。

对于单一登录到工作帐户，Azure AD 需要知道 Azure AD 用户在 Shuccho Navi 中的对应用户是谁。 换句话说，需要建立 Azure AD 用户与 Shuccho Navi 中相关用户之间的关联关系。

若要配置和测试 Shuccho Navi 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 Shuccho Navi 测试用户](#create-a-shuccho-navi-test-user)** - 在 Shuccho Navi 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 Shuccho Navi 应用程序中配置单一登录。

**配置 Shuccho Navi 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com/)中的“Shuccho Navi”应用程序集成页上，选择“单一登录”。

    ![图像](./media/shucchonavi-tutorial/b1_b2_select_sso.png)

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![图像](./media/shucchonavi-tutorial/b1_b2_saml_sso.png)

3. 在“设置 SAML 单一登录”页上，单击“编辑”按钮，以打开“基本 SAML 配置”对话框。

    ![图像](./media/shucchonavi-tutorial/b1-domains_and_urlsedit.png)

4. 在“基本 SAML 配置”部分中，按照以下步骤操作：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://naviauth.nta.co.jp/saml/login?ENTP_CD=<Your company code>`。

    ![图像](./media/shucchonavi-tutorial/tutorial_shucchonavi_url.png)

    > [!NOTE]
    > 登录 URL 值不是实际值。 请使用实际登录 URL 更新此值。 请联系 [Shuccho Navi 支持团队](mailto:sys_ntabtm@nta.co.jp)来获取此值。
 
5. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“下载”以下载“联合元数据 XML”并将其保存在计算机上。

    ![图像](./media/shucchonavi-tutorial/tutorial_shucchonavi_certificate.png) 

6. 若要在 Shuccho Navi 端配置单一登录，需将下载的“联合元数据 XML”发送给 [Shuccho Navi 支持团队](mailto:sys_ntabtm@nta.co.jp)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![图像](./media/shucchonavi-tutorial/d_users_and_groups.png)

2. 选择屏幕顶部的“新建用户”。

    ![图像](./media/shucchonavi-tutorial/d_adduser.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![图像](./media/shucchonavi-tutorial/d_userproperties.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
 
### <a name="create-a-shuccho-navi-test-user"></a>创建 Shuccho Navi 测试用户

在本部分中，在 Shuccho Navi 中创建名为 Britta Simon 的用户。 请与  [Shuccho Navi 支持团队](mailto:sys_ntabtm@nta.co.jp)协作，将用户添加到 Shuccho Navi 平台中。 使用单一登录前，必须先创建并激活用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Shuccho Navi 的权限，以支持其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![图像](./media/shucchonavi-tutorial/d_all_applications.png)

2. 在应用程序列表中，选择“Shuccho Navi”。

    ![图像](./media/shucchonavi-tutorial/tutorial_shucchonavi_app.png)

3. 在左侧菜单中，选择“用户和组”。

    ![图像](./media/shucchonavi-tutorial/d_leftpaneusers.png)

4. 选择“添加”按钮，然后在“添加分配”对话框中选择“用户和组”。

    ![图像](./media/shucchonavi-tutorial/d_assign_user.png)

4. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

5. 在“添加分配”对话框中，选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Shuccho Navi”磁贴时，用户应自动登录到 Shuccho Navi 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)


