---
title: 无密码安全密钥登录到本地资源（预览） - Azure 活动目录
description: 了解如何使用 Azure 活动目录（预览）将无密码安全密钥登录启用到本地资源
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 181e8192170cd7394d6817edd655f4e8257b48a4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654041"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>使用 Azure 活动目录（预览）为本地资源启用无密码安全密钥登录

本文档重点介绍为**Azure AD 联接**和混合 Azure AD**加入**Windows 10 设备的环境启用无密码身份验证。 此功能使用 Microsoft 兼容的安全密钥为本地资源提供无缝的单一登录 （SSO）。

|     |
| --- |
| FIDO2 安全密钥是 Azure 活动目录的公共预览功能。 有关预览的详细信息，请参阅 Microsoft [Azure 预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>使用 FIDO2 密钥将 SSO 到本地资源

Azure 活动目录 （AD） 可以为一个或多个活动目录域颁发 Kerberos 票证授予票证 （TGT）。 此功能允许用户使用 FIDO2 安全密钥等现代凭据登录到 Windows 并访问基于活动目录的传统资源。 Kerberos 服务票证和授权继续由本地活动目录域控制器控制。

在本地活动目录中创建 Azure AD Kerberos Server 对象，然后安全地发布到 Azure 活动目录。 该对象不与任何物理服务器关联。 它只是一个资源，Azure 活动目录可以使用它为活动目录域生成 Kerberos TGT。

![从 Azure AD 和 AD DS 获取票证授予票证 （TGT）](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. 用户使用 FIDO2 安全密钥登录到其 Windows 10 设备，并验证 Azure AD。
1. Azure AD 检查目录的 Kerberos 服务器密钥与用户的本地 AD 域匹配。
   1. Azure AD 为用户的本地 AD 域生成 Kerberos TGT。 TGT 仅包括用户的 SID。 TGT 中不包含任何授权数据。
1. TGT 及其 Azure AD 主刷新令牌 （PRT） 将返回给客户端。
1. 客户端计算机与本地 AD 域控制器联系，并将部分 TGT 交易为完全成型的 TGT。
1. 客户端计算机现在具有 Azure AD PRT 和完整的活动目录 TGT，可以访问云和本地资源。

## <a name="requirements"></a>要求

在完成本文中的步骤之前，组织必须完成向[Windows 10 设备（预览）启用无密码安全密钥符号](howto-authentication-passwordless-security-key.md)的步骤。

组织还必须满足以下软件要求。

- 设备必须运行 Windows 10 内部人员版本 18945 或更新。
- 您必须具有[Azure AD 连接](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)的版本 1.4.32.0 或更高版本。
  - 有关可用的 Azure AD 混合身份验证选项的详细信息，请参阅[为 Azure Active Directory 混合标识解决方案选择正确的身份验证方法](../../security/fundamentals/choose-ad-authn.md)，并[选择用于 Azure AD 连接的安装类型](../hybrid/how-to-connect-install-select-installation.md)。
- 您的 Windows Server 域控制器必须安装以下修补程序：
    - 对于 Windows 服务器 2016 -https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - 对于 Windows 服务器 2019 -https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>支持的方案

在以下两种情况下，该方案都支持单一登录 （SSO）：

- 对于云资源，如 Office 365 和其他启用 SAML 的应用程序。
- 用于本地资源，以及对网站的 Windows 集成身份验证。 这些资源可以包括需要 IIS 身份验证的网站和 SharePoint 网站以及/或使用 NTLM 身份验证的资源。

### <a name="unsupported-scenarios"></a>不支持的方案

不支持以下方案：

- Windows 服务器活动目录域服务 （AD DS） 域已加入（仅限本地设备）部署。
- 使用安全密钥的 RDP、VDI 和 Citrix 方案。
- 使用安全密钥的 S/MIME。
- 使用安全密钥"以样运行"。
- 使用安全密钥登录到服务器。

## <a name="create-kerberos-server-object"></a>创建 Kerberos 服务器对象

管理员使用 Azure AD Connect 服务器中的 PowerShell 工具在其本地目录中创建 Azure AD Kerberos Server 对象。 在组织中包含 Azure AD 用户的每个域和林中运行以下步骤：

1. 升级到最新版本的 Azure AD 连接。 这些说明假定您已配置 Azure AD 连接以支持混合环境。
1. 在 Azure AD 连接服务器上，打开提升的 PowerShell 提示符，然后导航到`C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. 运行以下 PowerShell 命令，在本地活动目录域和 Azure 活动目录租户中创建新的 Azure AD Kerberos 服务器对象。

> [!NOTE]
> 在`contoso.corp.com`下面的示例中，将替换为本地活动目录域名。

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

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>查看和验证 Azure AD Kerberos 服务器

您可以使用以下命令查看和验证新创建的 Azure AD Kerberos 服务器：

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

此命令输出 Azure AD Kerberos 服务器的属性。 您可以查看属性以验证一切是否井然有序。

| properties | 说明 |
| --- | --- |
| ID | AD DS DC 对象的唯一 ID。 此 ID 有时称为"插槽"或"分支 ID"。 |
| DomainDnsName | 活动目录域的 DNS 域名。 |
| ComputerAccount | Azure AD Kerberos Server 对象（DC）的计算机帐户对象。 |
| UserAccount | 保存 Azure AD Kerberos 服务器 TGT 加密密钥的已禁用用户帐户对象。 此帐户的 DN 是`CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| 密钥版本 | Azure AD Kerberos 服务器 TGT 加密密钥的密钥版本。 创建密钥时分配版本。 然后，每次旋转键时都会递增版本。 增量基于复制元数据，可能大于 1。 例如，初始*密钥版本*可以是*192272*。 第一次旋转密钥时，版本可以前进到*212621*。 需要验证的重要的事情是，本地对象的*KeyVersion*和云对象的*CloudKeyVersion*是否相同。 |
| 密钥更新 | 更新或创建的 Azure AD Kerberos 服务器 TGT 加密密钥的日期和时间。 |
| 密钥从 | 上次更新 Azure AD Kerberos 服务器 TGT 加密密钥的 DC。 |
| 云 Id | 来自 Azure AD 对象的 ID。 必须与上面的 ID 匹配。 |
| 云域Dns名称 | Azure AD 对象的*域 Dns 名称*。 必须与上面的*域名*匹配。 |
| 云键版本 | 来自 Azure AD 对象的*密钥版本*。 必须与上面*的密钥版本*匹配。 |
| 云键更新 | 从 Azure AD 对象*更新的键*。 必须与上面的 *"更新密钥"匹配*。 |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>旋转 Azure AD Kerberos 服务器密钥

Azure AD Kerberos 服务器加密 krbtgt 密钥应定期轮换。 建议您遵循用于旋转所有其他活动目录域控制器 krbtgt 密钥的相同计划。

> [!WARNING]
> 还有其他工具可以旋转 krbtgt 键，但是，您必须使用本文档中提到的工具来旋转 Azure AD Kerberos Server 的 krbtgt 键。 这可确保在本地 AD 和 Azure AD 中更新密钥。

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>删除 Azure AD Kerberos 服务器

如果要还原方案并从本地活动目录和 Azure 活动目录中删除 Azure AD Kerberos 服务器，请运行以下命令：

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>多林和多域方案

Azure AD Kerberos 服务器对象在 Azure AD 中表示为*KerberosDomain*对象。 每个本地活动目录域在 Azure AD 中表示为单个*KerberosDomain*对象。

例如，您的组织具有具有两个域的活动目录林`contoso.com`和`fabrikam.com`。 如果选择允许 Azure AD 为整个林颁发 Kerberos TGT，则 Azure AD 中有两个*KerberosDomain*对象。 一个*KerberosDomain*`contoso.com`对象，一`fabrikam.com`个用于 。 如果有多个活动目录林，则每个林中的每个域都有一个*KerberosDomain*对象。

您需要运行步骤，在包含 Azure AD 用户的组织中的每个域和林中[创建 Kerberos 服务器对象](#create-kerberos-server-object)。

## <a name="known-behavior"></a>已知行为

如果您的密码已过期，则阻止使用 FIDO 登录。 期望用户在使用 FIDO 登录之前重置其密码。

## <a name="troubleshooting-and-feedback"></a>故障排除和反馈

如果您想在预览此功能时共享反馈或遇到问题，请使用以下步骤通过 Windows 反馈中心应用共享：

1. 启动**反馈中心**并确保您已登录。
1. 根据以下分类提交反馈：
   - 类别：安全和隐私
   - 子类别： FIDO
1. 要捕获日志，请使用 选项**重新创建问题**

## <a name="frequently-asked-questions"></a>常见问题

### <a name="does-this-work-in-my-on-premises-environment"></a>这在我的本地环境中工作吗？

此功能不适合纯本地活动目录域服务 （AD DS） 环境。

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>我的组织需要两个因素身份验证才能访问资源。 我可以做些什么来支持这一要求？

安全密钥有多种外形。 请与感兴趣的设备制造商联系，讨论如何使用 PIN 或生物识别作为第二个因素来启用其设备。

### <a name="can-admins-set-up-security-keys"></a>管理员可以设置安全密钥吗？

我们正在开发此功能，以便此功能的一般可用性 （GA）。

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>我在哪里可以找到符合安全密钥的密钥？

[FIDO2 安全密钥](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>如果我丢失了安全密钥，该怎么办？

您可以通过导航到**安全信息**页并删除安全密钥从 Azure 门户中删除密钥。

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>创建混合 Azure AD 联接计算机后，无法立即使用 FIDO

如果干净地安装混合 Azure AD 联接计算机，则在域加入和重新启动过程后，必须使用密码登录并等待策略同步，然后才能使用 FIDO 登录。

- `dsregcmd /status`通过键入命令窗口来检查当前状态，并检查*AzureAd并和**域加入*均显示 *"是*"。
- 此延迟是域加入设备的已知限制，不特定于 FIDO。

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>使用 FIDO 登录并获得凭据提示后，我无法将 SSO 连接到我的 NTLM 网络资源

确保修补足够的域控制器，以便及时响应以服务资源请求。 要检查是否可以看到运行该功能的域控制器，请查看 的`nltest /dsgetdc:contoso /keylist /kdc`输出。

## <a name="next-steps"></a>后续步骤

[了解有关无密码的更多](concept-authentication-passwordless.md)
