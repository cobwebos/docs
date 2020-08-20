---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Catchpoint 的集成
description: 了解如何在 Azure Active Directory 与 Catchpoint 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.openlocfilehash: 649396b81402e9229eb9ea2c627b60f249f8c601
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88530250"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>教程：Azure Active Directory 与 Catchpoint 的单一登录集成

本教程介绍如何将 Catchpoint 与 Azure Active Directory (Azure AD) 相集成。 将 Catchpoint 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制用户对 Catchpoint 的访问权限。
* 为具有 Azure AD 帐户的用户启用 Catchpoint 自动登录。
* 在一个中心位置（Azure 门户）管理帐户。

有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Catchpoint 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Catchpoint 支持 SP 和 IDP 发起的 SSO。
* Catchpoint 支持实时 (JIT) 用户预配。
* 配置 Catchpoint 后，可以强制实施会话控制。 此预防措施可以实时保护组织的敏感数据免于外泄和渗透。 会话控制是条件访问的扩展。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-catchpoint-from-the-gallery"></a>从库中添加 Catchpoint

若要配置 Catchpoint 与 Azure AD 的集成，请将 Catchpoint 添加到托管 SaaS 应用列表中。

1. 使用工作、学校或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧窗格中选择“Azure Active Directory”服务。 
1. 转到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入 **Catchpoint**。 
1. 在结果面板中选择“Catchpoint”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>配置并测试 Catchpoint 的 Azure AD 单一登录

若要使 SSO 正常工作，需将某个 Azure AD 用户链接到 Catchpoint 中的用户。 对于本教程，我们将配置名为 B.Simon 的测试用户。  

完成以下部分：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，为用户启用此功能。
    * [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 B.Simon 测试 Azure AD 单一登录。
    * [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 B.Simon 能够使用 Azure AD 单一登录。
1. [配置 Catchpoint SSO](#configure-catchpoint-sso)，在应用程序端配置单一登录设置。
    * [创建 Catchpoint 测试用户](#create-a-catchpoint-test-user)，允许将 B.Simon Azure AD 测试帐户链接到 Catchpoint 中的类似用户帐户。
1. [测试 SSO](#test-sso)，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

在 Azure 门户中执行以下步骤来启用 Azure AD SSO：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 在“Catchpoint”应用程序集成页上，找到“管理”部分并选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，选择笔形图标以编辑“基本 SAML 配置”。  

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 配置 Catchpoint 的发起模式：
   - 对于“IDP”发起的模式，请输入以下字段的值： 
     - **标识符**：`https://portal.catchpoint.com/SAML2`
     - **回复 URL**：`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - 对于“SP”发起的模式，请选择“设置其他 URL”并输入以下值：  
     - **登录 URL**：`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Catchpoint 应用程序需要特定格式的 SAML 断言。 请将自定义属性映射添加到 SAML 令牌属性的配置中。 下表包含默认属性的列表：

    | 名称 | 源属性|
    | ------------ | --------- |
    | Givenname | user.givenneame |
    | Surname | user.surname |
    | Emailaddress | user.mail |
    | Name | user.userprincipalname |
    | Unique User Identifier | user.userprincipalname |

    ![用户属性和声明列表的屏幕截图](common/default-attributes.png)

1. 另外，Catchpoint 应用程序要求在 SAML 响应中传入另一个属性。 请参阅下表。 系统也会预先填充此属性，但你可以检查此属性，并根据要求进行更新。

    | 名称 | 源属性|
    | ------------ | --------- |
    | namespace | user.assignedrole |

    > [!NOTE]
    > `namespace` 声明需要通过帐户名称进行映射。 应当为此帐户名称设置 Azure AD 中的、要在 SAML 响应中传回的某个角色。 有关 Azure AD 中的角色的详细信息，请参阅[为企业应用程序配置 SAML 令牌中颁发的角色声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)。

1. 转到“设置 SAML 单一登录”页。  在“SAML 签名证书”部分中，找到“证书(Base64)”。   选择“下载”以将证书保存到计算机上。 

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 Catchpoint”部分中，复制后续步骤中需要使用的 URL。 

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，你将使用 Azure 门户创建名为 B.Simon 的 Azure AD 测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。   
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，输入 `B.Simon@contoso.com`。
   1. 选中“显示密码”复选框  。 记下显示的密码值。
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，你将通过授予 B.Simon 访问 Catchpoint 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。  
1. 在“应用程序”列表中选择“Catchpoint”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从用户列表中选择“B.Simon”。   单击屏幕底部的“选择”。 
1. 如果需要在 SAML 断言中使用角色值，请查看“选择角色”对话框，并从列表中选择用户的角色。  单击屏幕底部的“选择”按钮  。
1. 在“添加分配”对话框中选择“分配”。  

## <a name="configure-catchpoint-sso"></a>配置 Catchpoint SSO

1. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Catchpoint 应用程序。

1. 依次选择“设置”图标、“SSO 标识提供者”。  

    ![Catchpoint 设置屏幕截图，其中已选择“SSO 标识提供者”](./media/catchpoint-tutorial/configuration1.png)

1. 在“单一登录”页上输入以下字段： 

   ![Catchpoint“单一登录”页的屏幕截图](./media/catchpoint-tutorial/configuration2.png)

   字段 | 值
   ----- | ----- 
   **Namespace** | 有效的命名空间值。
   **标识提供者颁发者** | Azure 门户中的 `Azure AD Identifier` 值。
   **单一登录 URL** | Azure 门户中的 `Login URL` 值。
   **证书** | 从 Azure 门户下载的 `Certificate (Base64)` 文件的内容。 请使用记事本查看并复制这些内容。

   还可以选择“上传元数据”选项来上传“联合元数据 XML”。  

1. 选择“保存”。 

### <a name="create-a-catchpoint-test-user"></a>创建 Catchpoint 测试用户

Catchpoint 支持默认已启用的实时用户预配。 本部分中没有操作项。 如果 Catchpoint 中不存在 B.Simon 用户，则在身份验证后会创建该用户。

## <a name="test-sso"></a>测试 SSO

本部分将使用“我的应用”门户测试 Azure AD 单一登录配置。

在“我的应用”门户中选择“Catchpoint”磁贴时，应会自动登录到配置了 SSO 的 Catchpoint 应用。 有关“我的应用”门户的详细信息，请参阅[从“我的应用”门户登录和启动应用](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)。

> [!NOTE]
> 通过登录页面登录到 Catchpoint 应用程序时，请在提供“Catchpoint 凭据”后，在“公司凭据(SSO)”字段中输入有效的“命名空间”值，然后选择“登录”。    
> 
> ![Catchpoint 配置](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Catchpoint](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
