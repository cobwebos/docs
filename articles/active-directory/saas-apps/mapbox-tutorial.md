---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Mapbox 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Mapbox 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 78ae0db9-1ba2-4f54-a645-43c537719123
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e901fd3600a0a5b3efbe816b906f8598e202f454
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83882701"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mapbox"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Mapbox 的集成

本教程介绍如何将 Mapbox 与 Azure Active Directory (Azure AD) 集成。 将 Mapbox 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Mapbox。
* 让用户使用其 Azure AD 帐户自动登录到 Mapbox。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Mapbox 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Mapbox 支持 IDP 发起的 SSO
* 配置 Mapbox 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-mapbox-from-the-gallery"></a>从库中添加 Mapbox

若要配置 Mapbox 与 Azure AD 的集成，需要从库中将 Mapbox 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Mapbox” 。
1. 从结果面板中选择“Mapbox”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-mapbox"></a>配置并测试 Mapbox 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Mapbox 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 Mapbox 中的相关用户之间建立链接关系。

若要配置和测试 Mapbox 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Mapbox SSO](#configure-mapbox-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Mapbox 测试用户](#create-mapbox-test-user)** - 在 Mapbox 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Mapbox”应用程序集成页上，找到“管理”部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在基本 SAML 配置部分，应用程序进行了预配置，且已通过 Azure 预填充了必要的 URL。 用户需要单击“保存”按钮来保存配置。

1. Mapbox 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 Mapbox 令牌属性配置。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，Mapbox 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称   |  源属性|
    | -----|--------- |
    | 角色 (role) | user.assignedroles |
    | | |

    > [!NOTE]
    > 若要了解如何在 Azure AD 中配置角色，请参阅[此文](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(原始)”，选择“下载”以下载该证书并将其保存到计算机上   。

    ![证书下载链接](common/certificateraw.png)

1. 在“设置 Mapbox”部分中，根据要求复制相应的 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。 
   1. 单击“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Mapbox 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Mapbox”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。  

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。  
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 
1. 在“添加分配”对话框中，单击“分配”按钮。 

## <a name="configure-mapbox-sso"></a>配置 Mapbox SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Mapbox。

1. 单击“设置”选项卡。

    ![Mapbox 配置](./media/mapbox-tutorial/configure1.png)

1. 在左侧导航窗格中单击“安全”选项卡。

    ![Mapbox 配置](./media/mapbox-tutorial/configure2.png)

1. 单击“编辑单一登录”。

    ![Mapbox 配置](./media/mapbox-tutorial/configure3.png)

1. 向下滚动到“步骤 3：为 Mapbox 设置 SAML 单一登录”，并执行以下步骤：

    ![Mapbox 配置](./media/mapbox-tutorial/configure4.png)

    1. 在“Idp 登录 URL”文本框中，粘贴从 Azure 门户复制的“登录 URL”值 。

    1. 在“颁发者 ID”文本框中，粘贴从 Azure 门户复制的“Azure AD 标识符”值 。

    1. 在记事本中打开从 Azure 门户下载的“证书（原始）”文件，复制证书文件的内容，并将其粘贴到“X.509 证书”文本框中 。

    1. 单击“保存单一登录设置”。

### <a name="create-mapbox-test-user"></a>创建 Mapbox 测试用户

在本部分中，会在 Mapbox 中创建一个名为 Britta Simon 的用户。 在  [Mapbox 支持团队](mailto:help@mapbox.com)的配合下，将用户添加到 Mapbox 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“Mapbox”磁贴时，应会自动登录到已为其设置了 SSO 的 Mapbox。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Mapbox](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 Mapbox](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

