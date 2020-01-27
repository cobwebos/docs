---
title: 教程：Azure Active Directory 与 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）的单一登录 (SSO) 集成 | Microsoft Docs
description: 了解如何配置 Azure Active Directory 与 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）的单一登录 (SSO) 集成。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f4311031-5a4b-468e-be58-324d06220869
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74b56e30eb8851913f91cbee377204a575311375
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293668"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>教程：Azure Active Directory 与 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）的单一登录 (SSO) 集成

在本教程中，你将了解如何将 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）与 Azure Active Directory (Azure AD) 进行集成。 将 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）与 Azure AD 集成后，你可以：

* 在 Azure AD 中控制谁有权访问 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）。
* 让用户使用其 Azure AD 帐户自动登录到 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* SSOGEN - 用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关支持 **SP 和 IDP** 发起的 SSO。
* 配置 SSOGEN - 用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>从库中添加 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）

若要配置 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）与 Azure AD 的集成，需要从库中将 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”  部分中，在搜索框中键入“从库中添加 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）”  。
1. 从结果面板中添加“SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）”  ，然后添加应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>配置并测试 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）中的相关用户之间建立链接关系。

若要配置并测试 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）的 Azure AD SSO，需要完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）SSO 的 SSOGEN Azure AD SSO 网关](#configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso)** - 在应用程序端配置单一登录设置。
    * **[创建“用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关”测试用户](#create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user)** - 在 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）中创建 B.Simon 的对应用户，并将其链接到用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）** 应用程序集成页上，找到“管理”  部分并选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值   ：

    在“回复 URL”文本框中，使用以下模式键入 URL：`https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>` 

1. 如果要在 SP  发起的模式下配置应用程序，请单击“设置其他 URL”  ，并执行以下步骤：

    在“登录 URL”  文本框中，使用以下模式键入 URL：`https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“回复 URL”和“注销 URL”更新这些值。 联系 [SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）客户端支持团队](mailto:support@ssogen.com)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）应用程序需要特定格式的 SAML 断言，因此，需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表，其中的 **nameidentifier** 通过 **user.userprincipalname** 进行映射。 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）应用程序要求通过 **user.onpremisessamaccountname** 来映射 **nameidentifier**，因此你需要单击“编辑”  图标并更改属性映射来编辑属性映射。

    ![image](common/edit-attribute.png)

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

在本部分中，通过向 B.Simon 授予对 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）的访问权限使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-sso"></a>配置用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）SSO 的 SSOGEN Azure AD SSO 网关

若要在 **SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）** 端配置单一登录，请找到下方特定于应用程序的 SSO 注册文档：

* Oracle EBS - Azure AD SSO 集成：[https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft - Azure AD SSO 集成：[https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards - Azure AD SSO 集成：[https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Apache - Azure AD SSO 集成：[https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen-azure-ad-sso-gateway-for-oracle-e-business-suite-ebs-peoplesoft-and-jde-test-user"></a>创建“用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 SSOGEN Azure AD SSO 网关”测试用户

身份验证成功后，Azure AD 向用户应用程序发送唯一的用户标识符（名称 ID）。  请确保唯一用户标识符（名称 ID）与你的应用程序中的用户记录相匹配，例如 Oracle EBS 中的 FND_USER.USER_NAME。

若需支持，请联系 [info@ssogen.com](mailto:info@ssogen.com) 和 [support@ssogen.com](mailto:support@ssogen.com)。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的“SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）”磁贴时，应当会自动登录到已为其设置了 SSO 的 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 SSOGEN（用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关）](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 SSOGEN - 用于 Oracle 电子商务套件（EBS、PeopleSoft 和 JDE）的 Azure AD SSO 网关](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
