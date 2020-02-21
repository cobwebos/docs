---
title: 无密码安全密钥登录到本地资源（预览版）-Azure Active Directory
description: 了解如何使用 Azure Active Directory （预览版）启用无密码安全密钥登录到本地资源
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e28403d905a25e9e792b3b1f31b79c39cd7728b
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522079"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>使用 Azure Active Directory （预览）启用无密码安全密钥登录到本地资源

本文档重点介绍如何为**Azure AD 联接**的环境和**混合 Azure AD 已加入**Windows 10 设备的环境启用到本地资源的无密码身份验证。 此功能使用与 Microsoft 兼容的安全密钥向本地资源提供无缝单一登录（SSO）。

|     |
| --- |
| FIDO2 安全密钥是 Azure Active Directory 的公共预览功能。 有关预览的详细信息，请参阅[Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>使用 FIDO2 密钥的 SSO 到本地资源

Azure Active Directory （AD）可以为一个或多个 Active Directory 域颁发 Kerberos 票证授予票证（Tgt）。 此功能允许用户使用新式凭据（如 FIDO2 安全密钥）登录 Windows，并访问传统的基于 Active Directory 的资源。 Kerberos 服务票证和授权将继续由本地 Active Directory 域控制器控制。

Azure AD Kerberos Server 对象在本地 Active Directory 中创建，然后安全地发布到 Azure Active Directory。 对象不与任何物理服务器关联。 它只是 Azure Active Directory 为 Active Directory 域生成 Kerberos Tgt 时可以使用的资源。

![获取票证授予票证（TGT） Azure AD 和 AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. 用户使用 FIDO2 安全密钥登录到其 Windows 10 设备，并 Azure AD 进行身份验证。
1. Azure AD 检查与用户的本地 AD 域匹配的 Kerberos 服务器密钥的目录。
   1. Azure AD 为用户的本地 AD 域生成 Kerberos TGT。 TGT 仅包括用户的 SID。 TGT 中未包含任何授权数据。
1. TGT 返回到客户端及其 Azure AD 主刷新令牌（PRT）。
1. 客户端计算机与本地 AD 域控制器联系，并为完全形成的 TGT 的部分 TGT 进行交易。
1. 客户端计算机现在具有 Azure AD PRT 和完整 Active Directory TGT，并可访问云和本地资源。

## <a name="requirements"></a>要求

在完成本文中的步骤之前，组织必须完成为[Windows 10 设备（预览版）启用无密码安全密钥签名](howto-authentication-passwordless-security-key.md)的步骤。

组织还必须满足以下软件要求。

- 设备必须运行 Windows 10 内部版本18945或更高版本。
- 您必须具有[Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)版本的1.4.32.0 或更高版本。
- Windows Server 域控制器必须安装下列修补程序：
    - 对于 Windows Server 2016- https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - 对于 Windows Server 2019- https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>支持的方案

此方案支持以下两种方案中的单一登录（SSO）：

- 适用于 Office 365 和其他启用 SAML 的应用程序的云资源。
- 对于本地资源和对网站的 Windows 集成身份验证。 这些资源可能包括需要 IIS 身份验证的网站和 SharePoint 站点，以及使用 NTLM 身份验证的资源。

### <a name="unsupported-scenarios"></a>不支持的方案

不支持以下方案：

- Windows Server Active Directory 域服务（AD DS）已加入域（仅限本地设备）部署。
- 使用安全密钥的 RDP、VDI 和 Citrix 方案。
- S/MIME 使用安全密钥。
- 使用安全密钥 "运行身份"。
- 使用安全密钥登录到服务器。

## <a name="create-kerberos-server-object"></a>创建 Kerberos 服务器对象

管理员使用 Azure AD Connect 服务器中的 PowerShell 工具在其本地目录中创建 Azure AD 的 Kerberos 服务器对象。 在组织中包含 Azure AD 用户的每个域和林中运行以下步骤：

1. 升级到最新版本的 Azure AD Connect。 说明假设已将 Azure AD Connect 配置为支持混合环境。
1. 在 Azure AD Connect 服务器上，打开提升的 PowerShell 提示符，然后导航到 `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. 运行以下 PowerShell 命令，在本地 Active Directory 域和 Azure Active Directory 租户中创建新的 Azure AD Kerberos 服务器对象。

> [!NOTE]
> 将以下示例中的 `contoso.corp.com` 替换为本地 Active Directory 域名。

```powerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>查看并验证 Azure AD Kerberos 服务器

您可以使用以下命令查看和验证新创建的 Azure AD Kerberos 服务器：

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

此命令输出 Azure AD Kerberos 服务器的属性。 您可以查看属性以验证所有内容是否都按正确的顺序进行。

| properties | 说明 |
| --- | --- |
| ID | AD DS DC 对象的唯一 ID。 此 ID 有时称为 "槽" 或它是 "分支 ID"。 |
| DomainDnsName | Active Directory 域的 DNS 域名。 |
| ComputerAccount | Azure AD Kerberos 服务器对象（DC）的计算机帐户对象。 |
| UserAccount | 已禁用的用户帐户对象，该对象保存 Azure AD Kerberos 服务器 TGT 加密密钥。 此帐户的 DN 是 `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| KeyVersion | Azure AD Kerberos 服务器 TGT 加密密钥的密钥版本。 创建密钥时，将分配该版本。 然后，每次对密钥进行旋转时，版本都会递增。 此增量是基于复制元数据的，可能大于1。 例如，初始*KeyVersion*可能为*192272*。 第一次轮换密钥时，版本可能会前进到*212621*。 要验证的重要一点是，本地对象的*KeyVersion*和云对象的*CloudKeyVersion*是相同的。 |
| KeyUpdatedOn | 更新或创建 Azure AD Kerberos 服务器 TGT 加密密钥的日期和时间。 |
| KeyUpdatedFrom | 上次更新 Azure AD Kerberos 服务器 TGT 加密密钥的 DC。 |
| CloudId | Azure AD 对象的 ID。 必须与上述 ID 匹配。 |
| CloudDomainDnsName | Azure AD 对象中的*DomainDnsName* 。 必须与上面的*DomainDnsName*匹配。 |
| CloudKeyVersion | Azure AD 对象中的*KeyVersion* 。 必须与上面的*KeyVersion*匹配。 |
| CloudKeyUpdatedOn | Azure AD 对象中的*KeyUpdatedOn* 。 必须与上面的*KeyUpdatedOn*匹配。 |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>旋转 Azure AD Kerberos 服务器密钥

Azure AD Kerberos 服务器加密 krbtgt 密钥应定期旋转。 建议遵循用于旋转所有其他 Active Directory 域控制器 krbtgt 密钥的相同计划。

> [!WARNING]
> 还可以使用其他工具轮换 krbtgt 密钥，但是，你必须使用本文档中提到的工具来轮换 Azure AD Kerberos 服务器的 krbtgt 密钥。 这可确保在本地 AD 和 Azure AD 中更新密钥。

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>删除 Azure AD Kerberos 服务器

如果要还原方案并从本地 Active Directory 和 Azure Active Directory 中删除 Azure AD Kerberos 服务器，请运行以下命令：

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>多林和多域方案

Azure AD Kerberos server 对象在 Azure AD 中表示为*KerberosDomain*对象。 每个本地 Active Directory 域在 Azure AD 中都表示为一个*KerberosDomain*对象。

例如，你的组织有一个 Active Directory 林，其中包含两个域，`contoso.com` 和 `fabrikam.com`。 如果选择允许 Azure AD 为整个林颁发 Kerberos Tgt，则 Azure AD 中有两个*KerberosDomain*对象。 一个用于 `contoso.com`的*KerberosDomain*对象，一个用于 `fabrikam.com`。 如果有多个 Active Directory 的林，则每个林中的每个域都有一个*KerberosDomain*对象。

需要在组织中包含 Azure AD 用户的每个域和林中运行步骤以[创建 Kerberos 服务器对象](#create-kerberos-server-object)。

## <a name="known-behavior"></a>已知行为

如果密码已过期，则会阻止通过 FIDO 登录。 希望用户重置其密码，然后才能使用 FIDO 登录。

## <a name="troubleshooting-and-feedback"></a>故障排除和反馈

若要在预览此功能时共享反馈或遇到问题，请使用以下步骤通过 Windows 反馈中心应用进行共享：

1. 启动**反馈中心**并确保已登录。
1. 按照以下分类提交反馈：
   - 类别：安全和隐私
   - 子类别： FIDO
1. 若要捕获日志，请使用选项**重新创建我的问题**

## <a name="frequently-asked-questions"></a>常见问题

### <a name="does-this-work-in-my-on-premises-environment"></a>这是在我的本地环境中吗？

此功能不适用于纯本地 Active Directory 域服务（AD DS）环境。

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>我的组织需要双重身份验证来访问资源。 为支持此要求，我该做什么？

安全密钥分为多种形式。 请联系感兴趣的设备制造商，讨论如何使用 PIN 或生物识别功能来启用其设备，作为第二个因素。

### <a name="can-admins-set-up-security-keys"></a>管理员可以设置安全密钥吗？

我们正在努力提供此功能的公开上市（GA）功能。

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>在哪里可以找到符合条件的安全密钥？

[FIDO2 安全密钥](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>如果丢失了安全密钥，我该怎么办？

可以通过导航到 "**安全信息**" 页并删除安全密钥，从 Azure 门户中删除密钥。

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>创建混合 Azure AD 联接的计算机后，我无法立即使用 FIDO

如果清理安装混合 Azure AD 联接的计算机，则在加入域后，你必须使用密码登录，并等待策略同步，然后才能使用 FIDO 登录。

- 通过在命令窗口中键入 `dsregcmd /status` 来检查当前状态，并检查*AzureAdJoined*和*DomainJoined*是否都显示为 *"是"* 。
- 此延迟是加入域的设备的已知限制，不是 FIDO 特定的。

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>使用 FIDO 登录并收到凭据提示后，无法获取到 NTLM 网络资源的 SSO

请确保修补了足够的域控制器来响应资源请求。 若要检查是否可以看到运行该功能的域控制器，请查看 `nltest /dsgetdc:contoso /keylist /kdc`的输出。

## <a name="next-steps"></a>后续步骤

[了解有关无密码的详细信息](concept-authentication-passwordless.md)