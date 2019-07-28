---
title: 教程：Azure Active Directory 与 Civic Platform 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Civic Platform 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d790454-143e-40ac-b3cb-5a256977b4db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/25/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ccf124c5a4160715df4e685e405dcd591c49ae7
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68496442"
---
# <a name="tutorial-integrate-civic-platform-with-azure-active-directory"></a>教程：将 Civic Platform 与 Azure Active Directory 集成

本教程介绍如何将 Civic Platform 与 Azure Active Directory (Azure AD) 集成。 将 Civic Platform 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Civic Platform。
* 让用户使用其 Azure AD 帐户自动登录到 Civic Platform。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月免费试用版。
* 已启用 Civic Platform 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Civic Platform 支持 **SP** 发起的 SSO





## <a name="adding-civic-platform-from-the-gallery"></a>从库中添加 Civic Platform

若要配置 Civic Platform 与 Azure AD 的集成，需要从库中将 Civic Platform 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Civic Platform**。 
1. 从结果面板中选择“Civic Platform”，然后添加该应用。  在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Civic Platform 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Civic Platform 中的相关用户之间建立链接关系。

若要配置并测试 Civic Platform 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 Civic Platform SSO](#configure-civic-platform-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
5. **[创建 Civic Platform 测试用户](#create-civic-platform-test-user)** - 在 Civic Platform 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Civic Platform”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.accela.com` 

    b. 在“标识符(实体 ID)”文本框中，键入 URL：`civicplatform.accela.com` 

    > [!NOTE]
    > “登录 URL”值不是实际值。 请使用实际登录 URL 更新此值。 请联系 [Civic Platform 客户端支持团队](mailto:skale@accela.com)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“设置 SAML 单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

1. 在 Azure AD 中导航到“Azure Active Directory” > “应用注册”，然后选择你的应用程序。  

1. 复制“目录(租户) ID”并将其存储在记事本中。 

    ![复制目录（租户）ID 并将其存储在应用代码中](media/civic-platform-tutorial/directoryid.png)

1. 复制“应用程序 ID”并将其存储在记事本中。 

   ![复制应用程序（客户端）ID](media/civic-platform-tutorial/applicationid.png)

1. 在 Azure AD 中导航到“Azure Active Directory” > “应用注册”，然后选择你的应用程序。   选择“证书和机密”。 

1. 选择“客户端机密”->“新建客户端机密”。 

1. 提供机密的说明和持续时间。 完成后，选择“添加”。 

   > [!NOTE]
   > 保存客户端机密后，将显示客户端机密的值。 复制此值，因为稍后不能检索密钥。

   ![复制机密值，因为以后不再可以检索到此值](media/civic-platform-tutorial/secretkey.png)

### <a name="configure-civic-platform-sso"></a>配置 Civic Platform SSO

1. 打开新的 Web 浏览器窗口，以管理员身份登录到 Atlassian Cloud 公司站点。

1. 单击“标准选项”。 

    ![证书下载链接](media/civic-platform-tutorial/standard-choices.png)

1. 创建标准选项 **ssoconfig**。

1. 搜索 **ssoconfig** 并提交。

    ![证书下载链接](media/civic-platform-tutorial/sso-config.png)

1. 单击红点展开“SSOCONFIG”。

    ![证书下载链接](media/civic-platform-tutorial/sso-config01.png)

1. 在以下步骤中提供 SSO 相关的配置信息：

    ![证书下载链接](media/civic-platform-tutorial/sso-config02.png)

    1. 在“applicationid”字段中，输入从 Azure 门户复制的“应用程序 ID”值。  

    1. 在“clientSecret”字段中，输入从 Azure 门户复制的“机密”值。  

    1. 在“directoryId”字段中，输入从 Azure 门户复制的“目录(租户) ID”值。  

    1. 输入 idpName。 例如 `Azure`。

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

在本部分，你将通过授予 B.Simon 访问 Civic Platform 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“Civic Platform”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-civic-platform-test-user"></a>创建 Civic Platform 测试用户

在本部分，你将在 Civic Platform 中创建名为 B.Simon 的用户。 在 [Civic Platform 客户端支持团队](mailto:skale@accela.com)的配合下添加用户。 使用单一登录前，必须先创建并激活用户。

### <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Civic Platform”磁贴时，应会自动登录到设置了 SSO 的 Civic Platform。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

