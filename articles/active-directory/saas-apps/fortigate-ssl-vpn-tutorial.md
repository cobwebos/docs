---
title: 教程：Azure Active Directory 与 FortiGate SSL VPN 的单一登录 (SSO) 集成 | Microsoft Docs
description: 了解将 FortiGate SSL VPN 与 Azure Active Directory (Azure AD) 集成所需执行的步骤。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 187903bfbf75ada45b9a539acd1157dfe730747a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331108"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>教程：Azure Active Directory 与 FortiGate SSL VPN 的单一登录 (SSO) 集成

本教程介绍如何将 FortiGate SSL VPN 与 Azure Active Directory (Azure AD) 集成。 将 FortiGate SSL VPN 与 Azure AD 集成后，可以：

* 使用 Azure AD 控制谁有权访问 FortiGate SSL VPN。
* 让用户使用其 Azure AD 帐户自动登录到 FortiGate SSL VPN。
* 在一个中心位置（Azure 门户）管理帐户。

有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 FortiGate SSL VPN 订阅。

## <a name="tutorial-description"></a>教程说明

本教程在测试环境中配置并测试 Azure AD SSO。

FortiGate SSL VPN 支持 SP 发起的 SSO。

配置 FortiGate SSL VPN 后，可强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>从库中添加 FortiGate SSL VPN

若要配置 FortiGate SSL VPN 与 Azure AD 的集成，需要从库中将 FortiGate SSL VPN 添加到托管 SaaS 应用列表：

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左窗格中选择“Azure Active Directory”。
1. 转到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，输入“FortiGate SSL VPN” 。
1. 在结果面板中选择“FortiGate SSL VPN”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>配置并测试 FortiGate SSL VPN 的 Azure AD SSO

使用名为 B.Simon 的测试用户配置并测试 FortiGate SSL VPN 的 Azure AD SSO。 若要使 SSO 正常运行，需要在 Azure AD 用户与 FortiGate SSL VPN 中的相关用户之间建立关联。

若要配置并测试 FortiGate SSL VPN 的 Azure AD SSO，请完成以下的大致步骤：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，为用户启用该功能。
    1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以测试 Azure AD 单一登录。
    1. [向测试用户授予访问权限](#grant-access-to-the-test-user)，为该用户启用 Azure AD 单一登录。
1. 在应用程序端上[配置 FortiGate SSL VPN SSO](#configure-fortigate-ssl-vpn-sso)。
    1. 创建 FortiGate SSL VPN 测试用户，对应于该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-single-sign-on)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO：

1. 在 [Azure 门户](https://portal.azure.com/)的“FortiGate SSL VPN”应用程序集成页上的“管理”部分中，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的铅笔按钮以编辑设置： 

   ![显示用于编辑基本 SAML 配置的铅笔按钮的屏幕截图。](common/edit-urls.png)

1. 在“设置 SAML 单一登录”页面上，输入以下值：

    a. 在“登录 URL”框中，输入采用 `https://<FQDN>/remote/login` 模式的 URL。

    b. 在“标识符”框中，输入采用 `https://<FQDN>/remote/saml/metadata` 模式的 URL。

    c. 在“回复 URL”框中，输入采用 `https://<FQDN>/remote/saml/login` 模式的 URL。

    d. 在“注销 URL”框中，输入采用 `https://<FQDN>/remote/saml/logout` 模式的 URL。

    > [!NOTE]
    > 这些值只是模式。 你需要使用实际的登录 URL、标识符、回复 URL 和注销 URL   。 要获取实际值，请联系 [FortiGate SSL VPN 客户端支持团队](mailto:tac_amer@fortinet.com)。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. FortiGate SSL VPN 应用程序需要特定格式的 SAML 断言，这要求向配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![显示默认属性的屏幕截图。](common/default-attributes.png)

1. 下表显示了 FortiGate SSL VPN 所需的两个附加声明。 这些声明的名称必须与本教程的“执行 FortiGate 命令行配置”部分中使用的名称相匹配。 

   | 名称 |  源属性|
   | ------------ | --------- |
   | username | user.userprincipalname |
   | group | user.groups |
   
   若要创建这些附加声明，请执行以下操作：
   
   1. 在“用户属性和声明”旁边，选择“编辑”。
   1. 选择“添加新声明”。
   1. 对于“名称”，请输入“username”。
   1. 对于“源属性”，请选择“user.userprincipalname”。
   1. 选择“保存”  。
   1. 选择“添加组声明”。
   1. 选择“所有组”。
   1. 选中“自定义组声明的名称”复选框。
   1. 对于“名称”，请输入“group”。
   1. 选择“保存”  。   

1. 在“设置 SAML 单一登录”页面上的“SAML 签名证书”部分，选择“证书(Base64)”旁边的“下载”链接，下载证书并将其保存到计算机上   ：

    ![显示证书下载链接的屏幕截图。](common/certificatebase64.png)

1. 在“设置 FortiGate SSL VPN”部分中，根据要求复制相应的 URL：

    ![显示配置 URL 的屏幕截图。](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，你将在 Azure 门户中创建名为 B. Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中选择“Azure Active Directory”。 选择“用户”，然后选择“所有用户” 。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中，完成以下步骤：
   1. 在“名称”框中，输入 **B.Simon**。  
   1. 在“用户名”框中，输入 \<username>@\<companydomain>.\<extension>。 例如，`B.Simon@contoso.com`。
   1. 选择“显示密码”，然后记下“密码”框中显示的值。 
   1. 选择“创建”。

#### <a name="grant-access-to-the-test-user"></a>向测试用户授予访问权限

在本部分，你将授予 B.Simon 访问 FortiGate SSL VPN 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“FortiGate SSL VPN”。
1. 在应用概述页上的“管理”部分，选择“用户和组”： 

   ![显示“用户和组”选项的屏幕截图。](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”  ：

    ![显示“添加用户”按钮的屏幕截图。](common/add-assign-user.png)

1. 在“用户和组”对话框中的“用户”列表内选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。 单击屏幕底部的“选择”按钮  。
1. 在“添加分配”对话框中选择“分配”。  

#### <a name="create-a-security-group-for-the-test-user"></a>为测试用户创建安全组

在本部分中，你将在 Azure Active Directory 中为测试用户创建安全组。 FortiGate 将使用此安全组通过 VPN 来授予用户网络访问权限。

1. 在 Azure 门户的左侧窗格中选择“Azure Active Directory”。 然后选择“组”。
1. 选择屏幕顶部的“新建组”。
1. 在“新建组”属性中，完成以下步骤：
   1. 在“组类型”列表中，选择“安全性”。 
   1. 在“组名”框中，输入“FortiGateAccess” 。
   1. 在“组说明”框中，输入“授予 FortiGate VPN 访问权限的组” 。
   1. 对于“Azure AD 角色可以分配到组(预览版)”设置，请选择“否”。
   1. 在“成员身份类型”框中，选择“已分配” 。
   1. 在“成员”下，选择“未选择任何成员”。
   1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮   。
   1. 选择“创建”。
1. 返回到 Azure Active Directory 中的“组”部分后，找到“FortiGate 访问权限”组并记下“对象 ID”  。稍后需要用到此信息。

### <a name="configure-fortigate-ssl-vpn-sso"></a>配置 FortiGate SSL VPN SSO

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>将 Base64 SAML 证书上传到 FortiGate 设备

在租户中完成 FortiGate 应用的 SAML 配置后，你下载了 Base64 编码的 SAML 证书。 需要将此证书上传到 FortiGate 设备：

1. 登录到 FortiGate 设备的管理门户。
1. 在左窗格中，选择“系统”。
1. 在“系统”下，选择“证书” 。
1. 选择“导入” > “远程证书” 。
1. 浏览到从 Azure 租户中的 FortiGate 应用部署下载的证书，将其选中，然后选择“确定”。

证书上传后，请记下其在“系统” > “证书” > “远程证书”下的名称  。 默认情况下，它将命名为 REMOTE_Cert_N，其中 N 是一个整数值 。

#### <a name="complete-fortigate-command-line-configuration"></a>完成 FortiGate 命令行配置

以下步骤要求配置 Azure 注销 URL。 此 URL 包含一个问号字符 (?)。 需要执行特定步骤才能成功提交此字符。 无法通过 FortiGate CLI 控制台完成这些步骤。 而是要使用 PuTTY 等工具建立到 FortiGate 设备的 SSH 会话。 如果 FortiGate 设备是 Azure 虚拟机，则可通过 Azure 虚拟机的串行控制台完成以下步骤。

若要完成这些步骤，需要使用之前记录的值：

- 实体 ID
- 回复 URL
- 注销 URL
- Azure 登录 URL
- Azure AD 标识符
- Azure 注销 URL
- Base64 SAML 证书名称 (REMOTE_Cert_N)

1. 与 FortiGate 设备建立 SSH 会话，并使用 FortiGate 管理员帐户登录。
1. 运行以下命令：

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > Azure 注销 URL 包含 `?` 字符。 必须输入特殊键序列，才能正确地将此 URL 提供给 FortiGate 串行控制台。 URL 通常是 `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`。
   >
   > 若要在串行控制台中输入 Azure 注销 URL，请输入 `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation`。
   > 
   > 然后，选择 Ctrl+V 并粘贴 URL 的其余部分以完成该行：`set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`。

#### <a name="configure-fortigate-for-group-matching"></a>配置 FortiGate 以进行组匹配

在本部分中，你将配置 FortiGate 以识别包含测试用户的安全组的对象 ID。 此配置将使 FortiGate 能够基于组的成员身份作出访问决策。

若要完成这些步骤，需要使用之前在本教程中创建的 FortiGateAccess 安全组的对象 ID。

1. 与 FortiGate 设备建立 SSH 会话，并使用 FortiGate 管理员帐户登录。
1. 运行以下命令：

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>创建 FortiGate VPN 门户和防火墙策略

在本部分中，你将配置 FortiGate VPN 门户和防火墙策略，以授予对之前在本教程中创建的 FortiGateAccess 安全组的访问权限。

在  [FortiGate 支持团队](mailto:tac_amer@fortinet.com)的配合下，将 VPN 门户和防火墙策略添加到 FortiGate VPN 平台。 需要完成此步骤才能使用单一登录。

### <a name="test-single-sign-on"></a>测试单一登录 

在本部分，你将使用访问面板测试 Azure AD 单一登录配置。

选择访问面板中的 FortiGate SSL VPN 磁贴时，应会自动登录到为其设置了 SSO 的 FortiGate SSL VPN。 有关访问面板的详细信息，请参阅[访问面板简介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

为了获得最佳最终用户体验，Microsoft 和 FortiGate 建议使用 Fortinet VPN 客户端 FortiClient。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 FortiGate SSL VPN](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
