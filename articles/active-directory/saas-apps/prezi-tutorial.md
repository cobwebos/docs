---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Prezi 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Prezi 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: jeedes
ms.openlocfilehash: 2f3e540174643f20c87396b8568f6e5b0a1ab16d
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658932"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-prezi"></a>教程：Azure Active Directory 单一登录与 Prezi 的集成

本教程介绍如何将 Prezi 与 Azure Active Directory (Azure AD) 集成。 将 Prezi 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Prezi。
* 让用户使用其 Azure AD 帐户自动登录到 Prezi。
* 在 Azure 门户中管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Prezi 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Prezi 支持 SP 和 IDP 发起的 SSO。
* Prezi 支持实时用户预配。
* 配置 Prezi 后，就可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 有关详细信息，请参阅[了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="add-prezi-from-the-gallery"></a>从库中添加 Prezi

若要配置 Prezi 与 Azure AD 的集成，需要从库中将 Prezi 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在最左侧窗格中，选择“Azure Active Directory”。
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，输入“Prezi” 。
1. 在结果面板中选择“Prezi”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-prezi"></a>配置并测试 Prezi 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 Prezi 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 用户与 Prezi 中的相关用户之间建立关联。

若要配置并测试 Prezi 的 Azure AD SSO，请完成以下构建基块：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，使用户能够使用此功能。
    1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以使用 B.Simon 用户测试 Azure AD SSO。
    1. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 B.Simon 能够使用 Azure AD SSO。
1. [配置 Prezi SSO](#configure-prezi-sso) - 在应用程序端配置 SSO 设置。
    1. [创建 Prezi 测试用户](#create-a-prezi-test-user) - 在 Prezi 中创建 B.Simon 的对应用户，并将其关联到该用户的 Azure AD 表示形式。
1. [测试 SSO](#test-sso)，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

若要在 Azure 门户中启用 Azure AD SSO，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com/)的“Prezi”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页上，选择编辑图标以编辑“基本 SAML 配置”的设置。

   ![编辑“基本 SAML 配置”设置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成。

1. 若要在 SP 发起的模式下配置应用程序，请选择“设置其他 URL”并执行以下步骤 ：

    在“登录 URL”框中，输入 URL：`https://prezi.com/login/sso/`。

1. 选择“保存”。

1. Prezi 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![用户属性和声明](common/default-attributes.png)

1. Prezi 应用程序还要求在 SAML 响应中传回其他几个属性，如此处所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 | 源属性|
    | ---------------| --------------- |
    | given_name | user.givenname |
    | family_name | user.surname |

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，找到“证书(Base64)”  。 选择“下载”以下载证书，并将其保存在计算机上。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Prezi”部分，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户最左侧的窗格中，选择“Azure Active Directory”。 转到“用户”，然后选择“所有用户” 。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”框中，输入 **B.Simon**。
   1. 在“用户名”框中，输入 `username@companydomain.extension`（例如 `B.Simon@contoso.com`）。
   1. 选中“显示密码”复选框。 记下“密码”框中显示的值。
   1. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Prezi 的权限，允许其使用 Azure SSO。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。 
1. 在应用程序列表中选择“Prezi”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。  

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”  。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择” 。
1. 在“添加分配”对话框中选择“分配”。 

## <a name="configure-prezi-sso"></a>配置 Prezi SSO

1. 在另一个 Web 浏览器窗口中，使用团队帐户登录到 Prezi，然后转到[管理控制台](https://prezi.com/organizations/manage)。

1. 在“管理控制台”中，选择“设置”选项卡 。

    ![“设置”选项卡](./media/prezi-tutorial/settings-image.png)

1. 转到“单一登录(SSO)”部分，并打开开关以启用 SSO。
    
    ![“单一登录(SSO)”开关](./media/prezi-tutorial/single-signon.png)

1. 在“单一登录(SSO)”部分执行以下步骤：

    ![“单一登录(SSO)”部分](./media/prezi-tutorial/configuration.png)

    1. 在“标识符或颁发者 URL”框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值。

    1. 在“SAML 2.0 终结点(HTTP)”框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    1. 在记事本中打开从 Azure 门户下载的“证书(Base64)”。 复制证书的内容，然后将内容粘贴到“证书(X.509)”框中。

    1. 选择“保存”。

### <a name="create-a-prezi-test-user"></a>创建 Prezi 测试用户

在本部分，我们将在 Prezi 中创建名为 Britta Simon 的用户。 Prezi 支持默认启用的实时用户预配。 此部分不存在任何操作项。 如果 Prezi 中尚不存在用户，身份验证后会创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分，我们将使用访问面板测试 Azure AD SSO 配置。

选择访问面板中的 Prezi 磁贴时，应当会自动登录到为其设置了 SSO 的 Prezi 帐户。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [在 Azure AD 中试用 Prezi](https://aad.portal.azure.com/)
- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [如何使用高级可见性和控制保护 Prezi](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

