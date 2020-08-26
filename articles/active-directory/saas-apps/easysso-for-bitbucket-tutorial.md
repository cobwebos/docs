---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 EasySSO for BitBucket 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 EasySSO for BitBucket 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.openlocfilehash: bd15798e9b642f3abc617c0fa224c530d90b5dd6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555552"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>教程：Azure Active Directory 单一登录 (SSO) 与 EasySSO for BitBucket 的集成

本教程介绍如何将 EasySSO for BitBucket 与 Azure Active Directory (Azure AD) 集成。 将 EasySSO for BitBucket 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 EasySSO for BitBucket。
* 让用户使用其 Azure AD 帐户自动登录到 EasySSO for BitBucket。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，你需要以下各项：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 EasySSO for BitBucket 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* EasySSO for BitBucket 支持 SP 发起的 SSO 和 IdP 发起的 SSO。
* EasySSO for BitBucket 支持“实时”用户预配。
* 配置 EasySSO for BitBucket 后，可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>从库中添加 EasySSO for BitBucket

若要配置 EasySSO for BitBucket 与 Azure AD 的集成，需要从库中将 EasySSO for BitBucket 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 转到“企业应用程序”，并选择“所有应用程序”。 
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中键入“EasySSO for BitBucket”。
1. 从结果中选择“EasySSO for BitBucket”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bitbucket"></a>配置并测试 EasySSO for BitBucket 的 Azure AD 单一登录

通过使用名为 B.Simon 的测试用户配置并测试 EasySSO for BitBucket 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 EasySSO for BitBucket 中的相关用户之间建立链接关系。

若要配置并测试 EasySSO for BitBucket 的 Azure AD SSO，请完成以下步骤：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，使用户能够使用此功能。
    1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以使用 B.Simon 测试 Azure AD 单一登录。
    1. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 B.Simon 能够使用 Azure AD 单一登录。
1. [配置 EasySSO for BitBucket SSO](#configure-easysso-for-bitbucket-sso) 以在应用程序端配置单一登录设置。
    1. [创建 EasySSO for BitBucket 测试用户](#create-an-easysso-for-bitbucket-test-user) 以在 EasySSO for BitBucket 中创建 B.Simon 的对应用户，它与该用户的 Azure AD 表示形式相关联。
1. [测试 SSO](#test-sso)，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“EasySSO for BitBucket”应用程序集成页面上，找到“管理”部分 。 选择“单一登录”。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的笔形图标以编辑设置 。

   ![“设置 SAML 单一登录”页的屏幕截图，其中突出显示了铅笔图标](common/edit-urls.png)

1. 若要在 IdP 发起模式下配置应用程序，请在“基本 SAML 配置”部分中输入以下字段的值 ：

    a. 在“标识符”文本框中，使用以下模式键入 URL：`https://<server-base-url>/plugins/servlet/easysso/saml`

    b. 在“回复 URL”文本框中，键入使用以下模式的 URL：`https://<server-base-url>/plugins/servlet/easysso/saml`

1. 若要在 SP 发起模式下配置应用程序，请选择“设置其他 URL”并执行以下步骤 ：

    - 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<server-base-url>/login.jsp`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“标识符”、“回复 URL”和“登录 URL”更新这些值。 如有疑问，请联系 [EasySSO 支持团队](mailto:support@techtime.co.nz)来获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. EasySSO for BitBucket 应用程序需要特定格式的 SAML 断言，这要求你向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![默认属性的屏幕截图](common/default-attributes.png)

1. EasySSO for BitBucket 应用程序还要求在 SAML 响应中传递回更多的属性。 下表显示了这些属性。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 | 源属性|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    如果你的 Azure AD 用户配置了 sAMAccountName，你则需要将 urn:oid:0.9.2342.19200300.100.1.1 映射到 sAMAccountName 属性  。
    
1. 在“设置 SAML 单一登录”页面上的“SAML 签名证书”部分中，选择“证书(Base64)”或“联合元数据 XML”选项的下载链接   。 将下载的任一内容或两者都保存到计算机上。 稍后将需要使用该链接来配置 BitBucket EasySSO。

    ![“SAML 签名证书”部分的屏幕截图，其中突出显示了下载链接](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    如果计划使用证书手动配置 EasySSO for BitBucket，还需要复制“登录 URL”和“Azure AD 标识符”，并将其保存在计算机上 。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，你将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。  
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 对于“名称”，请输入 `B.Simon`。  
   1. 对于“用户名”，请输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选择“显示密码”复选框，然后记下密码。
   1. 选择“创建”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 EasySSO for BitBucket 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。 
1. 在应用程序列表中，选择“EasySSO for BitBucket”。
1. 在应用的概述页中找到“管理”部分，然后选择“用户和组” 。

   ![“管理”部分的屏幕截图，其中突出显示了“用户和组”](common/users-groups-blade.png)

1. 选择“添加用户”。 在“添加分配”对话框中选择“用户和组”。 

    ![“用户和组”页的屏幕截图，其中已突出显示“添加用户”](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后选择屏幕底部的“选择”   。
1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。 然后选择屏幕底部的“选择”。
1. 在“添加分配”对话框中选择“分配”。 

## <a name="configure-easysso-for-bitbucket-sso"></a>配置 EasySSO for BitBucket SSO

1. 使用管理员权限登录到 Atlassian BitBucket 实例，并转到“管理”部分。 

    ![BitBucket 实例的屏幕截图，其中突出显示了齿轮图标](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. 找到并选择“EasySSO”。

    ![Easy SSO 选项的屏幕截图](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. 选择“SAML”。 这会使你转到 SAML 配置部分。

    ![“EasySSO 管理”页面的屏幕截图，其中突出显示了 SAML](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. 选择“证书”选项卡，你随即会看到以下屏幕：

    ![“证书”选项卡的屏幕截图，其中突出显示了各种选项](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. 找到在本教程前面部分保存的“证书(Base64)”或“元数据文件” 。 可以使用以下任一方式继续进行操作：

    - 使用下载到计算机本地文件中的应用联合“元数据文件”。 选择“上传”单选按钮，然后按照操作系统特定的路径进行操作。

    - 在任意纯文本编辑器中打开应用联合“元数据文件”以查看文件内容。 将其复制到剪贴板。 选择“输入”，并将剪贴板内容粘贴到文本字段中。
 
    - 完全进行手动配置。 在任意纯文本编辑器中打开应用联合“证书(Base64)”以查看文件内容。 将其复制到剪贴板，并粘贴到“IdP 令牌签名证书”文本字段中。 然后转到“常规”选项卡，在“POST 绑定 URL”和“实体 ID”字段中分别填入之前保存的“登录 URL”和“Azure AD 标识符”的值    。
 
1. 选择页面底部的“保存”。 你将看到元数据或证书文件的内容被分析为配置字段。 EasySSO for BitBucket 配置已完成。

1. 若要测试配置，请转到“外观和感受”选项卡，然后选择“SAML 登录按钮” 。 这会在 BitBucket 登录屏幕上启用一个单独的按钮，专门用于测试端到端 Azure AD SAML 集成。 对于生产模式，你也可以使此按钮保留为启用状态，并配置其位置、颜色和转换。

    ![SAML 页面“外观和感受”选项卡的屏幕截图，其中突出显示了“SAML 登录按钮”](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >若有任何问题，请联系 [EasySSO 支持团队](mailto:support@techtime.co.nz)。

### <a name="create-an-easysso-for-bitbucket-test-user"></a>创建一个 EasySSO for BitBucket 测试用户

在本部分中，你将在 BitBucket 中创建一个名为 Britta Simon 的用户。 EasySSO for BitBucket 支持默认禁用的实时用户预配。 若要启用它，必须在 EasySSO 插件配置的“常规”部分中显式选中“成功登录后创建用户” 。 如果 BitBucket 中不存在用户，则会在身份验证后创建一个新用户。

但是，如果不想在用户首次登录时启用自动用户预配，用户则必须存在于 BitBucket 实例使用的用户目录中。 例如，此目录可能是 LDAP 或 Atlassian Crowd。

![EasySSO 插件配置的“常规”部分的屏幕截图，其中突出显示了“成功登录后创建用户”](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>测试 SSO 

### <a name="idp-initiated-workflow"></a>IDP 发起的工作流

在本部分，你将使用访问面板测试 Azure AD 单一登录配置。

选择 EasySSO for BitBucket 磁贴时，你应该会自动登录到为其设置了 SSO 的 BitBucket 实例。 有关详细信息，请参阅[从“我的应用”门户登录和启动应用](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

### <a name="sp-initiated-workflow"></a>SP 发起的工作流

在本部分中，你将使用 BitBucket 的“SAML 登录”按钮测试 Azure AD 单一登录配置。

![“登录”屏幕的屏幕截图，其中突出显示了“SAML 登录”](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-7.png)

此方案假定已在 BitBucket EasySSO 配置页面的“外观和感受”选项卡中启用了“SAML 登录按钮” 。 在浏览器隐身模式下打开 BitBucket 登录 URL，避免对现有会话造成任何干扰。 选择“SAML 登录”后，你将被重定向到 Azure AD 用户身份验证流。 此过程成功完成后，系统会将你作为通过 SAML 进行了身份验证的用户重定向回 BitBucket 实例。

在从 Azure AD 重定向回实例后，你可能会看到以下屏幕：

![EasySSO 故障屏幕的屏幕截图，其中突出显示了“参考编号”](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-8.png)

如果遇到了此故障，请按照[此页面上的说明](https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS)访问 atlassian-bitbucket.log 文件。 错误的详细信息将通过 EasySSO 错误页面上的参考 ID 提供。

若有任何问题，请联系 [EasySSO 支持团队](mailto:support@techtime.co.nz)。

## <a name="additional-resources"></a>其他资源

- [用于将 SaaS 应用程序与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 EasySSO for BitBucket](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [通过高级可见性和控制保护 EasySSO for BitBucket](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
