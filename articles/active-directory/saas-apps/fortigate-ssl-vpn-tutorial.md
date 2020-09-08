---
title: 教程：Azure Active Directory 与 FortiGate SSL VPN 的单一登录 (SSO) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 FortiGate SSL VPN 之间配置单一登录。
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
ms.openlocfilehash: ecb53d661b1171f9c1b18d37d0bb35952645ba7e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299637"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>教程：Azure Active Directory 与 FortiGate SSL VPN 的单一登录 (SSO) 集成

本教程介绍如何将 FortiGate SSL VPN 与 Azure Active Directory (Azure AD) 集成。 将 FortiGate SSL VPN 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 FortiGate SSL VPN。
* 让用户使用其 Azure AD 帐户自动登录到 FortiGate SSL VPN。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 FortiGate SSL VPN 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* FortiGate SSL VPN 支持“SP”发起的 SSO
* 配置 FortiGate SSL VPN 后，可以强制实施会话控制，实时防止组织的敏感数据外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-fortigate-ssl-vpn-from-the-gallery"></a>从库中添加 FortiGate SSL VPN

若要配置 FortiGate SSL VPN 与 Azure AD 的集成，需要从库中将 FortiGate SSL VPN 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“FortiGate SSL VPN” 。
1. 从结果面板中选择“FortiGate SSL VPN”，然后添加该应用。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>配置并测试 FortiGate SSL VPN 的 Azure AD SSO

使用名为“B.Simon”的测试用户配置并测试 FortiGate SSL VPN 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 FortiGate SSL VPN 中的相关用户之间建立链接关系。

若要配置和测试 FortiGate SSL VPN 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. [配置 FortiGate SSL VPN SSO](#configure-fortigate-ssl-vpn-sso) - 在应用程序端配置单一登录设置。
    1. **创建 FortiGate SSL VPN 测试用户** - 在 FortiGate SSL VPN 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-single-sign-on)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“FortiGate SSL VPN”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“使用 SAML 设置单一登录”页上，输入以下字段的值：

    a. 在“登录 URL”文本框中，使用以下模式键入 URL：`https://<FQDN>/remote/login` 

    b. 在“标识符”文本框中，使用以下模式键入 URL：`https://<FQDN>/remote/saml/metadata`

    c. 在“回复 URL”文本框中，使用以下模式键入 URL：`https://><FQDN/remote/saml/login`

    d. 在“注销 URL”文本框中，使用以下模式键入 URL：`https://<FQDN>/remote/saml/logout`

    > [!NOTE]
    > 这些不是实际值。 请使用实际的“登录 URL”、“标识符”、“回复 URL”和“注销 URL”值更新这些值。 请联系 [FortiGate SSL VPN 客户端支持团队](mailto:tac_amer@fortinet.com)以获取这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”部分中显示的模式。

1. FortiGate SSL VPN 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，FortiGate SSL VPN 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
    
    | 名称 |  源属性|
    | ------------ | --------- |
    | username | user.userprincipalname |
    | group | user.groups |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，找到“证书(Base64)”，选择“下载”以下载该证书并将其保存到计算机上     。

    ![证书下载链接](common/certificatebase64.png)

1. 在“设置 FortiGate SSL VPN”部分中，根据要求复制相应 URL。

    ![复制配置 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”  。
1. 选择屏幕顶部的“新建用户”。
1. 在“用户”属性中执行以下步骤：
   1. 在“名称”字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension。 例如，`B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分，你将通过授予 B.Simon 访问 FortiGate SSL VPN 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“FortiGate SSL VPN”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。
1. 在“添加分配”对话框中，单击“分配”按钮。

### <a name="create-a-security-group-for-the-test-user"></a>为测试用户创建安全组

在本部分中，你将在 Azure Active Directory 中为测试用户创建安全组。 FortiGate 将使用此安全组通过 VPN 授予用户网络访问权限。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”和“组” 。
1. 选择屏幕顶部的“新建组”。
1. 在“新建组”属性中执行以下步骤：
   1. 在“组类型”字段中，选择“安全”。
   1. 在“名称”字段中，输入 `FortiGateAccess`。
   1. 在“组说明”字段中，输入 `Group for granting FortiGate VPN access`。
   1. 对于“Azure AD 角色可以分配到组(预览版)”设置，请选择“否”。
   1. 在“成员身份类型”字段中，选择“已分配”。
   1. 在“成员”下，选择“未选择任何成员”。
   1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。
   1. 选择“创建”  。
1. 返回 Azure Active Directory 中的“组”边栏选项卡后，找到“FortiGate Access”组，并记下“对象 ID”以供之后使用。

## <a name="configure-fortigate-ssl-vpn-sso"></a>配置 FortiGate SSL VPN SSO

### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>将 Base64 SAML 证书上传到 FortiGate 设备

在租户中完成 FortiGate 应用的 SAML 配置后，你下载了 Base64 编码的 SAML 证书。 必须将其上传到 FortiGate 设备：

1. 登录到 FortiGate 设备的管理门户。
1. 在左侧菜单中，单击“系统”。
1. 在“系统”下，单击“证书”。
1. 单击“导入” -> “远程证书”。
1. 浏览到从 Azure 租户中的 FortiGate 应用部署下载的证书，将其选中，然后单击“确定”

上传证书后，请记下其在“系统” > “证书” > “远程证书”下的名称。 默认情况下，它将命名为 REMOTE_Cert_N，其中 N 是一个整数值。

### <a name="perform-fortigate-command-line-configuration"></a>执行 FortiGate 命令行配置

以下步骤要求配置 Azure 注销 URL。 此 URL 包含一个问号 (?)。 需要执行特殊步骤才能成功提交此字符。 无法从 FortiGate CLI 控制台执行这些步骤。 相反，请使用 PuTTY 等工具建立到 FortiGate 设备的 SSH 会话。 如果 FortiGate 设备是 Azure 虚拟机，则可以从 Azure 虚拟机串行控制台执行以下步骤。

若要执行这些步骤，将需要之前记录的值：

- 实体 ID
- 回复 URL
- 注销 URL
- Azure 登录 URL
- Azure AD 标识符
- Azure 注销 URL
- Base64 SAML 证书名称 (REMOTE_Cert_N)

1. 与 FortiGate 设备建立 SSH 会话，并使用 FortiGate 管理员帐户登录。
1. 执行以下命令：

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
   > “Azure 注销 URL”包含 `?` 字符。 必须输入特殊密钥序列才能正确地将 URL 提供给 FortiGate 串行控制台。 该 URL 通常为 `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`。
   >
   > 若要在串行控制台中输入 Azure 注销 URL，请输入 `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation`。
   > 
   > 然后，选择 CTRL + V 并粘贴 URL 的其余部分以完成该行：`set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`。

### <a name="configure-fortigate-for-group-matching"></a>配置 FortiGate 以进行组匹配

在本部分中，你将配置 FortiGate 以识别测试用户所在的安全组的对象 ID。 这将使 FortiGate 能够基于此组的成员身份做出访问决策。

若要执行这些步骤，将需要之前创建的 FortiGateAccess 安全组的对象 ID

1. 与 FortiGate 设备建立 SSH 会话，并使用 FortiGate 管理员帐户登录。
1. 执行以下命令：

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

### <a name="create-fortigate-vpn-portals-and-firewall-policy"></a>创建 FortiGate VPN 门户和防火墙策略

在本部分中，你将配置 FortiGate VPN 门户和防火墙策略，以授予对上文创建的安全组 FortiGateAccess 的访问权限。

在  [FortiGate 支持团队](mailto:tac_amer@fortinet.com)的配合下，将 VPN 门户和防火墙策略添加到 FortiGate VPN 平台。 在使用单一登录前，必须完成这些步骤。

## <a name="test-single-sign-on"></a>测试单一登录 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 FortiGate SSL VPN 磁贴时，应当会自动登录到为其设置了 SSO 的 FortiGate SSL VPN。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

Microsoft 和 FortiGate 建议使用 Fortinet VPN 客户端 FortiClient，以获得最佳最终用户体验。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 FortiGate SSL VPN](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
