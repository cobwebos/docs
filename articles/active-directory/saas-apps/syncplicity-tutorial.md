---
title: 教程：Azure Active Directory 与 Syncplicity 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Syncplicity 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 663958ae367162eaeb336c819d1d219dc74a2cbe
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233285"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>教程：将 Syncplicity 与 Azure Active Directory 集成

本教程介绍如何将 Syncplicity 与 Azure Active Directory (Azure AD) 集成。 将 Syncplicity 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Syncplicity。
* 让用户使用其 Azure AD 帐户自动登录到 Syncplicity。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以从[此处](https://azure.microsoft.com/pricing/free-trial/)获取一个月免费试用版。
* 已启用 Syncplicity (SSO) 单一登录的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 Syncplicity 支持 SP 发起的 SSO  。

## <a name="adding-syncplicity-from-the-gallery"></a>从库添加 Syncplicity

若要配置 Syncplicity 与 Azure AD 的集成，需要从库中将 Syncplicity 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Syncplicity”   。
1. 从结果面板中选择“Syncplicity”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso"></a>配置和测试 Azure AD SSO

使用名为 B. Simon 的测试用户配置和测试 Syncplicity 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Syncplicity 相关用户之间建立链接关系。

若要配置和测试 Syncplicity 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 Syncplicity SSO](#configure-syncplicity-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B.Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B.Simon 能够使用 Azure AD 单一登录。
5. **[创建 Syncplicity 测试用户](#create-syncplicity-test-user)** - 在 Syncplicity 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Syncplicity”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，输入以下字段的值  ：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<companyname>.syncplicity.com` 

    b. 在“标识符(实体 ID)”文本框中，使用以下模式键入 URL：`https://<companyname>.syncplicity.com/sp` 

    > [!NOTE]
    > 这些不是实际值。 使用实际登录 URL 和标识符更新这些值。 请联系 [Syncplicity 客户端支持团队](https://www.syncplicity.com/contact-us)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

   ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Syncplicity”部分中，根据要求复制相应 URL  。

   ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>配置 Syncplicity SSO

1. 登录到 **Syncplicity** 租户。

1. 在顶部菜单中，单击“管理员”  ，选择“设置”  ，并单击“自定义域和单一登录”  。

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. 在“单一登录(SSO)”  对话框页上，执行以下步骤：

    ![单一登录\(\)SSO](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. 在“自定义域”  文本框中，键入域名。
  
    b. 选择“已启用”  作为“单一登录状态”  。

    c. 在“实体 ID”  文本框中，粘贴你在 Azure 门户的“基本 SAML 配置”  中使用的“标识符(实体 ID)”  值。

    d. 在“登录页 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”   。

    e. 在“注销页 URL”文本框中，粘贴从 Azure 门户复制的“注销 URL”   。

    f. 在“标识提供者证书”中单击“选择文件”，然后上传已从 Azure 门户下载的证书。  

    g. 单击“保存更改”。 

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

在本部分中，通过授予 B.Simon 访问 Syncplicity 的权限，允许她使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Syncplicity”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-syncplicity-test-user"></a>创建 Syncplicity 测试用户

要使 Azure AD 用户能够登录，必须将这些用户预配到 Syncplicity 应用程序中。 本部分介绍如何在 Syncplicity 中创建 Azure AD 用户帐户。

**要将用户帐户预配到 Syncplicity，请执行以下步骤：**

1. 登录到 **Syncplicity** 租户（例如：`https://company.Syncplicity.com`）。

1. 单击“管理员”  ，选择“用户帐户”  ，然后单击“添加用户”。 

    ![管理用户](./media/syncplicity-tutorial/ic769764.png "管理用户")

1. 键入要预配的 Azure AD 帐户的**电子邮件地址**，选择“用户”作为“角色”，然后单击“下一步”。   

    ![帐户信息](./media/syncplicity-tutorial/ic769765.png "帐户信息")

    > [!NOTE]
    > Azure AD 帐户持有者将收到一封电子邮件，其中包含用于确认和激活帐户的链接。

1. 选择新用户应属于你的公司中的哪个组，然后单击“下一步”。 

    ![组成员身份](./media/syncplicity-tutorial/ic769772.png "组成员身份")

    > [!NOTE]
    > 如果没有列出任何组，请单击“下一步”  。

1. 在用户计算机上选择要在 Syncplicity 控件下放置的文件夹，然后单击“下一步”  。

    ![Syncplicity 文件夹](./media/syncplicity-tutorial/ic769773.png "Syncplicity 文件夹")

> [!NOTE]
> 可以使用 Syncplicity 提供的任何其他 Syncplicity 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

### <a name="test-sso"></a>测试 SSO

选择访问面板中的 Syncplicity 磁贴时，应会自动登录到为其设置了 SSO 的 Syncplicity。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)