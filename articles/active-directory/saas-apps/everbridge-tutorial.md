---
title: 教程：Azure Active Directory 与 EverBridge 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 EverBridge 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: 40645db589409ac80c69f1e7595e20869b08f5d0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194672"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>教程：Azure Active Directory 与 EverBridge 集成

本教程介绍如何将 EverBridge 与 Azure Active Directory (Azure AD) 集成。

将 EverBridge 与 Azure AD 集成提供以下优势：

- 可以在 Azure AD 中控制谁有权访问 EverBridge。
- 可以让用户使用其 Azure AD 帐户自动登录到 EverBridge（单一登录）。
- 可在中心位置（即 Azure 门户）管理帐户。

如需了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 EverBridge 的集成，需要以下项：

- Azure AD 订阅
- 启用了单一登录的 EverBridge 订阅

> [!NOTE]
> 为了测试本教程中的步骤，我们不建议使用生产环境。

测试本教程中的步骤应遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月的试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述

在本教程中，将在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 EverBridge
2. 配置和测试 Azure AD 单一登录

## <a name="adding-everbridge-from-the-gallery"></a>从库中添加 EverBridge

若要配置 EverBridge 与 Azure AD 的集成，需要从库中将 EverBridge 添加到托管 SaaS 应用列表。

**若要从库中添加 EverBridge，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

2. 导航到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”按钮。

    ![“新增应用程序”按钮][3]

4. 在搜索框中键入“EverBridge”，在结果面板中选择“EverBridge”，然后单击“添加”按钮添加该应用程序。

    ![结果列表中的 EverBridge](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，基于名为“Britta Simon”的测试用户配置和测试 EverBridge 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 EverBridge 用户。 换句话说，需要建立 Azure AD 用户与 EverBridge 中相关用户之间的关联。

若要配置和测试 EverBridge 的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)** - 让用户使用此功能。
2. **[创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
3. **[创建 EverBridge 测试用户](#creating-an-everbridge-test-user)** - 在 EverBridge 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
4. **[分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)** - 让 Britta Simon 使用 Azure AD 单一登录。
5. **[测试单一登录](#testing-single-sign-on)** - 验证配置是否正常工作。

### <a name="configuring-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 EverBridge 应用程序中配置单一登录。

**若要配置 EverBridge 的 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的 EverBridge 应用程序集成页上，单击“单一登录”。

    ![配置单一登录链接][4]

2. 在“选择单一登录方法”对话框中，单击“SAML”模式对应的“选择”，以启用单一登录。

    ![配置单一登录](common/tutorial_general_301.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框。

    ![配置单一登录](common/editconfigure.png)

    >[!NOTE]
    >你需要同时在两端（即在 Azure 门户和 Everbridge 门户上）将应用程序配置为“管理员门户”或“成员门户”。

4. 若要将 **EverBridge** 应用程序配置为 **EverBridge 管理员门户**，请在“基本 SAML 配置”部分中执行以下步骤：

    ![EverBridge 域和 URL 单一登录信息](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://sso.everbridge.net/<API_Name>`

    b. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [EverBridge 支持团队](mailto:support@everbridge.com)获取这些值。

5. 若要将 **EverBridge** 应用程序配置为 **EverBridge 成员门户**，请在“基本 SAML 配置”部分中执行以下步骤：

    * 如果要在 **IDP** 发起的模式下配置应用程序，请执行以下步骤：

        ![EverBridge 域和 URL 单一登录信息](./media/everbridge-tutorial/tutorial_everbridge_url1.png)

        * 在“标识符”文本框中，使用以下模式键入 URL：`https://sso.everbridge.net/<API_Name>/<Organization_ID>`

        * 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

    * 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

        ![EverBridge 域和 URL 单一登录信息](./media/everbridge-tutorial/tutorial_everbridge_url2.png)

        * 在“登录 URL”文本框中，使用以下模式键入 URL： `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

    > [!NOTE]
    > 这些不是实际值。 使用实际标识符、回复 URL 和登录 URL 更新这些值。 请联系 [EverBridge 支持团队](mailto:support@everbridge.com)获取这些值。

6. 在“SAML 签名证书”页的“SAML 签名证书”部分，单击“下载”以下载“联合元数据 XML”并将元数据文件保存在计算机上。

    ![证书下载链接](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

7. 在“设置 EverBridge”部分中，根据要求复制相应的 URL。

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

    ![EverBridge 配置](common/configuresection.png)

8. 若要为作为 **EverBridge 管理员门户**应用程序的 **EverBridge** 配置 SSO，请执行以下步骤： 
 
9. 在另一个 Web 浏览器窗口中，以管理员身份登录到 EverBridge。

9. 在顶部菜单中，单击“设置”选项卡，并在“安全”下选择“单一登录”。
   
    ![配置单一登录](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
    a. 在“名称”文本框中，键入标识符提供者的名称（例如：你的公司名称）。
   
    b. 在“API 名称”文本框中，键入 API 的名称。
   
    c. 单击“选择文件”按钮，以上传从 Azure 门户下载的元数据文件。
   
    d. 在“SAML 标识位置”中选择“标识位于 Subject 语句的 NameIdentifier 元素中”。
   
    e. 在“标识提供者登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值。
   
    f. 在“服务提供程序发起的请求绑定”中选择“HTTP 重定向”。

    g. 单击“保存”

10. 若要为作为 **EverBridge 成员门户**的 **EverBridge** 应用程序配置单一登录，需要将下载的**联合身份验证元数据 XML** 发送给 [Everbridge 支持团队](mailto:support@everbridge.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="creating-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”。

    ![创建 Azure AD 用户][100]

2. 选择屏幕顶部的“新建用户”。

    ![创建 Azure AD 测试用户](common/create_aaduser_01.png) 

3. 在“用户属性”中，按照以下步骤操作。

    ![创建 Azure AD 测试用户](common/create_aaduser_02.png)

    a. 在“名称”字段中，输入 BrittaSimon。
  
    b. 在“用户名”字段中键入 brittasimon@yourcompanydomain.extension  
    例如： BrittaSimon@contoso.com

    c. 选择“属性”，再选择“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择“创建”。
  
### <a name="creating-an-everbridge-test-user"></a>创建 EverBridge 测试用户

在本部分中，会在 Everbridge 中创建一个名为 Britta Simon 的用户。 与 [EverBridge 支持团队](mailto:support@everbridge.com)协作，将用户添加到 EverBridge 平台。

### <a name="assigning-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 EverBridge 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201]

2. 在应用程序列表中，选择“EverBridge”。

    ![配置单一登录](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

3. 在左侧菜单中，单击“用户和组”。

    ![分配用户][202]

4. 单击“添加”按钮。 然后在“添加分配”对话框中选择“用户和组”。

    ![分配用户][203]

5. 在“用户和组”对话框中，选择“用户”列表中的 Britta Simon，然后单击屏幕底部的“选择”按钮。

6. 在“添加分配”对话框中，选择“分配”按钮。

### <a name="testing-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

当在访问面板中单击 EverBridge 磁贴时，应当会自动登录到 EverBridge 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
