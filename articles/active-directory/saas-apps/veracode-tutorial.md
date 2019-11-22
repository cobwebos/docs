---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Veracode 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Veracode 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcec326ddab1e74f43e1bb7ef446998a40799fd0
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043561"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Veracode 集成

本教程介绍如何将 Veracode 与 Azure Active Directory (Azure AD) 集成。 将 Veracode 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Veracode。
* 让用户使用其 Azure AD 帐户自动登录到 Veracode。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Veracode 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 Veracode 支持由标识提供者发起的 SSO 和实时用户预配。

## <a name="add-veracode-from-the-gallery"></a>从库中添加 Veracode

若要配置 Veracode 与 Azure AD 的集成，请从库中将 Veracode 添加到托管 SaaS 应用程序列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 转到“企业应用程序”，并选择“所有应用程序”。  
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Veracode”  。
1. 从结果面板中选择“Veracode”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>配置并测试 Veracode 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Veracode 的 Azure AD SSO  。 若要运行 SSO，必须在 Azure AD 用户与 Veracode 中相关用户之间建立链接。

若要配置和测试 Veracode 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B.Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
1. **[配置 Veracode SSO](#configure-veracode-sso)** 以在应用程序端配置单一登录设置。
    * **[创建 Veracode 测试用户](#create-veracode-test-user)** 以在 Veracode 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的 Veracode 应用程序集成页上，找到“管理”部分   。 选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的笔形图标以编辑设置   。

   ![“设置 SAML 单一登录”的屏幕截图，其中突出显示了笔形图标](common/edit-urls.png)

1. 在基本 SAML 配置  部分，应用程序进行了预配置，且已通过 Azure 预填充了必要的 URL。 选择“保存”。 

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，找到“证书(Base64)”    。 选择“下载”以下载证书，并将其保存在计算机上。 

    ![“SAML 签名证书”部分的屏幕截图，其中突出显示了“下载”链接](common/certificatebase64.png)

1. Veracode 需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![“用户属性和声明”部分的屏幕截图](common/default-attributes.png)

1. Veracode 还要求在 SAML 响应中传回其他几个属性。 这些属性也是预先填充的，但可以根据要求查看它们。

    | Name | 源属性|
    | ---------------| --------------- |
    | 名 |User.givenname |
    | 姓 |User.surname |
    | 电子邮件 |User.mail |

1. 在“设置 Veracode”部分中，根据要求复制相应的 URL  。

    ![“设置 Veracode”部分的屏幕截图，其中突出显示了配置 URL](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>配置 Veracode SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Veracode 公司站点。

1. 在顶部菜单中，选择“设置” > “管理员”   。
   
    ![“Veracode 管理”的屏幕截图，突出显示了“设置”图标和“管理员”](./media/veracode-tutorial/ic802911.png "管理")

1. 选择“SAML”选项卡  。

1. 在“组织 SAML 设置”  部分中执行以下步骤：

    ![“组织 SAML 设置”部分的屏幕截图](./media/veracode-tutorial/ic802912.png "管理")

    a.  对于“证书颁发者”，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    b. 对于“断言签名证书”，请选择“选择文件”从 Azure 门户上传已下载的证书   。

    c. 对于“自动注册”，选择“启用自动注册”   。

1. 在“自助注册设置”部分，请执行以下步骤，并选择“保存”   ：

    ![“自动注册设置”部分的屏幕截图，突出显示了各种选项](./media/veracode-tutorial/ic802913.png "管理")

    a. 对于“新用户激活”，选择“不需要激活”   。

    b. 对于“用户数据更新”，选择“首选项 Veracode 用户数据”   。

    c. 对于“SAML 属性的详细信息”  ，选择以下选项：
      * “用户角色” 
      * “策略管理员” 
      * “审阅者” 
      * “安全主管” 
      * “执行经理” 
      * “提交者” 
      * “创建者” 
      * “所有扫描类型” 
      * “团队成员身份” 
      * “默认团队” 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” >“用户” > “所有用户”。   
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：

   1. 对于“名称”  ，请输入 `B.Simon`。  
   1. 对于“用户名”  ，请输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选择“显示密码”并记下显示的值  。
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 B.Simon 访问 Veracode 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。  
1. 在应用程序列表中，选择“Veracode”  。
1. 在应用的概述页中找到“管理”部分，然后选择“用户和组”   。

   ![“管理”部分的屏幕截图，其中突出显示了“用户和组”](common/users-groups-blade.png)

1. 选择“添加用户”。  在“添加分配”对话框中选择“用户和组”。  

    ![“用户和组”页的屏幕截图，其中已突出显示“添加用户”](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”中选择 B.Simon    。 然后选择屏幕底部的“选择”  。
1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。  然后选择屏幕底部的“选择”  。
1. 在“添加分配”对话框中选择“分配”。  

### <a name="create-veracode-test-user"></a>创建 Veracode 测试用户

若要登录到 Veracode，必须将 Azure AD 用户预配为 Veracode。 这项任务是自动完成的，无需手动执行任何操作。 如有必要，在第一次尝试单一登录时会自动创建用户。

> [!NOTE]
> 可以使用 Veracode 提供的任何其他 Veracode 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

选择“访问面板”中的“Veracode”时，应当会自动登录到为其设置了 SSO 的 Veracode  。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Veracode](https://aad.portal.azure.com/)