---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Foodee 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Foodee 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7e28bd4a-5624-4c67-aebb-0856d97e82c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 16e7c7e676737df7e755aa4e602f8ceabd8a002f
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373186"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-foodee"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Foodee 集成

本教程介绍如何将 Foodee 与 Azure Active Directory (Azure AD) 集成。 将 Foodee 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Foodee。
* 让用户使用其 Azure AD 帐户自动登录到 Foodee。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Foodee 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Foodee 支持 **SP 和 IDP** 发起的 SSO
* Foodee 支持**实时**用户预配

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-foodee-from-the-gallery"></a>从库中添加 Foodee

若要配置 Foodee 与 Azure AD 的集成，需要将库中的 Foodee 添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Foodee”   。
1. 从结果面板中选择“Foodee”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-foodee"></a>配置并测试 Foodee 的 Azure AD 单一登录

使用名为 B. Simon 的测试用户配置和测试 Foodee 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Foodee 相关用户之间建立链接关系。

若要配置和测试 Foodee 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Foodee SSO](#configure-foodee-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Foodee 测试用户](#create-foodee-test-user)** - 在 Foodee 中创建 B.Simon 的对应用户，将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Foodee”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://concierge.food.ee/sso/saml/<INSTANCENAME>/consume` 

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://concierge.food.ee/sso/saml/<INSTANCENAME>`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“回复 URL”和“注销 URL”更新这些值。 请联系 [Foodee 客户端支持团队](mailto:dev@food.ee)，获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Foodee”部分中，根据要求复制相应 URL  。

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

在本部分中，通过授予 B.Simon 访问 Foodee 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Foodee”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="configure-foodee-sso"></a>配置 Foodee SSO

1. 若要在“Foodee”中自动执行配置，需要安装“我的应用安全登录浏览器扩展”，方法是单击“安装扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Foodee”，此时会将你定向到 Foodee 应用程序  。 在此处提供管理员凭据以登录到 Foodee。 浏览器扩展会自动配置应用程序，并自动执行步骤 3-4。

    ![设置配置](common/setup-sso.png)

3. 若要手动设置 Foodee，请打开新的 Web 浏览器窗口，以管理员身份登录 Foodee 公司站点，并执行以下步骤：

4. 在页面右上角单击**个人资料徽标**，然后导航至“单一登录”并执行以下步骤： 

   ![Foodee 配置](./media/foodee-tutorial/config01.png)

   1. 在“IDP 名称”  文本框中键入名称，例如“ex:Azure”。
   1. 在记事本中打开“联合元数据 XML”，复制其内容并将其粘贴到“IDP 元数据 XML”文本框中  。
   1. 单击“ **保存**”。

### <a name="create-foodee-test-user"></a>创建 Foodee 测试用户

本部分将在 Foodee 中创建一个名为 B.Simon 的用户。 Foodee 支持在默认情况下启用的恰时预配。 此部分不存在任何操作项。 尝试访问 Foodee 时，如果 Foodee 中尚不存在用户，则系统会创建一个新用户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Foodee”磁贴时，应会自动登录到设置了 SSO 的 Foodee。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Foodee](https://aad.portal.azure.com/)

