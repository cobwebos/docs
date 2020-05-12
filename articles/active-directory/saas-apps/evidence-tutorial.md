---
title: 教程：Azure Active Directory 与 Evidence.com 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Evidence.com 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f9a7cb7c-ff67-40dc-872c-1fa35f9dd03b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b2e04c49b05173c6eef27da30c96e2fd8b40b5d
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734665"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evidencecom"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Evidence.com 的集成

本教程介绍如何将 Evidence.com 与 Azure Active Directory (Azure AD) 集成。 将 Evidence.com 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Evidence.com。
* 让用户使用其 Azure AD 帐户自动登录到 Evidence.com。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Evidence.com 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Evidence.com 支持 **SP** 发起的 SSO
* 配置 Evidence.com 后，就可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-evidencecom-from-the-gallery"></a>从库中添加 Evidence.com

若要配置 Evidence.com 与 Azure AD 的集成，需要从库中将 Evidence.com 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中键入“Evidence.com”   。
1. 从结果面板中选择“Evidence.com”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-evidencecom"></a>配置并测试 Evidence.com 的 Azure AD 单一登录

使用名为 B.Simon  的测试用户配置并测试 Evidence.com 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Evidence.com 相关用户之间建立关联。

若要配置并测试 Evidence.com 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 Evidence.com SSO](#configure-evidencecom-sso)  - 在应用程序端配置单一登录。
    1. [创建 Evidence.com 测试用户](#create-evidencecom-test-user)  - 在 Evidence.com 中创建 B.Simon 的对应用户，并将其关联到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Evidence.com”应用程序集成页上，找到“管理”部分并选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“设置 SAML 单一登录”页上，执行以下步骤  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<yourtenant>.evidence.com`

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<yourtenant>.evidence.com`

    c. 在 **“回复 URL”** 文本框中，使用以下模式键入 URL：`https://<your tenant>.evidence.com/?class=UIX&proc=Login`
    
    > [!NOTE]
    > 这些不是实际值。 请使用实际登录 URL、标识符和回复 URL 更新这些值。 请联系 [Evidence.com 客户端支持团队](https://communities.taser.com/support/SupportContactUs?typ=LE)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“使用 SAML 设置单一登录”  页上，在“SAML 签名证书”  部分中，单击“下载”  以根据要求从给定的选项下载**证书(Base64)** 并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Evidence.com”部分，根据要求复制相应的 URL  。

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

在本部分中，你将通过授予 B.Simon 访问 Evidence.com 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Evidence.com”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-evidencecom-sso"></a>配置 Evidence.com SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 Evidence.com 租户，然后导航到“管理”选项卡。 

2. 单击“代理单一登录” 

3. 选择“基于 SAML 的单一登录” 

4. 将 Azure 门户中显示的“Azure AD 标识符”、“登录 URL”和“注销 URL”值复制到 Evidence.com 中的对应字段。   

5. 在记事本中打开下载的“证书(Base64)”文件，将其内容复制到剪贴板，然后将其粘贴到“安全证书”框中  。 

6. 在 Evidence.com 中保存配置。

### <a name="create-evidencecom-test-user"></a>创建 Evidence.com 测试用户

要使 Azure AD 用户能够登录，必须在 Evidence.com 应用程序中为其预配访问权限。 本部分介绍了如何在 Evidence.com 中创建 Azure AD 用户帐户

**若要在 Evidence.com 中预配用户帐户，请执行以下步骤：**

1. 在 Web 浏览器窗口中，以管理员身份登录到 Evidence.com 公司站点。

2. 导航到“管理”选项卡。 

3. 单击“添加用户”。 

4. 单击 **“添加”** 按钮。

5. 添加的用户的“电子邮件地址”必须与要向其授予访问权限的用户在 Azure AD 中的用户名相匹配。  如果在组织中，用户名和电子邮件地址不是同一个值，则可以使用 Azure 门户的“Evidence.com”>“属性”>“单一登录”部分将发送到 Evidence.com 的 nameidenitifer 更改为电子邮件地址。 

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Evidence.com 磁贴时，应会自动登录到为其设置了 SSO 的 Evidence.com。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Evidence.com](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Evidence.com](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

