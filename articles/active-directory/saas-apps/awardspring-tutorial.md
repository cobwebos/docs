---
title: 教程：Azure Active Directory 与 AwardSpring 的单一登录 (SSO) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 AwardSpring 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f115be6-4fbe-42aa-9319-7462e7a75736
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e199b0d6cf2be7dc956267843f4dabe521d87428
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83647628"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-awardspring"></a>教程：Azure Active Directory 与 AwardSpring 的单一登录 (SSO) 集成

本教程介绍如何将 AwardSpring 与 Azure Active Directory (Azure AD) 集成。 将 AwardSpring 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 AwardSpring。
* 让用户使用其 Azure AD 帐户自动登录到 AwardSpring。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 AwardSpring 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* AwardSpring 支持 **SP 和 IDP** 发起的 SSO
* AwardSpring 支持**恰时**用户预配

## <a name="adding-awardspring-from-the-gallery"></a>从库中添加 AwardSpring

若要配置 AwardSpring 与 Azure AD 的集成，需要从库中将 AwardSpring 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中，在搜索框中键入“AwardSpring”   。
1. 从结果面板中选择“AwardSpring”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-awardspring"></a>配置并测试 AwardSpring 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 AwardSpring 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 AwardSpring 中的相关用户之间建立链接关系。

若要配置并测试 AwardSpring 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 AwardSpring SSO](#configure-awardspring-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 AwardSpring 测试用户](#create-awardspring-test-user)** - 在 AwardSpring 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **AwardSpring** 应用程序集成页面上，找到“管理”部分，选择“单一登录”   。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    a. 在“标识符”  文本框中，使用以下模式键入 URL：`https://<subdomain>.awardspring.com/SignIn/SamlMetaData`

    b. 在“回复 URL”  文本框中，使用以下模式键入 URL：`https://<subdomain>.awardspring.com/SignIn/SamlAcs`

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<subdomain>.awardspring.com/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 请联系 [AwardSpring 客户端支持团队](mailto:support@awardspring.com)获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. AwardSpring 应用程序需要特定格式的 SAML 断言，这需要你将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，AwardSpring 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    |  名称 | 源属性 |
    | ---------------| --------------- |
    | 名字 | user.givenname |
    | 姓氏 | user.surname |
    | 电子邮件 | user.mail |
    | 用户名 | user.userprincipalname |
    | StudentID | < Student ID > |

    > [!NOTE]
    > StudentID 属性映射需传回声明的实际学生 ID。 请联系 [AwardSpring 客户端支持团队](mailto:support@awardspring.com)获取此值。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 AwardSpring”部分中，根据需要复制相应的 URL  。

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

在本部分中，将通过授予 B.Simon 访问 AwardSpring 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“AwardSpring”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-awardspring-sso"></a>配置 AwardSpring SSO

若要在 **AwardSpring** 端配置单一登录，需要将下载的**联合元数据 XML** 以及从 Azure 门户复制的相应 URL 发送给 [AwardSpring 支持团队](mailto:support@awardspring.com)。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-awardspring-test-user"></a>创建 AwardSpring 测试用户

在本部分中，将在 AwardSpring 中创建一个名为 B.Simon 的用户。 AwardSpring 支持默认启用的恰时用户预配。 此部分不存在任何操作项。 如果 AwardSpring 中尚不存在用户，身份验证后会创建一个新用户。

> [!Note]
> 如需手动创建用户，请联系 [AwardSpring 支持团队](mailto:support@awardspring.com)。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 AwardSpring 磁贴时，应当会自动登录到已为其设置了 SSO 的 AwardSpring。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [尝试通过 Azure AD 使用 AwardSpring](https://aad.portal.azure.com/)

