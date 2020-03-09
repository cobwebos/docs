---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 IamIP Patent Platform 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 IamIP Patent Platform 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5b4fc1-e8fd-4418-a369-189272fef80d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d487aaf7ba4aaf666962cf91ca86d46115055b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190732"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iamip-patent-platform"></a>教程：Azure Active Directory 单一登录 (SSO) 与 IamIP Patent Platform 的集成

本教程介绍如何将 IamIP Patent Platform 与 Azure Active Directory (Azure AD) 集成。 将 IamIP Patent Platform 与 Azure AD 集成后，可以：

* 使用 Azure AD 控制谁有权访问 IamIP Patent Platform。
* 让用户使用其 Azure AD 帐户自动登录到 IamIP Patent Platform。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用程序与 Azure AD 集成的详细信息，请参阅[单一登录到 Azure Active Directory 中的应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用单一登录 (SSO) 的 IamIP Patent Platform 订阅。

## <a name="tutorial-description"></a>教程说明

本教程在测试环境中配置并测试 Azure AD SSO。

IamIP Patent Platform 支持 SP 和 IDP 发起的 SSO。

配置 IamIP Patent Platform 后，可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。


## <a name="add-iamip-patent-platform-from-the-gallery"></a>从库中添加 IamIP Patent Platform

若要配置 IamIP Patent Platform 与 Azure AD 的集成，需要从库中将 IamIP Patent Platform 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左窗格中选择“Azure Active Directory”  。
1. 转到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **IamIP Patent Platform**。 
1. 在结果面板中选择“IamIP Patent Platform”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-iamip-patent-platform"></a>配置并测试 IamIP Patent Platform 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 IamIP Patent Platform 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 IamIP Patent Platform 中的对应用户之间建立链接关系。

若要配置并测试 IamIP Patent Platform 的 Azure AD SSO，请执行以下高级步骤：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    * [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以测试 Azure AD 单一登录  。
    * **[向测试用户授予访问权限](#grant-access-to-the-test-user)** ，使用户能够使用 Azure AD 单一登录。

1. 在应用程序端 **[配置 IamIP Patent Platform SSO](#configure-iamip-patent-platform-sso)** 。
    * **[创建 IamIP Patent Platform 测试用户](#create-iamip-patent-platform-test-user)** ，对应于该用户的 Azure AD 表示形式。

1. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO：

1. 在 [Azure 门户](https://portal.azure.com/)中的“IamIP Patent Platform”应用程序集成页上的“管理”部分选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的铅笔按钮以编辑设置：  

   ![“基本 SAML 配置”对应的铅笔按钮](common/edit-urls.png)

1. 如果已获取服务提供商元数据文件并想要在 IDP 发起的模式下配置 SSO，请在“基本 SAML 配置”部分执行以下步骤： 

    a. 选择“上传元数据文件”： 

    ![上传元数据文件](common/upload-metadata.png)

    b. 选择文件夹按钮，选择元数据文件，然后选择“上传”： 

    ![文件夹按钮和“上传”按钮](common/browse-upload-metadata.png)

    c. 上传元数据文件后，“基本 SAML 配置”部分中的“标识符”和“回复 URL”值将自动填充：   

    ![“标识符”和“回复 URL”值](common/idp-intiated.png)

    > [!Note]
    > 如果“标识符”和“回复 URL”值未自动填充，请根据要求手动提供这些值。  

1. 若要在 SP 发起的模式下配置应用程序，请选择“设置其他 URL”并完成以下步骤： 

    在“登录 URL”框中，输入 **https:\//patents.iamip.com/login-user**。 

1. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，找到“证书(原始)”对应的“下载”链接，以下载证书并将其保存到计算机上：    

    ![证书下载链接](common/certificateraw.png)

1. 在“设置 IamIP Patent Platform”部分，根据要求复制相应的 URL： 

    ![复制配置 URL](common/idp-intiated.png))

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B. Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中选择“Azure Active Directory”。  选择“用户”，然后选择“所有用户”   。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中，完成以下步骤： 
   1. 在“名称”  框中，输入 **B.Simon**。  
   1. 在“用户名”框中，输入 \<username>@\<companydomain>.\<extension>。  例如，`B.Simon@contoso.com` 。
   1. 选择“显示密码”，然后记下“密码”框中显示的值。  
   1. 选择“创建”  。

### <a name="grant-access-to-the-test-user"></a>向测试用户授予访问权限

在本部分，你将通过授予 B.Simon 访问 IamIP Patent Platform 的权限，使该用户能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中选择“IamIP Patent Platform”。 
1. 在应用概述页上的“管理”部分，选择“用户和组”：  

   ![选择“用户和组”](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”：   

    ![选择“添加用户”](common/add-assign-user.png)

1. 在“用户和组”对话框中的“用户”列表内选择“B.Simon”，然后单击屏幕底部的“选择”按钮。    
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮   。
1. 在“添加分配”对话框中，选择“分配”按钮。  

## <a name="configure-iamip-patent-platform-sso"></a>配置 IamIP Patent Platform SSO

若要在 IamIP Patent Platform 端配置单一登录，需要将下载的原始证书以及从 Azure 门户复制的相应 URL 发送给 [IamIP Patent Platform 支持团队](mailto:info@iamip.com)。 支持人员将在两端配置正确的 SAML SSO 连接。

### <a name="create-iamip-patent-platform-test-user"></a>创建 IamIP Patent Platform 测试用户

在 [IamIP Patent Platform 支持团队](mailto:info@iamip.com)的配合下，将名为 B.Simon 的用户添加到 IamIP Patent Platform 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

在本部分，你将使用访问面板测试 Azure AD SSO 配置。

在访问面板中选择“IamIP Patent Platform”磁贴时，应会自动登录到设置了 SSO 的 IamIP Patent Platform 实例。 有关访问面板的详细信息，请参阅[访问面板简介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 IamIP Patent Platform](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
