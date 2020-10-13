---
title: 将 Azure 多重身份验证用于 NPS-Azure Active Directory
description: 了解如何结合使用 Azure 多重身份验证功能和现有的网络策略服务器 (NPS) 身份验证基础结构
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/31/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 5095df51fe430990e200b7bc7c3ca03feb0799d5
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964275"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-multi-factor-authentication"></a>通过 Azure 多重身份验证将现有网络策略服务器 (NPS) 基础结构集成

用于 Azure 多重身份验证的网络策略服务器 (NPS) 扩展将基于云的 MFA 功能添加到使用现有服务器的身份验证基础结构。 使用 NPS 扩展，可将电话呼叫、短信或电话应用验证添加到现有的身份验证流，而无需安装、配置和维护新服务器。

NPS 扩展充当 RADIUS 与基于云的 Azure 多重身份验证之间的适配器，为联合用户或同步用户提供另一因素身份验证。

## <a name="how-the-nps-extension-works"></a>NPS 扩展的工作原理

使用适用于 Azure 多重身份验证的 NPS 扩展时，身份验证流包括以下组件：

1. **NAS/VPN 服务器**接收来自 VPN 客户端的请求，并将其转换为可发往 NPS 服务器的 RADIUS 请求。
2. **NPS 服务器** 连接到 Active Directory 域服务 (AD DS) 执行 RADIUS 请求的主要身份验证，成功后，将请求传递到任何已安装的扩展。  
3. **NPS 扩展** 将触发对 Azure 多重身份验证的请求，以便进行辅助身份验证。 该扩展收到响应后，如果 MFA 质询成功，该扩展将通过向 NPS 服务器提供由 Azure STS 颁发的、包含 MFA 声明的安全令牌，来完成身份验证请求。
4. **AZURE MFA** 与 Azure Active Directory (Azure AD) 来检索用户的详细信息，并使用配置给用户的验证方法执行辅助身份验证。

下图显示了此高级身份验证请求流：

![通过 VPN 服务器到 NPS 服务器和 Azure 多重身份验证 NPS 扩展进行用户身份验证的身份验证流示意图](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>RADIUS 协议行为和 NPS 扩展

由于 RADIUS 是 UDP 协议，因此发送方假设数据包丢失并等待响应。 一段时间后，连接可能会超时。如果是这样，则会重新发送数据包，因为发送方假设数据包未到达目标。 在本文的身份验证方案中，VPN 服务器发送请求并等待响应。 如果连接超时，VPN 服务器将再次发送请求。

![NPS 服务器响应超时后的 RADIUS UDP 数据包流和请求的关系图](./media/howto-mfa-nps-extension/radius-flow.png)

NPS 服务器可能不会在连接超时之前响应 VPN 服务器的原始请求，因为 MFA 请求可能仍在进行处理。 用户可能未成功响应 MFA 提示，因此 Azure 多重身份验证 NPS 扩展正在等待该事件完成。 在这种情况下，NPS 服务器会将其他 VPN 服务器请求识别为重复请求。 NPS 服务器会丢弃这些重复的 VPN 服务器请求。

![NPS 服务器的关系图丢弃来自 RADIUS 服务器的重复请求](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

如果查看 NPS 服务器日志，可能会看到这些附加请求被丢弃。 此行为是为了防止最终用户获取单个身份验证尝试的多个请求而设计的。 NPS 服务器事件日志中的丢弃请求不表示 NPS 服务器或 Azure 多重身份验证 NPS 扩展出现问题。

为了最大限度地减少丢弃的请求，建议配置 VPN 服务器的超时至少为60秒。 如果需要，或要减少事件日志中的丢弃请求，可以将 VPN 服务器超时值提高到90或120秒。

由于此 UDP 协议行为，NPS 服务器可能会收到重复请求并发送另一次 MFA 提示，即使用户已对初始请求做出响应。 若要避免这种计时情况，Azure 多重身份验证 NPS 扩展在将成功的响应发送到 VPN 服务器后，将继续筛选和丢弃最多10秒的重复请求。

![在返回成功响应后，NPS 服务器的关系图会继续丢弃来自 VPN 服务器的重复请求](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

同样，即使在 Azure 多重身份验证提示成功的情况下，也可能会在 NPS 服务器事件日志中看到丢弃的请求。 这是预期的行为，并不表示 NPS 服务器或 Azure 多重身份验证 NPS 扩展出现问题。

## <a name="plan-your-deployment"></a>规划部署

NPS 扩展自动处理冗余，因此无需特殊配置。

你可以根据需要创建任意数量的启用 Azure 多重身份验证的 NPS 服务器。 如果确实安装了多个服务器，应该为其中每个服务器使用不同的客户端证书。 为每个服务器创建一个证书意味着可以单独更新每个证书，而无需担心所有服务器的停机时间。

VPN 服务器路由身份验证请求，因此它们需要知道新的启用了 Azure 多重身份验证的 NPS 服务器。

## <a name="prerequisites"></a>先决条件

NPS 扩展需与现有基础结构配合工作。 在开始之前，请确保满足以下先决条件。

### <a name="licenses"></a>许可证

Azure 多重身份验证的 NPS 扩展可供具有 [Azure 多重身份验证许可证的](multi-factor-authentication.md)客户使用。 Azure 多重身份验证的基于消耗的许可证（例如每用户或每个身份验证许可证）与 NPS 扩展不兼容。

### <a name="software"></a>软件

Windows Server 2012 或更高版本。

### <a name="libraries"></a>库

需要手动安装以下库：

- [Visual C++ Redistributable for Visual Studio 2015](https://www.microsoft.com/download/details.aspx?id=48145)

以下库会自动随扩展一起安装。

- [Visual C++ Redistributable Packages for Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块版本 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

还可以通过在安装过程中运行的配置脚本（如果尚未存在）来安装用于 Windows PowerShell 的 Microsoft Azure Active Directory 模块。 如果尚未安装此模块，则无需提前安装。

### <a name="azure-active-directory"></a>Azure Active Directory

使用 NPS 扩展的每个用户都必须使用 Azure AD Connect 同步到 Azure AD，并且必须注册 MFA。

安装该扩展时，需要 Azure AD 租户的 *租户 ID* 和管理员凭据。 若要获取租户 ID，请完成以下步骤：

1. 以 Azure 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择 " **Azure Active Directory**"。
1. 在 " **概述** " 页上，将显示 *租户信息* 。 在 " *租户 ID*" 旁边，选择 " **复制** " 图标，如以下示例屏幕截图所示：

   ![正在从 Azure 门户获取租户 ID](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>网络要求

NPS 服务器必须能够通过端口80和443与以下 Url 通信：

* *https： \/ /adnotifications.windowsazure.com*
* *https： \/ /login.microsoftonline.com*
* *https： \/ /credentials.azure.com*

此外，还需要连接到以下 Url，才能 [使用提供的 PowerShell 脚本完成适配器的安装](#run-the-powershell-script)：

* *https： \/ /login.microsoftonline.com*
* *https： \/ /provisioningapi.microsoftonline.com*
* *https： \/ /aadcdn.msauth.net*

## <a name="prepare-your-environment"></a>准备环境

在安装 NPS 扩展之前，请准备好环境来处理身份验证流量。

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>在已加入域的服务器上启用 NPS 角色

NPS 服务器连接到 Azure AD 并对 MFA 请求进行身份验证。 为此角色选择一台服务器。 我们建议选择一台不处理来自其他服务的请求的服务器，因为对于不是 RADIUS 的任何请求，NPS 扩展都会引发错误。 NPS 服务器必须设置为你的环境的主要和辅助身份验证服务器。 不能将 RADIUS 请求通过代理发送到其他服务器。

1. 在服务器上，打开 **服务器管理器**。 从 "*快速*启动" 菜单中选择 "**添加角色和功能向导**"。
2. 对于安装类型，请选择 " **基于角色或基于功能的安装**"。
3. 选择“网络策略和访问服务”服务器角色。 此时会弹出一个窗口，告诉你运行此角色所需的其他功能。
4. 继续执行向导，直到出现 " *确认* " 页。 准备就绪后，选择 " **安装**"。

安装 NPS 服务器角色可能需要几分钟的时间。 完成后，继续执行以下部分，将此服务器配置为处理来自 VPN 解决方案的传入的 RADIUS 请求。

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>将 VPN 解决方案配置为与 NPS 服务器通信

根据使用的 VPN 解决方案，配置 RADIUS 身份验证策略的步骤会有所不同。 配置 VPN 策略以指向 RADIUS NPS 服务器。

### <a name="sync-domain-users-to-the-cloud"></a>将域用户同步到云

此步骤可能已在租户上完成，但最好仔细检查 Azure AD Connect 最近是否已同步数据库。

1. 以管理员身份登录到 [Azure 门户](https://portal.azure.com)。
2. 依次选择“Azure Active Directory” > “Azure AD Connect”
3. 确认同步状态是“已启用”，并确认上次同步时间是在不到一小时前。

如果需要启动新的同步循环，请参阅 [Azure AD Connect 同步：计划程序](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler)。

### <a name="determine-which-authentication-methods-your-users-can-use"></a>决定你的用户可以使用哪些身份验证方法

有两个因素会影响 NPS 扩展部署可以使用哪些身份验证方法：

1. 在 RADIUS 客户端（VPN、Netscaler 服务器或其他客户端）与 NPS 服务器之间使用的密码加密算法。
   - **PAP** 支持云中的 Azure 多重身份验证的所有身份验证方法：电话呼叫、单向短信、移动应用通知、OATH 硬件令牌和移动应用验证码。
   - **CHAPV2** 和 **EAP** 支持电话呼叫和移动应用通知。

      > [!NOTE]
      > 部署 NPS 扩展时，请根据这些因素评估你的用户可使用哪些方法。 如果你的 RADIUS 客户端支持 PAP，但客户端 UX 没有可用于输入验证码的输入字段，则电话呼叫和移动应用通知是两种受支持的选项。
      >
      > 此外，如果你的 VPN 客户端 UX 支持输入字段，并且你配置了网络访问策略，则身份验证可能会成功。 但是，在网络策略中配置的 RADIUS 属性都不会同时应用于网络访问设备，如 RRAS 服务器和 VPN 客户端。 因此，VPN 客户端的访问权限可能比所需的更多，或者不能访问。

2. 客户端应用程序（VPN、Netscaler 服务器或其他客户端）可以处理的输入方法。 例如，VPN 客户端是否有一些手段允许用户键入通过文本或移动应用收到的验证码？

可以在 Azure 中[禁用不受支持的身份验证方法](howto-mfa-mfasettings.md#verification-methods)。

### <a name="register-users-for-mfa"></a>用户注册 MFA

在部署和使用 NPS 扩展之前，需要为 MFA 注册需要执行 Azure 多重身份验证的用户。 若要在部署扩展时对其进行测试，还需要至少一个完全注册到 Azure 多重身份验证的测试帐户。

如果需要创建和配置测试帐户，请使用以下步骤：

1. 通过测试帐户登录 [https://aka.ms/mfasetup](https://aka.ms/mfasetup)。
2. 按照提示设置验证方法。
3. 在作为管理员用户的 Azure 门户中， [创建一个条件性访问策略](howto-mfa-getstarted.md#create-conditional-access-policy) 来要求对测试帐户进行多重身份验证。

> [!IMPORTANT]
>
> 确保用户已成功注册 Azure 多重身份验证。 如果用户以前仅注册了自助式密码重置 (SSPR)，则为其帐户启用 StrongAuthenticationMethods。 配置 StrongAuthenticationMethods 时将强制执行 Azure 多重身份验证，即使用户仅注册了 SSPR。
>
> 可以启用合并安全注册，该安全注册可同时配置 SSPR 和 Azure 多重身份验证。 有关详细信息，请参阅[在 Azure Active Directory 中启用合并安全信息注册](howto-registration-mfa-sspr-combined.md)。
>
> 如果用户以前仅启用了 SSPR，也可以[强制用户重新注册身份验证方法](howto-mfa-userdevicesettings.md#manage-user-authentication-options)。

## <a name="install-the-nps-extension"></a>安装 NPS 扩展

> [!IMPORTANT]
> 在不同于 VPN 接入点的服务器上安装 NPS 扩展。

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>针对 Azure MFA 下载并安装 NPS 扩展

若要下载和安装 NPS 扩展，请完成以下步骤：

1. 从 Microsoft 下载中心[下载 NPS 扩展](https://aka.ms/npsmfa)。
1. 将二进制文件复制到要配置的网络策略服务器。
1. 运行 *setup.exe* 并按照安装说明操作。 如果遇到错误，请确保已成功安装 [先决条件部分中的库](#libraries) 。

#### <a name="upgrade-the-nps-extension"></a>升级 NPS 扩展

如果以后升级现有 NPS 扩展安装，若要避免重新启动基础服务器，请完成以下步骤：

1. 卸载现有版本。
1. 运行新的安装程序。
1. * (IAS) 服务重新启动网络策略服务器*。

### <a name="run-the-powershell-script"></a>运行 PowerShell 脚本

安装程序会在 (上创建一个 PowerShell 脚本 `C:\Program Files\Microsoft\AzureMfa\Config` ，其中 `C:\` 安装驱动器) 。 此 PowerShell 脚本在每次运行时执行以下操作：

* 创建自签名证书。
* 将证书的公钥关联到 Azure AD 上的服务主体。
* 将证书存储在本地计算机证书存储中。
* 向网络用户授予对证书私钥的访问权限。
* 重新启动 NPS 服务。

除非你想要使用自己的证书 (而不是 PowerShell 脚本生成的自签名证书) ，否则请运行 PowerShell 脚本来完成 NPS 扩展安装。 如果在多台服务器上安装扩展，则每个服务器都应有自己的证书。

若要提供负载平衡功能或实现冗余，请根据需要在其他 NPS 服务器上重复上述步骤：

1. 以管理员身份打开 Windows PowerShell 提示符。
1. 将目录更改为安装程序创建 PowerShell 脚本的位置：

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. 运行安装程序创建的 PowerShell 脚本。

   > [!IMPORTANT]
   > 对于使用 Azure 政府版或 Azure 中国世纪互联云的客户，请先 `Connect-MsolService` 在 *AzureMfaNpsExtnConfigSetup.ps1* 脚本中编辑 cmdlet，使其包含所需云的 *AzureEnvironment* 参数。 例如，指定 *-AzureEnvironment USGovernment* 或 *-AzureEnvironment AzureChinaCloud*。
   >
   > 有关详细信息，请参阅 [connect-msolservice 参数引用](/powershell/module/msonline/connect-msolservice#parameters)。

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. 出现提示时，请以管理员身份登录到 Azure AD。
1. PowerShell 会提示输入租户 ID。 使用从 "先决条件" 部分中的 Azure 门户复制的 *租户 ID* GUID。
1. 脚本完成后，会显示一条成功消息。  

如果以前的计算机证书已过期，并且已生成新证书，则应删除所有过期的证书。 证书过期会导致 NPS 扩展启动出现问题。

> [!NOTE]
> 如果使用自己的证书，而不是使用 PowerShell 脚本生成的证书，请确保它们符合 NPS 命名约定。 使用者名称必须是 **CN = \<TenantID\> ，OU = Microsoft NPS Extension**。

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Microsoft Azure 政府或 Azure 中国世纪互联的其他步骤

对于使用 Azure 政府版或 Azure 中国世纪互联云的客户，需要在每个 NPS 服务器上执行以下其他配置步骤。

> [!IMPORTANT]
> 仅当你是 Azure 政府版或 Azure 中国世纪互联客户时才配置这些注册表设置。

1. 如果你是 Azure 政府版或 Azure 中国世纪互联客户，请在 NPS 服务器上打开 **注册表编辑器** 。
1. 导航到 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`。
1. 对于 Azure 政府版客户，请设置以下键值：

    | 注册表项       | 值 |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. 对于 Azure 中国世纪互联客户，请设置以下键值：

    | 注册表项       | 值 |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. 重复上述两个步骤，为每个 NPS 服务器设置注册表项值。
1. 为每个 NPS 服务器重启 NPS 服务。

    为了最大限度地减少影响，请从 NLB 轮换中逐个移除 NPS 服务器，然后等待所有连接排出。

### <a name="certificate-rollover"></a>证书滚动更新

对于 NPS 扩展的 release *1.0.1.32* ，现在支持读取多个证书。 此功能有助于在证书更新过期之前方便滚动。 如果你的组织运行的是早期版本的 NPS 扩展，请升级到版本 *1.0.1.32* 或更高版本。

`AzureMfaNpsExtnConfigSetup.ps1` 脚本创建的证书有效期为 2 年。 监视证书的过期时间。 NPS 扩展的证书放置在 "*个人*" 下的 "*本地计算机*" 证书存储中，并*颁发给*为安装脚本提供的租户 ID。

当证书接近到期日期时，应创建新的证书来替换该证书。  通过再次运行 `AzureMfaNpsExtnConfigSetup.ps1` 并在出现提示时保持相同的租户 ID，可以完成此过程。 应在环境中的每个 NPS 服务器上重复此过程。

## <a name="configure-your-nps-extension"></a>配置 NPS 扩展

准备好环境后，NPS 扩展现已安装在所需的服务器上，你可以配置扩展。

本部分包含一些设计注意事项和建议，帮助用户成功完成 NPS 扩展的部署。

### <a name="configuration-limitations"></a>配置限制

- 适用于 Azure 多重身份验证的 NPS 扩展不包含用于将用户和设置从 MFA 服务器迁移到云的工具。 出于此原因，我们建议将扩展用于新部署，而非现有部署。 如果在现有部署上使用扩展，用户必须重新进行证明才能在云中填充其 MFA 详细信息。  
- NPS 扩展使用本地 AD DS 环境中的 UPN 来识别 Azure 多重身份验证上用于执行辅助身份验证的用户。可以将该扩展配置为使用其他标识符，例如备用登录 ID 或 UPN 以外的自定义 AD DS 字段。 有关详细信息，请参阅[用于多重身份验证的 NPS 扩展的高级配置选项](howto-mfa-nps-extension-advanced.md)一文。
- 并非所有加密协议都支持所有验证方法。
   - PAP 支持电话呼叫、单向短信、移动应用通知和移动应用验证码
   - **CHAPV2** 和 **EAP** 支持电话呼叫和移动应用通知

### <a name="control-radius-clients-that-require-mfa"></a>控制需要 MFA 的 RADIUS 客户端

使用 NPS 扩展为 RADIUS 客户端启用 MFA 后，需要此客户端的所有身份验证才能执行 MFA。 如果想要为一部分（而不是全部）RADIUS 客户端启用 MFA，可以配置两台 NPS 服务器，并只在其中一台服务器上安装该扩展。

将需要执行 MFA 的 RADIUS 客户端配置为向配置了该扩展的 NPS 服务器发送请求，将其他 RADIUS 客户端配置为向未配置该扩展的 NPS 服务器发送请求。

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>为未注册 MFA 的用户做好准备

如果你的某些用户未注册 MFA，你可以确定当他们尝试身份验证时要发生什么行为。 若要控制此行为，请使用注册表路径*HKLM\Software\Microsoft\AzureMFA*中的设置*REQUIRE_USER_MATCH* 。 此项设置提供单个配置选项：

| 密钥 | 值 | 默认 |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | TRUE/FALSE | 未设置（相当于 TRUE） |

此设置确定用户未注册 MFA 时要执行的操作。 如果该注册表项不存在、未设置或设置为 *TRUE*，并且用户未注册，则该扩展将无法进行 MFA 质询。

如果将 "密钥" 设置为 " *FALSE* "，且未注册用户，则将继续进行身份验证而不执行 MFA。 如果用户在 MFA 中注册，则即使 *REQUIRE_USER_MATCH* 设置为 *FALSE*，也必须使用 mfa 进行身份验证。

你可以选择在用户加入时创建此密钥并将其设置为 *FALSE* ，并且可能不会全部注册到 Azure 多重身份验证。 但是，由于设置该键允许未注册 MFA 的用户登录，因此应在转到生产环境之前删除该键。

## <a name="troubleshooting"></a>疑难解答

### <a name="nps-extension-health-check-script"></a>NPS 扩展运行状况检查脚本

对 NPS 扩展进行故障排除时，可以使用以下脚本执行基本的运行状况检查步骤。

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>如何验证是否已按预期安装了客户端证书？

请在证书存储中查找安装程序创建的自签名证书，然后检查私钥中是否包含授予“网络服务”用户的权限。 证书的使用者名称为 **CN \<tenantid\> ，OU = Microsoft NPS Extension**

脚本生成的自签名证书的 `AzureMfaNpsExtnConfigSetup.ps1` 有效期为两年。 当验证是否已安装证书时，还应检查证书是否尚未过期。

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>如何验证我的客户端证书是否与 Azure AD 中的租户相关联？

打开 PowerShell 命令提示符并运行以下命令：

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

这些命令会在 PowerShell 会话中列显所有可将你的租户与 NPS 扩展实例相关联的证书。 通过将客户端证书导出为不带私钥的 *64 编码 x.509 ( .cer) * 文件来查找证书，并将其与 PowerShell 中的列表进行比较。

以下命令将在*C：* 驱动器的根目录中创建一个名为*npscertificate*的文件，格式为 *.cer*。

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

运行此命令后，请在 *C：* 驱动器的根目录中找到该文件，然后双击该文件。 请参阅 "详细信息"，然后向下滚动到 "指纹"。 将服务器上安装的证书的指纹与此证书进行比较。 证书指纹应匹配。

如果命令返回多个证书，则可使用 "*有效开始*时间" 和 "*有效截止*时间" 时间戳（以用户可读形式）来筛选出明显的 valid-from 和 valid-until。

### <a name="why-cannot-i-sign-in"></a>为什么无法登录？

检查密码是否未过期。 NPS 扩展不支持在登录工作流中更改密码。 请与组织的 IT 人员联系以获得进一步的帮助。

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>我的请求为何失败并返回 ADAL 令牌错误？

此错误可能是多种原因之一造成的。 使用以下步骤来解决问题：

1. 重新启动 NPS 服务器。
2. 验证是否已按预期安装了客户端证书。
3. 验证该证书是否与 Azure AD 上的租户关联。
4. 验证是否可以从运行该扩展的服务器访问 `https://login.microsoftonline.com/`。

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>身份验证为何失败并在 HTTP 日志中返回一条错误，指出找不到用户？

验证 AD Connect 是否正在运行，以及用户是否同时出现在本地 AD DS 环境和 Azure AD 中。

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>日志中为何出现 HTTP 连接错误，并且所有身份验证都失败？

验证是否可以从运行该 NPS 扩展的服务器访问 https://adnotifications.windowsazure.com 。

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>为什么尽管存在有效的证书，身份验证也不起作用？

如果以前的计算机证书已过期，并且已生成新证书，则删除任何过期的证书。 过期的证书可能会导致 NPS 扩展开始出现问题。

若要检查是否具有有效的证书，请使用 MMC 检查本地 *计算机帐户的证书存储* ，并确保证书未通过其到期日期。 若要生成新的有效证书，请重新 [运行运行 PowerShell 安装程序脚本](#run-the-powershell-script)中的步骤。

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>为什么在 NPS 服务器日志中看到丢弃的请求？

如果超时值太小，VPN 服务器可能会向 NPS 服务器发送重复的请求。 NPS 服务器将检测这些重复的请求并将其丢弃。 此行为是设计造成的，并不表示 NPS 服务器或 Azure 多重身份验证 NPS 扩展出现问题。

若要详细了解 NPS 服务器日志中出现丢弃的数据包的原因，请参阅本文开头的 [RADIUS 协议行为和 NPS 扩展](#radius-protocol-behavior-and-the-nps-extension) 。

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>管理 TLS/SSL 协议和密码套件

建议禁用或删除旧的和较弱的密码套件，除非你的组织需要。 有关如何完成此任务的信息，请参阅 [管理 SSL/TLS 协议和密码套件 AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>更多故障排除方法

可以在本文中找到 [有关 Azure 多重身份验证的 NPS 扩展中的错误消息](howto-mfa-nps-extension-errors.md)。

## <a name="next-steps"></a>后续步骤

- [Windows Server 中网络策略服务器的概述和配置](/windows-server/networking/technologies/nps/nps-top)

- 在[适用于多重身份验证 NPS 扩展的高级配置选项](howto-mfa-nps-extension-advanced.md)中配置登录名的备用 ID 或设置不应执行双重验证的 IP 的异常列表

- 了解如何使用 NPS 扩展来集成[远程桌面网关](howto-mfa-nps-extension-rdg.md)和 [VPN 服务器](howto-mfa-nps-extension-vpn.md)

- [解决 Azure 多重身份验证的 NPS 扩展出现的错误消息](howto-mfa-nps-extension-errors.md)