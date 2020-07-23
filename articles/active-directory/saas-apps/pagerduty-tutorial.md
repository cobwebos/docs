---
title: 教程：Azure Active Directory 与 PagerDuty 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 PagerDuty 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 99d9988a02b046562c2517df8a81b8ef5c778ef2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683511"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pagerduty"></a>教程：Azure Active Directory 与 PagerDuty 的单一登录 (SSO) 集成

本教程介绍如何将 PagerDuty 与 Azure Active Directory (Azure AD) 集成。 将 PagerDuty 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 PagerDuty。
* 让用户可使用其 Azure AD 帐户自动登录到 PagerDuty。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 PagerDuty 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* PagerDuty 支持 SP 发起的 SSO 
* 配置 PagerDuty 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-pagerduty-from-the-gallery"></a>从库添加 PagerDuty

若要配置 PagerDuty 与 Azure AD 的集成，需要从库中将 PagerDuty 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中，在搜索框中  键入“PagerDuty”  。
1. 从结果面板中选择“PagerDuty”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-pagerduty"></a>配置并测试 PagerDuty 的 Azure AD 单一登录

使用名为 B.Simon  的测试用户配置并测试 PagerDuty 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 PagerDuty 中的相关用户之间建立链接关系。

若要配置并测试 PagerDuty 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 PagerDuty SSO](#configure-pagerduty-sso)  - 在应用程序端配置单一登录设置。
    1. [创建 PagerDuty 测试用户](#create-pagerduty-test-user)  - 在 PagerDuty 中创建 B.Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中，在“PagerDuty”应用程序集成页上，找到“管理”部分并选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<tenant-name>.pagerduty.com`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<tenant-name>.pagerduty.com`

    c. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<tenant-name>.pagerduty.com`

    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [PagerDuty 客户端支持团队](https://www.pagerduty.com/support/)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 PagerDuty”部分中，根据你的需要复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

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

在本部分中，你将通过授予 B.Simon 访问 PagerDuty 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“PagerDuty”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-pagerduty-sso"></a>配置 PagerDuty SSO

1. 在另一 Web 浏览器窗口中，以管理员身份登录到 Pagerduty 公司站点。

2. 在顶部菜单中，单击“帐户设置”。 

    ![帐户设置](./media/pagerduty-tutorial/ic778535.png "帐户设置")

3. 单击“单一登录”  。

    ![单一登录](./media/pagerduty-tutorial/ic778536.png "单一登录")

4. 在“启用单一登录(SSO)”页上，执行以下步骤： 

    ![启用单一登录](./media/pagerduty-tutorial/ic778537.png "启用单一登录")

    a. 在记事本中打开从 Azure 门户下载的 base-64 编码的证书，将其内容复制到剪贴板，再将其粘贴到“X.509 证书”  文本框中
  
    b. 在“登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”   。
  
    c. 在“注销 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”   。

    d. 选择“允许用户名/密码登录”  。

    e. 选中“需要精确的身份验证上下文比较”  复选框。

    f. 单击 **“保存更改”** 。

### <a name="create-pagerduty-test-user"></a>创建 PagerDuty 测试用户

若要使 Azure AD 用户能够登录到 PagerDuty，必须将其预配到 PagerDuty 中。 使用 PagerDuty 时，预配属手动任务。

> [!NOTE]
> 可以使用任何其他 PagerDuty 用户帐户创建工具或 PagerDuty 提供的 API 来预配 Azure Active Directory 用户帐户。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到你的 Pagerduty  租户。

2. 在顶部菜单中，单击“用户”。 

3. 单击“添加用户”。 
   
    ![添加用户](./media/pagerduty-tutorial/ic778539.png "添加用户")

4.  在“邀请团队”对话框中，执行以下步骤： 
   
    ![邀请团队](./media/pagerduty-tutorial/ic778540.png "邀请团队")

    a. 键入用户的“名字和姓氏”  ，例如“B.Simon”  。 
   
    b. 输入用户的电子邮件  地址，例如“b.simon\@contoso.com”  。
   
    c. 依次单击“添加”、“发送邀请”   。
   
    > [!NOTE]
    > 所有已添加的用户都会收到创建 PagerDuty 帐户的邀请。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 PagerDuty 磁贴时，应当会自动登录到已为其设置了 SSO 的 PagerDuty。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [尝试通过 Azure AD 使用 PagerDuty](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何使用高级可见性和控制保护 PagerDuty](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

