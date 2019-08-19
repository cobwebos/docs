---
title: 教程：Azure Active Directory 与 Carbonite Endpoint Backup 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Carbonite Endpoint Backup 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b78091f1-2f06-4c2c-8541-72aee0b5a322
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e078cb7daa787b9fe5e8bc996b36f0fef198f41c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879676"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>教程：将 Carbonite Endpoint Backup 与 Azure Active Directory 集成

本教程介绍如何将 Carbonite Endpoint Backup 与 Azure Active Directory (Azure AD) 集成。 将 Carbonite Endpoint Backup 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Carbonite Endpoint Backup。
* 让用户使用其 Azure AD 帐户自动登录到 Carbonite Endpoint Backup。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Carbonite Endpoint Backup 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Carbonite Endpoint Backup 支持 **SP 和 IDP** 发起的 SSO

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>从库中添加 Carbonite Endpoint Backup

若要配置 Carbonite Endpoint Backup 与 Azure AD 的集成，需要从库中将 Carbonite Endpoint Backup 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Carbonite Endpoint Backup**。 
1. 从结果面板中选择“Carbonite Endpoint Backup”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Carbonite Endpoint Backup 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Carbonite Endpoint Backup 中的相关用户之间建立链接关系。

若要配置并测试 Carbonite Endpoint Backup 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
2. **[配置 Carbonite Endpoint Backup SSO](#configure-carbonite-endpoint-backup-sso)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
5. **[创建 Carbonite Endpoint Backup 测试用户](#create-carbonite-endpoint-backup-test-user)** - 在 Carbonite Endpoint Backup 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
6. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Carbonite Endpoint Backup”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”文本框中，键入以下 URL 之一： 

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com`|
    | `https://red-apac.mysecuredatavault.com`|
    | `https://red-fr.mysecuredatavault.com`|
    | `https://red-emea.mysecuredatavault.com`|
    | `https://kamino.mysecuredatavault.com`|
    | | |

    b. 在“回复 URL”文本框中，键入以下 URL 之一： 

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | | |

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入以下 URL 之一： 

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/`|
    | `https://red-apac.mysecuredatavault.com/`|
    | `https://red-fr.mysecuredatavault.com/`|
    | `https://red-emea.mysecuredatavault.com/`|
    | | |

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Carbonite Endpoint Backup”部分，根据要求复制相应的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>配置 Carbonite Endpoint Backup SSO

1. 若要在 Carbonite Endpoint Backup 中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Carbonite Endpoint Backup”  会将你定向到 Carbonite Endpoint Backup 应用程序。 在此处，请提供管理员凭据以登录到 Carbonite Endpoint Backup。 浏览器扩展会自动配置应用程序，并自动执行第 3-7 步。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Carbonite Endpoint Backup，请打开新的 Web 浏览器窗口，以管理员身份登录 Carbonite Endpoint Backup 公司站点，并执行以下步骤：

4. 在左侧窗格中单击“公司”。 

    ![Carbonite Endpoint Backup 配置 ](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. 单击“单一登录”。 

    ![Carbonite Endpoint Backup 配置 ](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. 单击“启用”，然后单击“编辑设置”进行配置。  

    ![Carbonite Endpoint Backup 配置 ](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. 在“单一登录”设置页上执行以下步骤： 

    ![Carbonite Endpoint Backup 配置 ](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. 在“标识提供者名称”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。  

    1. 在“标识提供者 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    1. 单击“选择文件”，上传从 Azure 门户下载的“Certificate(Base64)”文件。  

    1. 单击“ **保存**”。

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

在本部分，你将通过授予 B.Simon 访问 Carbonite Endpoint Backup 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“Carbonite Endpoint Backup”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”    。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮    。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-carbonite-endpoint-backup-test-user"></a>创建 Carbonite Endpoint Backup 测试用户

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Carbonite Endpoint Backup 公司站点。

1. 在左窗格中单击“用户”，然后单击“添加用户”。  

    ![在 Carbonite Endpoint Backup 中添加用户](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. 在“添加用户”页上执行以下步骤： 

    ![在 Carbonite Endpoint Backup 中添加用户](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. 输入用户的“电子邮件”、“名字”和“姓氏”，并根据组织要求向用户提供所需的权限。   

    1. 单击“添加用户”  。

### <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Carbonite Endpoint Backup”磁贴时，应会自动登录到设置了 SSO 的 Carbonite Endpoint Backup。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)