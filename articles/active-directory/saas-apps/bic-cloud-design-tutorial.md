---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 BIC Cloud Design 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 BIC Cloud Design 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68709502-480d-4f47-add2-05e0046be8ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13f6de35400280d68227af1dd7e3a981494d9e61
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77088281"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bic-cloud-design"></a>教程：Azure Active Directory 单一登录 (SSO) 与 BIC Cloud Design 的集成

本教程介绍如何将 BIC Cloud Design 与 Azure Active Directory (Azure AD) 集成。 将 BIC Cloud Design 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 BIC Cloud Design。
* 让用户使用其 Azure AD 帐户自动登录到 BIC Cloud Design。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 BIC Cloud Design 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* BIC Cloud Design 支持 **SP** 发起的 SSO
* 配置 BIC Cloud Design 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-bic-cloud-design-from-the-gallery"></a>从库中添加 BIC Cloud Design

若要配置 BIC Cloud Design 与 Azure AD 的集成，需要从库中将 BIC Cloud Design 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **BIC Cloud Design**。 
1. 在结果面板中选择“BIC Cloud Design”，然后添加该应用。  在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-bic-cloud-design"></a>配置并测试 BIC Cloud Design 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 BIC Cloud Design 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 BIC Cloud Design 中的相关用户之间建立链接关系。

若要配置并测试 BIC Cloud Design 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 BIC Cloud Design SSO](#configure-bic-cloud-design-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 BIC Cloud Design 测试用户](#create-bic-cloud-design-test-user)** - 在 BIC Cloud Design 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“BIC Cloud Design”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分，如果有**服务提供程序元数据文件**，请执行以下步骤：

    a. 单击“上传元数据文件”  。

    ![上传元数据文件](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![选择元数据文件](common/browse-upload-metadata.png)

    c. 成功上传元数据文件后，“标识符”值会自动填充在“基本 SAML 配置”部分中  。

    ![BIC Cloud Design 域和 URL 单一登录信息](common/sp-identifier.png)

    在“登录 URL”文本框中，使用以下模式键入 URL： 

    |||
    |-|-|
    | `https://<customer-specific-name/tenant>.biccloud.com`|
    | `https://<customer-specific-name/tenant>.biccloud.de` |

    > [!Note]
    > 如果“标识符”值未自动填充，请根据要求手动填充该值。  登录 URL 值不是实际值。 使用实际登录 URL 更新此值。 请联系 [BIC Cloud Design 客户端支持团队](mailto:bicsupport@gbtec.de)获取此值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. BIC Cloud Design 应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除上述属性以外，BIC Cloud Design 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性|
    | ------------ | --------- |
    | 名称 | user.name |
    | 电子邮件地址 | user.mail |
    | 名称 ID | user.userprincipalname |
    | 电子邮件 | user.mail |
    | nametest | user.displayname |

1. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

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

在本部分，你将通过授予 B.Simon 访问 BIC Cloud Design 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“BIC Cloud Design”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-bic-cloud-design-sso"></a>配置 BIC Cloud Design SSO

若要在 **BIC Cloud Design** 端配置单一登录，需要将“应用联合元数据 URL”发送给 [BIC Cloud Design 支持团队](mailto:bicsupport@gbtec.de)。  他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-bic-cloud-design-test-user"></a>创建 BIC Cloud Design 测试用户

在本部分，你将在 BIC Cloud Design 中创建名为 B.Simon 的用户。 在  [BIC Cloud Design 支持团队](mailto:bicsupport@gbtec.de)的配合下，将用户添加到 BIC Cloud Design 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“BIC Cloud Design”磁贴时，应会自动登录到设置了 SSO 的 BIC Cloud Design。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 BIC Cloud Design](https://aad.portal.azure.com/)