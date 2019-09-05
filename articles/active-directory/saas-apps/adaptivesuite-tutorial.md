---
title: 教程：Azure Active Directory 与 Adaptive Insights 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Adaptive Insights 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad059e6047dd883c0a5aab0d714d999840050584
ms.sourcegitcommit: ac29357a47cc05afdf0f84834de5277598f4d87c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2019
ms.locfileid: "70213612"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>教程：将 Adaptive Insights 与 Azure Active Directory 集成

本教程介绍如何将 Adaptive Insights 与 Azure Active Directory (Azure AD) 集成。 将 Adaptive Insights 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Adaptive Insights。
* 让用户使用其 Azure AD 帐户自动登录到 Adaptive Insights。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Adaptive Insights 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Adaptive Insights 支持启用了 **IDP** 的 SSO

## <a name="adding-adaptive-insights-from-the-gallery"></a>从库中添加 Adaptive Insights

若要配置 Adaptive Insights 与 Azure AD 的集成，需要从库中将 Adaptive Insights 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Adaptive Insights”   。
1. 从结果面板中选择“Adaptive Insights”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 Adaptive Insights 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Adaptive Insights 中相关用户之间建立链接关系。

若要配置和测试 Adaptive Insights 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 Adaptive Insights SSO](#configure-adaptive-insights-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
5. **[创建 Adaptive Insights 测试用户](#create-adaptive-insights-test-user)** - 在 Adaptive Insights 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Adaptive Insights”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > 可以从 Adaptive Insights 的“SAML SSO 设置”  页获得“标识符(实体 ID)”和“回复 URL”值。

4. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

6. 在“设置 Adaptive Insights”部分中，根据要求复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>配置 Adaptive Insights SSO

1. 在其他 Web 浏览器窗口中，以管理员身份登录 Adaptive Insights 公司站点。

2. 转到“管理”  。

    ![管理员](./media/adaptivesuite-tutorial/ic805644.png "管理员")

3. 在“用户和角色”  部分中，单击“SAML SSO 设置”  。

    ![管理 SAML SSO 设置](./media/adaptivesuite-tutorial/ic805645.png "管理 SAML SSO 设置")

4. 在“SAML SSO 设置”  页上，执行以下步骤：

    ![SAML SSO 设置](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO 设置")

    a. 在“标识提供者名称”  文本框中，键入配置名称。

    b. 将从 Azure 门户复制的“Azure AD 标识符”  值粘贴到“标识提供者实体 ID”  文本框中。

    c. 将从 Azure 门户复制的“登录 URL”  值粘贴到“标识提供者程序 SSO URL”  文本框。

    d. 将从 Azure 门户复制的“注销 URL”  值粘贴到“自定义注销 URL”  文本框。

    e. 若要上传所下载的证书，请单击“选择文件”  。

    f. 针对各对象，选择以下项：

     * 对于“SAML 用户 ID”  ，请选择“用户的 Adaptive Insights 用户名称”  。

     * 对于“SAML 用户 ID 位置”  ，请选择“主题 NameID 中的用户 ID”  。

     * 对于“SAML NameID 格式”  ，请选择“电子邮件地址”  。

     * 对于“启用 SAML”  ，请选择“允许 SAML SSO 和直接 Adaptive Insights 登录”  。

    g. 复制 **Adaptive Insights SSO URL**，并将其粘贴到 Azure 门户的“基本 SAML 配置”  部分的“标识符(实体 ID)”  和“回复 URL”  文本框中。

    h. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Adaptive Insights 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Adaptive Insights”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-adaptive-insights-test-user"></a>创建 Adaptive Insights 测试用户

要使 Azure AD 用户能够登录 Adaptive Insights，必须将其预配到 Adaptive Insights 中。 就 Adaptive Insights 来说，预配任务需要手动完成。

**若要配置用户设置，请执行以下步骤：**

1. 以管理员身份登录 Adaptive Insights 公司网站  。

2. 转到“管理”  。

   ![管理员](./media/adaptivesuite-tutorial/IC805644.png "管理员")

3. 在“用户和角色”  部分中，单击“用户”  。

   ![添加用户](./media/adaptivesuite-tutorial/IC805648.png "添加用户")

4. 在“新建用户”  部分中，执行以下步骤：

   ![提交](./media/adaptivesuite-tutorial/IC805649.png "提交")

   a. 将要预配的有效 Azure Active Directory 用户的**姓名**、**用户名**、**电子邮件**、**密码**键入到相关文本框中。

   b. 选择“角色”  。

   c. 单击“提交”  。

> [!NOTE]
> 可以使用其他任何 Adaptive Insights 用户帐户创建工具或 Adaptive Insights 提供的 API 来预配 AAD 用户帐户。

### <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Adaptive Insights 磁贴时，应会自动登录到为其设置了 SSO 的 Adaptive Insights。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

