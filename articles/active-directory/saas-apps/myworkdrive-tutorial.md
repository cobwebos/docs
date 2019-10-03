---
title: 教程：Azure Active Directory 与 MyWorkDrive 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 MyWorkDrive 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 0c3eb72abc90347c8e18a2f56a5d4756ecd80f3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096338"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>教程：将 MyWorkDrive 与 Azure Active Directory 集成

本教程介绍如何将 MyWorkDrive 与 Azure Active Directory (Azure AD) 进行集成。 将 MyWorkDrive 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 MyWorkDrive。
* 让用户使用其 Azure AD 帐户自动登录到 MyWorkDrive。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月免费试用版。
* 启用 MyWorkDrive 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 MyWorkDrive 支持 **SP** 和 **IDP** 发起的 SSO

## <a name="adding-myworkdrive-from-the-gallery"></a>从库中添加 MyWorkDrive

要配置 MyWorkDrive 与 Azure AD 的集成，需要从库中将 MyWorkDrive 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“MyWorkDrive”   。
1. 从结果面板中选择“MyWorkDrive”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 Britta Simon 的测试用户配置和测试 MyWorkDrive 的 Azure AD SSO  。 若要正常使用 SSO，需要在 Azure AD 用户与 MyWorkDrive 相关用户之间建立关联。

若要配置和测试 MyWorkDrive 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 MyWorkDrive SSO](#configure-myworkdrive-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 MyWorkDrive 测试用户](#create-myworkdrive-test-user)** - 在 MyWorkDrive 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“MyWorkDrive”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在 IDP 发起的模式下配置应用程序，请在“基本 SAML 配置”页上输入以下字段的值   ：

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx` 

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“回复 URL”和“注销 URL”更新这些值。 输入你自己公司的 MyWorkDrive 服务器主机名，例如：
    > 
    > 回复 URL：`https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > 登录 URL：`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > 如果不确定如何设置自己的主机名和 SSL 证书，请联系 [MyWorkDrive 支持团队](mailto:support@myworkdrive.com)获取这些值。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，将“应用联合元数据 URL”复制到剪贴板    。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>配置 MyWorkDrive SSO

1. 在另一个 Web 浏览器窗口中，以安全管理员身份登录到 MyWorkDrive。

2. 在管理面板中的“MyWorkDrive 服务器”上，单击 **ENTERPRISE** 并执行以下步骤：

    ![管理](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. 启用“SAML/ADFS SSO”  。

    b. 选择“SAML - Azure AD” 

    c. 在“Azure 应用联合元数据 URL”文本框中，粘贴从 Azure 门户复制的**应用联合元数据 URL** 的值。 

    d. 单击“保存” 

    > [!NOTE]
    > 有关其他信息，请查看 [MyWorkDrive Azure AD 支持文章](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/)。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `Britta Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`BrittaSimon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过向 Britta Simon 授予对 MyWorkDrive 的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“MyWorkDrive”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“Britta Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-myworkdrive-test-user"></a>创建 MyWorkDrive 测试用户

在本部分中，将在 MyWorkDrive 中创建一个名为 Britta Simon 的用户。 与 [MyWorkDrive 支持团队](mailto:support@myworkdrive.com)合作以在 MyWorkDrive 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

### <a name="test-sso"></a>测试 SSO

选择访问面板中的 MyWorkDrive 磁贴时，应当会自动登录到为其设置了 SSO 的 MyWorkDrive。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)