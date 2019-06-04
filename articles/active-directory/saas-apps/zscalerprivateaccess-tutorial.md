---
title: 教程：Azure Active Directory 与 Zscaler Private Access (ZPA) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Zscaler Private Access (ZPA) 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 984498a2b9d4d72ee6bb6b9f0a9e62636bf870bf
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226435"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>教程：将 Zscaler Private Access (ZPA) 与 Azure Active Directory 集成

本教程介绍如何将 Zscaler Private Access (ZPA) 与 Azure Active Directory (Azure AD) 集成。 将 Zscaler Private Access (ZPA) 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Zscaler Private Access (ZPA)。
* 让用户使用其 Azure AD 帐户自动登录到 Zscaler Private Access (ZPA)。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Zscaler Private Access (ZPA) 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 Zscaler Private Access (ZPA) 支持 SP 发起的 SSO  。

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>从库中添加 Zscaler Private Access (ZPA)

要配置 Zscaler Private Access (ZPA) 与 Azure AD 的集成，需要从库中将 Zscaler Private Access (ZPA) 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Zscaler Private Access (ZPA)”   。
1. 从结果面板中选择“Zscaler Private Access (ZPA)”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 Britta Simon 的测试用户配置和测试 Zscaler Private Access (ZPA) 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Zscaler Private Access (ZPA) 相关用户之间建立链接关系。

若要配置和测试 Zscaler Private Access (ZPA) 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
2. **[配置 Zscaler Private Access (ZPA)](#configure-zscaler-private-access-zpa)** ，以便在应用程序端配置 SSO 设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以便使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Zscaler Private Access (ZPA) 测试用户](#create-zscaler-private-access-zpa-test-user)** ，以便在 Zscaler Private Access (ZPA) 中创建 Britta Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Zscaler Private Access (ZPA)”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”页上，输入以下字段的值  ：

    1. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>` 

    1. 在“标识符(实体 ID)”文本框中，键入 URL：`https://samlsp.private.zscaler.com/auth/metadata` 

    > [!NOTE]
    > “登录 URL”  值不是实际值。 请使用实际的登录 URL 更新此值。 若要获取这些值，请联系 [Zscaler Private Access (ZPA) 客户端支持团队](https://help.zscaler.com/zpa-submit-ticket)。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

   ![证书下载链接](common/metadataxml.png)

1. 在“设置 Zscaler Private Access (ZPA)”部分，根据需要复制相应的 URL  。

   ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>配置 Zscaler Private Access (ZPA)

1. 若要在 Zscaler Private Access (ZPA) 中自动完成配置，需要单击“安装扩展”安装“我的应用安全登录浏览器扩展”。  

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Zscaler Private Access (ZPA)”转到 Zscaler Private Access (ZPA) 应用程序。  在此处，请提供用于登录到 Zscaler Private Access (ZPA) 的管理员凭据。 浏览器扩展会自动配置该应用程序，并自动执行步骤 3-6。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Zscaler Private Access (ZPA)，请打开新的 Web 浏览器窗口，以管理员身份登录到 Zscaler Private Access (ZPA) 公司站点，然后执行以下步骤：

4. 在菜单左侧单击“管理”，导航到“身份验证”部分，单击“IdP 配置”    。

    ![Zscaler Private Access Administrator 管理](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. 在右上角，单击“添加 IdP 配置”  。 

    ![Zscaler Private Access Administrator idp](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. 在“添加 IdP 配置”页面上，执行以下步骤： 
 
    ![Zscaler Private Access Administrator 选择](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. 在“IdP 元数据文件上传”字段中单击“选择文件”，上传从 Azure AD 下载的元数据文件。  

    b. 它会从 Azure AD 读取 **IdP 元数据**，并填充所有字段信息，如下所示。

    ![Zscaler Private Access Administrator 配置](./media/zscalerprivateaccess-tutorial/config.png)

    c. 从“域”字段中选择域。 
    
    d. 单击“ **保存**”。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建一个名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `Britta Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`BrittaSimon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Zscaler Private Access (ZPA) 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Zscaler Private Access (ZPA)”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“Britta Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-zscaler-private-access-zpa-test-user"></a>创建 Zscaler Private Access (ZPA) 测试用户

在本部分中，会在 Zscaler Private Access (ZPA) 中创建一个名为“Britta Simon”的用户。 请与 [Zscaler Private Access (ZPA) 支持团队](https://help.zscaler.com/zpa-submit-ticket)协作，将用户添加到 Zscaler Private Access (ZPA) 平台。

### <a name="test-sso"></a>测试 SSO

在访问面板中选择“Zscaler Private Access (ZPA)”磁贴时，应会自动登录到设置了 SSO 的 Zscaler Private Access (ZPA) 中。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)