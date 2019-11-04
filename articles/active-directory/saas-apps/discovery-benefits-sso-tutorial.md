---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Discovery Benefits SSO 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Discovery Benefits SSO 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a788cd07-0eed-4067-b79d-19b840e8836d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64c4a6811ef5d7ed4f29c7dae89561616895a42a
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72271937"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Discovery Benefits SSO 的集成

本教程介绍如何将 Discovery Benefits SSO 与 Azure Active Directory (Azure AD) 集成。 将 Discovery Benefits SSO 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制有权访问 Discovery Benefits SSO 的人员。
* 让用户使用其 Azure AD 帐户自动登录到 Discovery Benefits SSO。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Discovery Benefits SSO 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Discovery Benefits SSO 支持 IDP 发起的 SSO 

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>从库添加 Discovery Benefits SSO

要配置 Discovery Benefits SSO 与 Azure AD 的集成，需要从库中将 Discovery Benefits SSO 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Discovery Benefits SSO”   。
1. 从结果面板中选择“Discovery Benefits SSO”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>配置和测试 Discovery Benefits SSO 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Discovery Benefits SSO 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Discovery Benefits SSO 相关用户之间建立链接关系。

若要配置和测试 Discovery Benefits SSO 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Discovery Benefits SSO SSO](#configure-discovery-benefits-sso-sso)** - 在应用程序端配置单一登录。
    1. **[创建 Discovery Benefits SSO 测试用户](#create-discovery-benefits-sso-test-user)** - 在 Discovery Benefits SSO 中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Discovery Benefits SSO”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本  **SAML** 配置”部分，应用程序已预配置为采用“ **IDP**”发起模式，并且已在 Azure 中预先填充了所需的 URL。   用户需要单击“ **保存**”按钮来保存配置。 

1. Discovery Benefits SSO 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 SAML 令牌属性配置。 以下屏幕截图显示了默认属性的列表。 单击“编辑”图标以打开“用户属性”对话框  。

    ![image](common/edit-attribute.png)

    a. 单击“编辑”图标，以打开“唯一用户标识符(名称 ID)”对话框   。

    ![Discovery Benefits SSO 配置](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![Discovery Benefits SSO 配置](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. 单击“编辑”图标，以打开“管理转换”对话框   。

    c. 在“转换”文本框中，键入为该行显示的 **ToUppercase()** 。 

    d. 在“参数 1”文本框中，键入为 `<Name Identifier value>` 之类的参数。 

    e. 单击“添加”  。

    > [!NOTE]
    > Discovery Benefits SSO 要求在“唯一用户标识符(名称 ID)”字段中传递固定的字符串值，  否则此集成无效。 Azure AD 目前不支持此功能。因此，若要解决此问题，可以使用对 NameID 的 **ToUpper** 或 **ToLower** 转换来设置固定的字符串值，如上面的屏幕截图所示。

    f. 我们已自动填充其他声明，这些声明是 SSO 配置（`SSOInstance` 和 `SSOID`）所需的。 使用“编辑”图标，按组织要求映射值。 

    ![Discovery Benefits SSO 配置](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Discovery Benefits SSO”部分，根据要求复制相应的 URL  。

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

在本部分中，通过授予 B.Simon 访问 Discovery Benefits SSO 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Discovery Benefits SSO”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-discovery-benefits-sso-sso"></a>配置 Discovery Benefits SSO SSO

若要在 Discovery Benefits SSO 端配置单一登录，需要将下载的“证书(Base64)”以及从 Azure 门户复制的相应 URL 发送给 [Discovery Benefits SSO 支持团队](mailto:Jsimpson@DiscoveryBenefits.com)   。 他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-discovery-benefits-sso-test-user"></a>创建 Discovery Benefits SSO 测试用户

本部分的内容是在 Discovery Benefits SSO 中创建名为 Britta Simon 的用户。 与  [Discovery Benefits SSO 支持团队](mailto:Jsimpson@DiscoveryBenefits.com)合作，在 Discovery Benefits SSO 平台中添加用户。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Discovery Benefits SSO”磁贴时，应会自动登录到设置了 SSO 的 Discovery Benefits SSO。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Discovery Benefits SSO](https://aad.portal.azure.com/)

