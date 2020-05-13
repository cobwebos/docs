---
title: 使用 NPS Azure Active Directory 提供 Azure MFA 功能
description: 向现有身份验证基础结构添加基于云的双重验证功能
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 9dce9e2f63afc50e367d650f93f293b974d912e9
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83199554"
---
# <a name="integrate-your-existing-nps-infrastructure-with-azure-multi-factor-authentication"></a>将现有 NPS 基础结构与 Azure 多重身份验证集成

适用于 Azure MFA 的网络策略服务器 (NPS) 扩展可以使用现有的服务器将基于云的 MFA 功能添加到身份验证基础结构。 使用 NPS 扩展，可将电话呼叫、短信或电话应用验证添加到现有的身份验证流，而无需安装、配置和维护新服务器。 

此扩展是为想要保护 VPN 连接，但不部署 Azure MFA 服务器的组织创建的。 NPS 扩展充当 RADIUS 和基于云的 Azure MFA 之间的适配器，以为联合用户或已同步用户提供身份验证的第二个因素。

使用适用于 Azure MFA 的 NPS 扩展时，身份验证流包括以下组件： 

1. **NAS/VPN 服务器**接收来自 VPN 客户端的请求，并将其转换为可发往 NPS 服务器的 RADIUS 请求。 
2. **NPS 服务器**连接到 Active Directory，针对 RADIUS 请求执行主要身份验证，成功后，将请求传递到所有已安装的扩展。  
3. **NPS 扩展**向 Azure MFA 触发一个执行辅助身份验证的请求。 该扩展收到响应后，如果 MFA 质询成功，该扩展将通过向 NPS 服务器提供由 Azure STS 颁发的、包含 MFA 声明的安全令牌，来完成身份验证请求。  
4. **AZURE MFA**与 Azure Active Directory 通信以检索用户的详细信息，并使用配置给用户的验证方法执行辅助身份验证。

下图显示了此高级身份验证请求流： 

![身份验证流示意图](./media/howto-mfa-nps-extension/auth-flow.png)

## <a name="plan-your-deployment"></a>规划部署

NPS 扩展自动处理冗余，因此无需特殊配置。

可以根据需要创建任意数量的已启用 Azure MFA 的 NPS 服务器。 如果确实安装了多个服务器，应该为其中每个服务器使用不同的客户端证书。 为每个服务器创建证书意味着，可单独更新每个证书，不必担心所有服务器出现停机。

VPN 服务器会路由身份验证请求，因此它们需要留意已启用新 Azure MFA 的 NPS 服务器。

## <a name="prerequisites"></a>必备条件

NPS 扩展需与现有基础结构配合工作。 在开始之前，请确保满足以下先决条件。

### <a name="licenses"></a>许可证

适用于 Azure MFA 的 NPS 扩展向持有 [Azure 多重身份验证许可证](multi-factor-authentication.md)（Azure AD Premium、EMS 或 MFA 独立证书已随附）的客户提供。 Azure MFA 的基于使用量的许可证（例如，按用户或按身份验证许可证）与 NPS 扩展不兼容。 

### <a name="software"></a>软件

Windows Server 2008 R2 SP1 或更高版本。

### <a name="libraries"></a>库

这些库会自动随扩展一同安装。

- [Visual C++ Redistributable Packages for Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块版本 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

已安装用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块，如果尚未安装，可以通过配置脚本，作为安装过程的一部分运行。 如果尚未安装此模块，则无需提前安装。

### <a name="azure-active-directory"></a>Azure Active Directory

使用 NPS 扩展的任何用户必须使用 Azure AD Connect 同步到 Azure Active Directory，并且必须注册 MFA。

安装该扩展时，需要使用 Azure AD 租户的目录 ID 和管理员凭据。 可在 [Azure 门户](https://portal.azure.com)中找到该目录 ID。 以管理员身份登录。 搜索并选择 " **Azure Active Directory**"，然后选择 "**属性**"。 复制“目录 ID”**** 框中的 GUID 并保存。 安装 NPS 扩展时，使用此 GUID 作为租户 ID。

![在 Azure Active Directory 属性下找到目录 ID](./media/howto-mfa-nps-extension/properties-directory-id.png)

### <a name="network-requirements"></a>网络要求

NPS 服务器必须能够通过端口 80 和 443 与以下 URL 通信。

- https： \/ /adnotifications.windowsazure.com
- https:\//login.microsoftonline.com
- https： \/ /credentials.azure.com

此外，还需要连接到以下 Url，才能[使用提供的 PowerShell 脚本完成适配器的设置](#run-the-powershell-script)

- https:\//login.microsoftonline.com
- https： \/ /provisioningapi.microsoftonline.com
- https： \/ /aadcdn.msauth.net

## <a name="prepare-your-environment"></a>准备环境

在安装 NPS 扩展之前，需要准备环境来处理身份验证流量。

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>在已加入域的服务器上启用 NPS 角色

NPS 服务器会连接到 Azure Active Directory，并对 MFA 请求进行身份验证。 为此角色选择一台服务器。 我们建议选择一台不处理来自其他服务的请求的服务器，因为对于不是 RADIUS 的任何请求，NPS 扩展都会引发错误。 NPS 服务器必须设置为环境的主要和次要身份验证服务器，它不能将 RADIUS 请求代理到另一台服务器。

1. 在服务器上，从“服务器管理器快速入门”菜单中打开“添加角色和功能向导”****。
2. 为安装类型选择“基于角色或基于功能的安装”****。
3. 选择“网络策略和访问服务”**** 服务器角色。 可能会弹出窗口，以通知运行此角色所需的功能。
4. 继续完成该向导，直到出现“确认”页。 选择“安装”  。

既然已为 NPS 指定了服务器，还应配置此服务器以处理来自 VPN 解决方案的传入 RADIUS 请求。

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>将 VPN 解决方案配置为与 NPS 服务器通信

根据使用的 VPN 解决方案，配置 RADIUS 身份验证策略的步骤会有所不同。 将此策略配置为指向 RADIUS NPS 服务器。

### <a name="sync-domain-users-to-the-cloud"></a>将域用户同步到云

此步骤可能已在租户上完成，但最好仔细检查 Azure AD Connect 最近是否已同步数据库。

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 选择**Azure Active Directory**  >  **Azure AD Connect**
3. 确认同步状态是“已启用”****，并确认上次同步时间是在不到一小时前。

如果需要启动新一轮的同步，可使用 [Azure AD Connect 同步：计划程序](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler)中的说明。

### <a name="determine-which-authentication-methods-your-users-can-use"></a>决定你的用户可以使用哪些身份验证方法

有两个因素会影响 NPS 扩展部署可以使用哪些身份验证方法：

1. 在 RADIUS 客户端（VPN、Netscaler 服务器或其他客户端）与 NPS 服务器之间使用的密码加密算法。
   - **PAP**在云中支持 Azure MFA 的所有身份验证方法：电话呼叫、单向短信、移动应用通知、OATH 硬件令牌和移动应用验证码。
   - **CHAPV2**和**EAP**支持电话呼叫和移动应用通知。

      > [!NOTE]
      > 部署 NPS 扩展时，请根据这些因素评估你的用户可使用哪些方法。 如果你的 RADIUS 客户端支持 PAP，但客户端 UX 没有可用于输入验证码的输入字段，则电话呼叫和移动应用通知是两种受支持的选项。
      >
      > 此外，如果你的 VPN 客户端 UX 支持输入字段，并且你配置了网络访问策略，则身份验证可能成功，但是，在网络策略中配置的 RADIUS 属性都不会同时应用于网络访问设备，如 RRAS 服务器和 VPN 客户端。 因此，VPN 客户端的访问权限可能比所需的访问权限更多或更少。
      >

2. 客户端应用程序（VPN、Netscaler 服务器或其他客户端）可以处理的输入方法。 例如，VPN 客户端是否有一些手段允许用户键入通过文本或移动应用收到的验证码？

可以在 Azure 中[禁用不受支持的身份验证方法](howto-mfa-mfasettings.md#verification-methods)。

### <a name="register-users-for-mfa"></a>用户注册 MFA

部署和使用 NPS 扩展前，需要先向 MFA 注册必须执行双重验证的用户。 更直接的是，若要在部署扩展时测试扩展，则至少需要一个已针对多重身份验证进行完全注册的测试帐户。

使用以下步骤以启动一个测试帐户：

1. [https://aka.ms/mfasetup](https://aka.ms/mfasetup)使用测试帐户登录到。
2. 按照提示设置验证方法。
3. [创建条件性访问策略](howto-mfa-getstarted.md#create-conditional-access-policy)，要求对测试帐户进行多重身份验证。

> [!IMPORTANT]
>
> 请确保用户已成功注册 Azure 多重身份验证。 如果用户以前仅注册了自助服务密码重置（SSPR），则为其帐户启用*StrongAuthenticationMethods* 。 即使用户只注册了 SSPR，也会在配置*StrongAuthenticationMethods*时强制执行 Azure 多重身份验证。
>
> 可以启用组合的安全注册，同时配置 SSPR 和 Azure 多重身份验证。 有关详细信息，请参阅[在 Azure Active Directory 中启用组合的安全信息注册](howto-registration-mfa-sspr-combined.md)。
>
> 如果以前仅启用了 SSPR，则还可以[强制用户重新注册身份验证方法](howto-mfa-userdevicesettings.md#manage-user-authentication-options)。

## <a name="install-the-nps-extension"></a>安装 NPS 扩展

> [!IMPORTANT]
> 在不同于 VPN 接入点的服务器上安装 NPS 扩展。

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>针对 Azure MFA 下载并安装 NPS 扩展

1. 从 Microsoft 下载中心[下载 NPS 扩展](https://aka.ms/npsmfa)。
2. 将二进制文件复制到要配置的网络策略服务器。
3. 运行*setup.exe*并按照安装说明进行操作。 如果发生错误，请仔细检查先决条件部分的两个库是否已成功安装。

#### <a name="upgrade-the-nps-extension"></a>升级 NPS 扩展

升级现有 NPS 扩展安装时，若要避免重新启动基础服务器，请完成以下步骤：

1. 卸载现有版本
1. 运行新安装程序
1. 重新启动网络策略服务器（IAS）服务

### <a name="run-the-powershell-script"></a>运行 PowerShell 脚本

安装程序会在以下位置创建 PowerShell 脚本：`C:\Program Files\Microsoft\AzureMfa\Config`（其中，C:\ 是安装驱动器）。 此 PowerShell 脚本在每次运行时执行以下操作：

- 创建自签名证书。
- 将证书的公钥关联到 Azure AD 上的服务主体。
- 将证书存储在本地计算机证书存储中。
- 向网络用户授予对证书私钥的访问权限。
- 重新启动 NPS。

除非你想要使用自己的证书（而不是 PowerShell 脚本生成的自签名证书），否则请运行该 PowerShell 脚本来完成安装。 如果在多台服务器上安装扩展，则每个服务器都应有自己的证书。

1. 以管理员身份运行 Windows PowerShell。
2. 更改目录。

   `cd "C:\Program Files\Microsoft\AzureMfa\Config"`

3. 运行安装程序创建的 PowerShell 脚本。

   `.\AzureMfaNpsExtnConfigSetup.ps1`

4. 以管理员身份登录到 Azure AD。
5. PowerShell 会提示输入租户 ID。 使用在先决条件部分中从 Azure 门户复制的目录 ID GUID。
6. 脚本完成后，PowerShell 会显示一条成功消息。  

在要针对负载均衡设置的任何其他 NPS 服务器上重复这些步骤。

如果以前的计算机证书已过期，并且已生成新证书，则应删除任何过期的证书。 如果证书过期，则可能会导致 NPS 扩展开始出现问题。

> [!NOTE]
> 如果使用自己的证书，而不是使用 PowerShell 脚本生成的证书，请确保它们符合 NPS 命名约定。 使用者名称必须为“CN=\<TenantID\>”，“OU=Microsoft NPS Extension”****。 

### <a name="microsoft-azure-government-additional-steps"></a>Microsoft Azure 政府其他步骤

对于使用 Azure 政府云的客户，需要在每个 NPS 服务器上执行以下附加配置步骤：

1. 在 NPS 服务器上打开**注册表编辑器**。
1. 导航到 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`。 设置以下键值：

    | 注册表项       | 值 |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. 重复上述两个步骤，为每个 NPS 服务器设置注册表项值。
1. 为每个 NPS 服务器重启 NPS 服务。

    为了最大限度地减少影响，每台 NPS 服务器一次一个地从 NLB 旋转，并等待所有连接耗尽。

### <a name="certificate-rollover"></a>证书滚动更新

对于 NPS 扩展的 release 1.0.1.32，现在支持读取多个证书。 此功能有助于在证书更新过期之前方便滚动。 如果你的组织运行的是早期版本的 NPS 扩展，则应升级到版本1.0.1.32 或更高版本。

脚本创建的证书 `AzureMfaNpsExtnConfigSetup.ps1` 有效期为2年。 IT 组织应监视证书的过期时间。 NPS 扩展的证书放置在 "个人" 下的 "本地计算机" 证书存储中，并颁发给脚本提供的租户 ID。

当证书接近到期日期时，应创建新的证书来替换证书。  此过程通过再次运行并在 `AzureMfaNpsExtnConfigSetup.ps1` 出现提示时保留相同的租户 ID 来完成。 应在环境中的每个 NPS 服务器上重复此过程。

## <a name="configure-your-nps-extension"></a>配置 NPS 扩展

本部分包含一些设计注意事项和建议，帮助用户成功完成 NPS 扩展的部署。

### <a name="configuration-limitations"></a>配置限制

- Azure MFA 的 NPS 扩展不包含用于将用户和设置从 MFA 服务器迁移到云的工具。 出于此原因，我们建议将扩展用于新部署，而非现有部署。 如果在现有部署上使用扩展，用户必须重新进行证明才能在云中填充其 MFA 详细信息。  
- NPS 扩展使用本地 Active directory 中的 UPN 来识别 Azure MFA 上用于执行辅助身份验证的用户。可以将该扩展配置为使用其他标识符，例如备用登录 ID 或 UPN 以外的自定义 Active Directory 字段。 有关详细信息，请参阅[用于多重身份验证的 NPS 扩展的高级配置选项](howto-mfa-nps-extension-advanced.md)一文。
- 并非所有加密协议都支持所有验证方法。
   - PAP 支持电话呼叫、单向短信、移动应用通知和移动应用验证码****
   - **CHAPV2** 和 **EAP** 支持电话呼叫和移动应用通知

### <a name="control-radius-clients-that-require-mfa"></a>控制需要 MFA 的 RADIUS 客户端

使用 NPS 扩展为 RADIUS 客户端启用 MFA 后，此客户端的所有身份验证都需要执行 MFA。 如果想要为一部分（而不是全部）RADIUS 客户端启用 MFA，可以配置两台 NPS 服务器，并只在其中一台服务器上安装该扩展。 将需要执行 MFA 的 RADIUS 客户端配置为向配置了该扩展的 NPS 服务器发送请求，将其他 RADIUS 客户端配置为向未配置该扩展的 NPS 服务器发送请求。

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>为未注册 MFA 的用户做好准备

如果你的某些用户未注册 MFA，你可以确定当他们尝试身份验证时要发生什么行为。 使用注册表路径 *HKLM\Software\Microsoft\AzureMFA* 中的注册表设置 *REQUIRE_USER_MATCH* 可以控制功能行为。 此项设置提供单个配置选项：

| 键 | 值 | 默认值 |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | 未设置（相当于 TRUE） |

此项设置的目的是确定当某个用户未注册 MFA 时要执行哪个操作。 如果该键不存在、未设置或设置为 TRUE，并且用户未注册，则该扩展将通不过 MFA 质询。 如果该键设置为 FALSE 并且用户未注册，身份验证会继续且不执行 MFA。 如果用户在 MFA 中注册，则即使 REQUIRE_USER_MATCH 设置为 FALSE，用户也必须使用 MFA 进行身份验证。

可以选择在用户加入并且可能尚未所有用户都已注册 Azure MFA 时创建该键并将其设置为 FALSE。 但是，由于设置该键允许未注册 MFA 的用户登录，因此应在转到生产环境之前删除该键。

## <a name="troubleshooting"></a>疑难解答

### <a name="nps-extension-health-check-script"></a>NPS 扩展运行状况检查脚本

下面的脚本可用于在对 NPS 扩展进行故障排除时执行基本运行状况检查步骤。

[MFA_NPS_Troubleshooter ps1](https://docs.microsoft.com/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

---

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>如何验证是否已按预期安装了客户端证书？

请在证书存储中查找安装程序创建的自签名证书，然后检查私钥中是否包含授予“网络服务”用户的权限。**** 证书的使用者名称为 **CN \<tenantid\>，OU = Microsoft NPS Extension**

*AzureMfaNpsExtnConfigSetup*脚本生成的自签名证书也具有两年的有效期。 当验证是否已安装证书时，还应检查证书是否尚未过期。

---

### <a name="how-can-i-verify-that-my-client-cert-is-associated-to-my-tenant-in-azure-active-directory"></a>如何验证客户端证书是否已关联到 Azure Active Directory 中的租户？

打开 PowerShell 命令提示符并运行以下命令：

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

这些命令会在 PowerShell 会话中列显所有可将你的租户与 NPS 扩展实例相关联的证书。 通过将客户端证书导出为不带私钥的“Base-64 编码 X.509(.cer)”文件来找到你的证书，并将它与 PowerShell 中的列表进行比较。

以下命令将在“C:”驱动器上以 .cer 格式创建名为“npscertificate”的文件。

``` PowerShell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

运行此命令后，请切换到 C 驱动器，找到该文件，然后双击该文件。 转到详细信息并向下滚动到“指纹”，将服务器上安装的证书的指纹与此指纹进行比较。 证书指纹应匹配。

如果该命令返回了多个证书，可以使用 Valid-From 和 Valid-Until 时间戳（采用用户可读格式）来筛选出每个不相符的项。

---

### <a name="why-cannot-i-sign-in"></a>为什么无法登录？

检查密码是否未过期。 NPS 扩展不支持在登录工作流中更改密码。 请与你组织的 IT 人员联系以获得进一步的帮助。

---

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>我的请求为何失败并返回 ADAL 令牌错误？

此错误可能是多种原因之一造成的。 使用以下步骤可帮助排查问题：

1. 重新启动 NPS 服务器。
2. 验证是否已按预期安装了客户端证书。
3. 验证该证书是否与 Azure AD 上的租户关联。
4. 验证是否可以从运行该扩展的服务器访问 `https://login.microsoftonline.com/`。

---

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>身份验证为何失败并在 HTTP 日志中返回一条错误，指出找不到用户？

验证 AD Connect 是否正在运行，并且该用户是否在 Windows Active Directory 和 Azure Active Directory 中存在。

---

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>日志中为何出现 HTTP 连接错误，并且所有身份验证都失败？

验证是否可以从运行该 NPS 扩展的服务器访问 https://adnotifications.windowsazure.com。

---

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>为什么虽然存在有效证书，身份验证不起作用？

如果以前的计算机证书已过期，并且已生成新证书，则应删除任何过期的证书。 如果证书过期，则可能会导致 NPS 扩展开始出现问题。

若要检查是否具有有效的证书，请使用 MMC 检查本地计算机帐户的证书存储，并确保证书尚未过期。 若要生成新的有效证书，请重新运行 "[运行 PowerShell 脚本](#run-the-powershell-script)" 部分下的步骤

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>管理 TLS/SSL 协议和密码套件

建议禁用或删除较旧和较弱的密码套件，除非组织需要这些套件。 若要了解如何完成此任务，可以参阅[为 AD FS 管理 SSL/TLS 协议和密码套件](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)一文。

### <a name="additional-troubleshooting"></a>其他疑难解答

可在[解决来自 Azure 多重身份验证的 NPS 扩展中的错误消息](howto-mfa-nps-extension-errors.md)一文中找到其他故障排除指南和可能的解决方案。

## <a name="next-steps"></a>后续步骤

- [Windows Server 中网络策略服务器的概述和配置](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)

- 在[适用于多重身份验证 NPS 扩展的高级配置选项](howto-mfa-nps-extension-advanced.md)中配置登录名的备用 ID 或设置不应执行双重验证的 IP 的异常列表

- 了解如何使用 NPS 扩展来集成[远程桌面网关](howto-mfa-nps-extension-rdg.md)和 [VPN 服务器](howto-mfa-nps-extension-vpn.md)

- [解决 Azure 多重身份验证的 NPS 扩展出现的错误消息](howto-mfa-nps-extension-errors.md)
