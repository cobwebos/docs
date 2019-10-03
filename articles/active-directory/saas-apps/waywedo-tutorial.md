---
title: 教程：Azure Active Directory 与 Way We Do 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Way We Do 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310408"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>教程：将 Way We Do 与 Azure Active Directory 集成

本教程介绍如何将 Way We Do 与 Azure Active Directory (Azure AD) 集成。 将 Way We Do 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Way We Do。
* 让用户使用其 Azure AD 帐户自动登录到 Way We Do。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月免费试用版。
* 已启用 Way We Do 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Way We Do 支持 **SP** 发起的 SSO
* Way We Do 支持**实时**用户预配

## <a name="adding-way-we-do-from-the-gallery"></a>从库添加 Way We Do

要配置 Way We Do 与 Azure AD 的集成，需要从库中将 Way We Do 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Way We Do”   。
1. 从结果面板中选择“Way We Do”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Way We Do 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Way We Do 相关用户之间建立链接关系。

若要配置和测试 Way We Do 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 Way We Do SSO](#configure-way-we-do-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Way We Do 测试用户](#create-way-we-do-test-user)** - 在 Way We Do 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Way We Do”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<SUBDOMAIN>.waywedo.com` 

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [ 客户端支持团队](mailto:support@waywedo.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(原始)”，选择“下载”以下载该证书并将其保存到计算机上     。

   ![证书下载链接](common/certificateraw.png)

1. 在“设置 Way We Do”部分中，根据要求复制相应的 URL。 

   ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>配置 Way We Do SSO

1. 若要在 Way We Do 中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

1. 将扩展添加到浏览器后，单击“安装 Way We Do”  会定向到 Way We Do 应用程序。 在此处，请提供管理员凭据以登录到 Way We Do。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

1. 若要手动设置 Way We Do，请打开新的 Web 浏览器窗口，以管理员身份登录 Way We Do 公司站点，并执行以下步骤：

1. 在 Way We Do 任意页面的右上角，单击“人员图标”，然后在下拉菜单中单击“帐户”   。

    ![Way We Do 帐户](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. 单击“菜单图标”，打开推送导航菜单，然后单击“单一登录”   。

    ![Way We Do 单一登录](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. 在“单一登录设置”页上，执行以下步骤  ：

    ![Way We Do 保存](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. 单击“打开单一登录”切换为“是”，启用单一登录   。

    b. 在“单一登录名称”文本框中，输入名称  。

    c. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    d. 在“SAML SSO URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    e. 通过单击“证书”旁的“选择”按钮上传证书   。

    f. **可选设置** -
    
    * 启用密码 - 禁用此选项时，常规密码应用于 Way We Do，这样用户只能使用单一登录。

    * 启用自动预配 - 启用此选项时，登录用的电子邮件地址将自动与 Way We Do 中的用户列表进行比较。 如果电子邮件地址与 Way We Do 中的活动用户不匹配，则会为登录人员添加新的用户帐户并请求任何缺失的信息。

      > [!NOTE]
      > 通过单一登录添加的用户被添加为普通用户，且系统不会为其分配角色。 Administrator 可作为编辑器或管理员进入并修改其安全角色，还可以分配一个或多个组织结构图角色。

    g. 单击“保存”保留设置  。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 B.Simon 访问 Way We Do 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Way We Do”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，选择“用户”列表中的“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-way-we-do-test-user"></a>创建 Way We Do 测试用户

在本部分，我们将在 Way We Do 中创建名为 Britta Simon 的用户。 Way We Do 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Way We Do 中尚不存在用户，身份验证后会创建一个新用户。

> [!Note]
> 如果需要手动创建用户，请联系 [ 客户端支持团队](mailto:support@waywedo.com)。

### <a name="test-sso"></a>测试 SSO

选择访问面板中的“Way We Do”磁贴时，应当会自动登录到设置了 SSO 的 Way We Do。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)