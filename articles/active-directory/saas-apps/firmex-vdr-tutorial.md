---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Firmex VDR 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Firmex VDR 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 670ff192-c23e-49e4-8fd1-516e02d8856c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdfb857d3a68081fda84aef33e6b5a4b4d1bce28
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2020
ms.locfileid: "76761306"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Firmex VDR 的集成

本教程介绍如何将 Firmex VDR 与 Azure Active Directory (Azure AD) 集成。 将 Firmex VDR 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Firmex VDR。
* 让用户使用其 Azure AD 帐户自动登录到 Firmex VDR。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Firmex VDR 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Firmex VDR 支持 **SP 和 IDP** 发起的 SSO

* 配置 Firmex 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-firmex-vdr-from-the-gallery"></a>从库中添加 Firmex VDR

若要配置 Firmex VDR 与 Azure AD 的集成，需要从库中将 Firmex VDR 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Firmex VDR**。 
1. 在结果面板中选择“Firmex VDR”，然后添加该应用。  在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>配置并测试 Firmex VDR 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Firmex VDR 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Firmex VDR 中的相关用户之间建立链接关系

若要配置并测试 Firmex VDR 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Firmex VDR SSO](#configure-firmex-vdr-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Firmex VDR 测试用户](#create-firmex-vdr-test-user)** - 在 Firmex VDR 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Firmex VDR”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”旁边的编辑/铅笔图标以编辑设置。  

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”部分中，用户不必执行任何步骤，因为该应用已经与 Azure 预先集成  。

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”文本框中，键入 URL：  `https://login.firmex.com`

1. 单击“ **保存**”。

1. Firmex VDR 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除上述属性以外，Firmex VDR 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ------------ | --------- |
    | 电子邮件 | user.mail |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Firmex VDR”部分，根据要求复制相应的 URL。 

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

在本部分，你将通过授予 B.Simon 访问 Firmex VDR 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“Firmex VDR”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-firmex-vdr-sso"></a>配置 Firmex VDR SSO

### <a name="before-you-get-started"></a>开始操作之前

#### <a name="what-youll-need"></a>准备工作

-   一个有效的 Firmex 订阅
-   用作 SSO 服务的 Azure AD
-   IT 管理员可以配置 SSO
-   启用 SSO 后，公司的所有用户必须使用 SSO 而不是登录名/密码登录到 Firmex。

#### <a name="how-long-will-this-take"></a>此过程需要多长时间？

实施 SSO 需要几分钟时间。 在 Firmex 支持人员为你的站点启用 SSO 之后、你公司的用户可以使用 SSO 进行身份验证之前，几乎没有任何停机时间。 只需执行以下几个步骤。

### <a name="step-1---identify-your-companys-domains"></a>步骤 1 - 识别公司的域

识别公司用户用于登录的域。

例如：

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>步骤 2 - 联系 Firmex 支持人员进行域的配置

向 [Firmex 支持团队](mailto:support@firmex.com)发送电子邮件，或拨打 1888 688 4042 x.11 来联系 Firmex 支持人员。 同时请传递你的域信息。 Firmex 支持人员会将域作为**认领的域**添加到 VDR。 现在，管理员必须配置 SSO。

警告：在站点管理员配置认领的域之前，公司用户将无法登录到 VDR。 非公司用户（即来宾用户）仍可以使用其电子邮件/密码登录。 配置过程需要几分钟时间。

### <a name="step-3---configure-the-claimed-domains"></a>步骤 3 - 配置认领的域

1. 以站点管理员身份登录到 Firmex。
1. 在左上角单击你的公司徽标。
1. 选择“SSO”选项卡。  然后选择“SSO 配置”。  单击要配置的域。

    ![认领的域](./media/firmex-vdr-tutorial/edit-sso.png)  

1. 让 IT 管理员填写以下字段。 应从标识提供者获取字段值：  

    ![SSO 配置](./media/firmex-vdr-tutorial/SSO-config.png)

    a. 在“实体 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值   。

    b. 在“标识提供者 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值   。

    c. **公钥证书** - 对于身份验证目的，SAML 消息可由颁发者进行数字签名。 若要验证消息的签名，消息接收方可以使用已知属于颁发者的公钥。 同样，若要加密消息，属于最终接收方的公共加密密钥必须是颁发者已知的密钥。 在这两种情况下（签名和加密），必须提前共享受信任的公钥。  这是**联合元数据 XML** 中的 **X509Certificate**

    d. 单击“保存”完成 SSO 配置。  更改会立即生效。

1. 此时，已为站点启用 SSO。

### <a name="create-firmex-vdr-test-user"></a>创建 Firmex VDR 测试用户

在本部分，你将在 Firmex 中创建名为 B.Simon 的用户。 在 [Firmex 支持团队](mailto:support@firmex.com)的配合下，将用户添加到 Firmex 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Firmex VDR”磁贴时，应会自动登录到设置了 SSO 的 Firmex VDR。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Firmex VDR](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Firmex](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
