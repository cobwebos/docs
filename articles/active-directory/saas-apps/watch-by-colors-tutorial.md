---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Watch by Colors 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Watch by Colors 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 586a029c-fb8d-4233-b280-103b9ba7102d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce0882be0419cbbbc7d94cb8d517e27bdb06a780
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013932"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Watch by Colors 集成

本教程介绍如何将 Watch by Colors 与 Azure Active Directory (Azure AD) 集成。 将 Watch by Colors 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Watch by Colors。
* 让用户使用其 Azure AD 帐户自动登录到 Watch by Colors。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Watch by Colors 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Watch by Colors 支持 **SP 和 IDP** 发起的 SSO

## <a name="adding-watch-by-colors-from-the-gallery"></a>从库中添加 Watch by Colors

若要配置 Watch by Colors 与 Azure AD 的集成，需要从库中将 Watch by Colors 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Watch by Colors”   。
1. 从结果面板中选择“Watch by Colors”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>配置和测试 Watch by Colors 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置和测试 Watch by Colors 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 Watch by Colors 相关用户之间建立链接关系。

若要配置和测试 Watch by Colors 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Watch by Colors SSO](#configure-watch-by-colors-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Watch by Colors 测试用户](#create-watch-by-colors-test-user)** - 在 Watch by Colors 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Watch by Colors”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分，应用程序已预配置为采用“IDP”发起模式，并且已在 Azure 中预先填充了所需的 URL。 ****   ****   用户需要单击“保存”按钮来保存配置。 ****  

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：`https://app.colorscorporation.com/login` 

1. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

在本部分中，将通过授予 B.Simon 访问 Watch by Colors 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Watch by Colors”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-watch-by-colors-sso"></a>配置 Watch by Colors SSO

1. 打开新的 Web 浏览器窗口，以管理员身份登录 Watch by Colors 公司站点，并执行以下步骤：

1. 在页面右上角，单击“个人资料”   > “帐户设置”   > “SSO (单一登录)”  。

    ![Watch by Colors 配置](./media/watch-by-colors-tutorial/config01.png)

1. 在“SSO (单一登录)”  页上，执行以下步骤：

    ![Watch by Colors 配置](./media/watch-by-colors-tutorial/config02.png)

    a. 将“启用 SAML”  切换到“开”  。

    b. 在“URL”文本框中，粘贴从 Azure 门户复制的**联合元数据 URL**。 

    c. 单击“导入”  ，页面上将自动填充以下字段。

    d. 单击“ **保存**”。

### <a name="create-watch-by-colors-test-user"></a>创建 Watch by Colors 测试用户

要使 Azure AD 用户能够登录 Watch by Colors，必须将这些用户预配到 Watch by Colors 中。 在 Watch by Colors 中，预配是一项手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 以安全管理员身份登录 Watch by Colors。

1. 在页面右上角，单击“个人资料”   > “用户”   > “添加用户”  。

    ![Watch by Colors 配置](./media/watch-by-colors-tutorial/config03.png)

1. 在“用户详细信息”  页上，执行以下步骤：

    ![Watch by Colors 配置](./media/watch-by-colors-tutorial/config04.png)

    a. 在“名字”文本框中，输入用户的名字，例如 B   。

    b. 在“姓氏”文本框中，输入用户的名字，如 Simon   。

    c. 在“电子邮件”文本框中，输入用户的电子邮件，如 `B.Simon@contoso.com`。 

    d. 在“密码”文本框中，输入密码  。

    e. 根据组织选择**帐户权限**。

    f. 单击“ **保存**”。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 Watch by Colors 磁贴时，应当会自动登录到设置了 SSO 的 Watch by Colors。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Slack](https://aad.portal.azure.com/)

