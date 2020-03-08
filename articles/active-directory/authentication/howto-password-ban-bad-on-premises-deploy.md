---
title: 部署本地 Azure AD 密码保护
description: 了解如何在本地 Active Directory 域服务环境中规划和部署 Azure AD 密码保护
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: a977eac19128886dd3c379e200f7cb78066a06af
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671729"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>规划和部署本地 Azure Active Directory 密码保护

用户经常会创建密码，使用常用的词语，如学校、体育团队或名人。 这些密码易于猜测，并使基于字典的攻击变弱。 若要在组织中强制实施强密码，Azure Active Directory （Azure AD）密码保护提供了全局和自定义的禁止密码列表。 如果这些禁止密码列表中存在匹配项，则密码更改请求失败。

若要保护本地 Active Directory 域服务（AD DS）环境，可以安装并配置 Azure AD 密码保护，以便使用本地 DC。 本文介绍如何在本地环境中安装和注册 Azure AD 密码保护代理服务，以及 Azure AD 密码保护 DC 代理。

有关 Azure AD 密码保护在本地环境中的工作原理的详细信息，请参阅[如何为 Windows Server Active Directory 强制执行 Azure AD 密码保护](concept-password-ban-bad-on-premises.md)。

## <a name="deployment-strategy"></a>部署策略

下图显示了 Azure AD 密码保护的基本组件如何在本地 Active Directory 环境中协同工作：

![Azure AD 密码保护组件如何配合工作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

在部署软件之前，最好先查看软件的工作方式。 有关详细信息，请参阅[Azure AD 密码保护的概念性概述](concept-password-ban-bad-on-premises.md)。

建议您在*审核*模式下启动部署。 审核模式是默认初始设置，可以继续设置密码。 将被阻止的密码记录在事件日志中。 在审核模式下部署代理服务器和 DC 代理后，请监视在强制执行策略时密码策略对用户产生的影响。

在审核阶段，许多组织发现下列情况适用：

* 需要改进现有的操作过程，以使用更安全的密码。
* 用户经常使用不安全的密码。
* 他们需要通知用户即将执行的安全强制更改、可能对这些更改的影响，以及如何选择更安全的密码。

还可以通过更强的密码验证来影响现有 Active Directory 域控制器部署自动化。 建议在审核期评估过程中至少发生一次 DC 升级和一个 DC 降级，以帮助发现此类问题。 有关详细信息，请参阅以下文章：

* [Ntdsutil 无法设置弱目录服务修复模式密码](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [由于目录服务修复模式密码弱，域控制器副本升级失败](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [由于本地管理员密码弱，域控制器降级失败](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

在一个合理的时间段内，在审核模式下运行此功能后，可以将该配置从*审核*切换为 "*强制执行*"，以要求更安全的密码。 此期间的其他监视是一个不错的主意。

### <a name="multiple-forest-considerations"></a>多林注意事项

跨多个林部署 Azure AD 密码保护不需要满足其他要求。

每个林都是独立配置的，如以下部分中所述，用于[部署本地 Azure AD 密码保护](#download-required-software)。 每个 Azure AD 密码保护代理只能支持其加入的林中的域控制器。

任何林中的 Azure AD 密码保护软件都不知道在其他林中部署的密码保护软件，不管 Active Directory 信任配置如何。

### <a name="read-only-domain-controller-considerations"></a>只读域控制器注意事项

不会处理密码更改或设置事件，也不会在只读域控制器（Rodc）上保存。 相反，会将其转发到可写域控制器。 不需要在 Rodc 上安装 Azure AD 密码保护 DC 代理软件。

此外，不支持在只读域控制器上运行 Azure AD 密码保护代理服务。

### <a name="high-availability-considerations"></a>高可用性注意事项

密码保护的主要问题是当林中的 Dc 尝试从 Azure 下载新策略或其他数据时，Azure AD 密码保护代理服务器的可用性。 在决定要调用哪个代理服务器时，每个 Azure AD 密码保护 DC 代理都使用简单的轮循机制算法。 代理跳过未响应的代理服务器。

对于大多数完全连接的 Active Directory 部署，这些部署具有目录和 sysvol 文件夹状态的正常复制，两 Azure AD 密码保护代理服务器都足以确保可用性。 此配置将导致及时下载新策略和其他数据。 如果需要，可以部署其他 Azure AD 密码保护代理服务器。

Azure AD 密码保护 DC 代理软件的设计可减轻与高可用性相关的常见问题。 Azure AD 密码保护 DC 代理维护最近下载的密码策略的本地缓存。 即使所有已注册的代理服务器都变为不可用，Azure AD 密码保护 DC 代理也会继续强制执行其缓存的密码策略。

大型部署中的密码策略合理的更新频率通常是天，而不是小时或更短时间。 因此，代理服务器的短暂中断不会显著影响 Azure AD 密码保护。

## <a name="deployment-requirements"></a>部署要求

有关许可的信息，请参阅[Azure AD 密码保护许可要求](concept-password-ban-bad.md#license-requirements)。

以下核心要求适用：

* 所有安装了 Azure AD Password Protection 组件的计算机都必须安装有通用 C 运行时。
    * 可以通过确保所有更新都 Windows 更新来获取运行时。 也可以在特定于 OS 的更新包中获取它。 有关详细信息，请参阅[Windows 中的通用 C 运行时更新](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)。
* 你需要一个在目录林根级域中具有 Active Directory 域管理员权限的帐户，以便向 Azure AD 注册 Windows Server Active Directory 林。
* 必须在运行 Windows Server 2012 的域中的所有域控制器上启用密钥分发服务。 默认情况下，此服务通过手动触发器启动启用。
* 每个域中至少一个域控制器之间必须存在网络连接，并且至少有一台服务器承载用于 Azure AD 密码保护的代理服务。 此连接必须允许域控制器访问 RPC 终结点映射器端口135和代理服务上的 RPC 服务器端口。
    * 默认情况下，RPC 服务器端口是动态 RPC 端口，但可以配置为[使用静态端口](#static)。
* 将安装 Azure AD 密码保护代理服务的所有计算机必须具有对以下终结点的网络访问权限：

    |**终结点**|**用途**|
    | --- | --- |
    |`https://login.microsoftonline.com`|身份验证请求|
    |`https://enterpriseregistration.windows.net`|Azure AD 密码保护功能|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD 密码保护 DC 代理

以下要求适用于 Azure AD 密码保护 DC 代理：

* 将安装 Azure AD 密码保护 DC 代理软件的所有计算机都必须运行 Windows Server 2012 或更高版本。
    * Active Directory 域或林不需要处于 Windows Server 2012 域功能级别（DFL）或林功能级别（FFL）。 如[设计原则](concept-password-ban-bad-on-premises.md#design-principles)中所述，要运行 DC 代理或代理软件，无需最低的 DFL 或 FFL。
* 运行 Azure AD 密码保护 DC 代理的所有计算机必须安装 .NET 4.5。
* 运行 Azure AD 密码保护 DC 代理服务的任何 Active Directory 域必须使用分布式文件系统复制（DFSR）进行 sysvol 复制。
   * 如果你的域尚未使用 DFSR，则必须先迁移，然后才能安装 Azure AD 密码保护。 有关详细信息，请参阅[SYSVOL 复制迁移指南： FRS 到 DFS 复制](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Azure AD 密码保护 DC 代理软件当前将安装在仍在使用 FRS 的域控制器（用于 DFSR 的前置技术）中进行 sysvol 复制，但该软件在此环境中将无法正常运行。
    >
    > 其他负面影响包括未能复制的单个文件，sysvol 还原过程会成功，但不会以静默方式复制所有文件。
    >
    > 将你的域迁移到尽快使用 DFSR，这两种方法都适用于 DFSR 固有的优势以及取消阻止 Azure AD 密码保护的部署。 在仍使用 FRS 的域中运行时，软件的未来版本将自动禁用。

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD 密码保护代理服务

以下要求适用于 Azure AD 密码保护代理服务：

* 将安装 Azure AD 密码保护代理服务的所有计算机都必须运行 Windows Server 2012 R2 或更高版本。

    > [!NOTE]
    > Azure AD 密码保护代理服务部署是部署 Azure AD 密码保护的必需要求，即使域控制器可能有出站直接 internet 连接。

* 将安装 Azure AD 密码保护代理服务的所有计算机必须安装了 .NET 4.7。
    * .NET 4.7 应已安装在完全更新的 Windows 服务器上。 如有必要，请下载并运行在[Windows .NET Framework 4.7 脱机安装程序](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)中找到的安装程序。
* 必须将承载 Azure AD 密码保护代理服务的所有计算机配置为授予域控制器登录到代理服务的能力。 此功能通过 "从网络访问此计算机" 权限分配来控制。
* 承载 Azure AD 密码保护代理服务的所有计算机都必须配置为允许出站 TLS 1.2 HTTP 流量。
* 使用 Azure AD 注册 Azure AD 密码保护代理服务和林的*全局管理员*帐户。
* 必须为[应用程序代理环境设置过程](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)中指定的端口和 url 集启用网络访问。

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Microsoft Azure AD 连接代理更新程序先决条件

Microsoft Azure AD Connect Agent 更新程序服务与 Azure AD 密码保护代理服务并行安装。 需要额外的配置才能使 Microsoft Azure AD Connect Agent 更新服务能够正常工作：

* 如果你的环境使用 HTTP 代理服务器，请遵循[使用现有本地代理服务器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)中所述的准则。
* Microsoft Azure AD 连接代理更新程序服务还需要[tls 要求](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements)中指定的 tls 1.2 步骤。

> [!WARNING]
> Azure AD 密码保护代理和 Azure AD 应用程序代理安装不同版本的 Microsoft Azure AD 连接代理更新程序服务，这就是说明如何引用应用程序代理内容的原因。 并行安装时这些不同版本不兼容，因此不建议在同一台计算机上安装 Azure AD 密码保护代理和应用程序代理。

## <a name="download-required-software"></a>下载所需软件

本地 Azure AD 密码保护部署有两个所需的安装程序：

* Azure AD 密码保护 DC 代理（*AzureADPasswordProtectionDCAgentSetup*）
* Azure AD 密码保护代理（*AzureADPasswordProtectionProxySetup*）

从[Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)下载这两个安装程序。

## <a name="install-and-configure-the-proxy-service"></a>安装和配置代理服务

Azure AD 密码保护代理服务通常位于本地 AD DS 环境中的成员服务器上。 安装后，Azure AD Password Protection proxy service 将与 Azure AD 通信，以维护 Azure AD 租户的全局和客户禁止密码列表的副本。

在下一部分中，会在本地 AD DS 环境中的域控制器上安装 Azure AD 密码保护 DC 代理。 这些 DC 代理与代理服务进行通信，以获取在域中处理密码更改事件时使用的最新禁止密码列表。

选择一台或多台服务器以承载 Azure AD 密码保护代理服务。 以下注意事项适用于服务器：

* 每个此类服务只能提供单个林的密码策略。 主计算机必须加入到该林的域中。 同时支持根域和子域。 在林的每个域中至少有一个 DC 与密码保护计算机之间需要网络连接。
* 你可以在域控制器上运行 Azure AD 密码保护代理服务，以便进行测试，但该域控制器需要建立 internet 连接。 此连接可能是一个安全问题。 建议仅对此配置进行测试。
* 建议至少将两个 Azure AD 密码保护代理服务器用于冗余，如前一节中的[高可用性注意事项](#high-availability-considerations)中所述。
* 不支持在只读域控制器上运行 Azure AD 密码保护代理服务。

若要安装 Azure AD 密码保护代理服务，请完成以下步骤：

1. 若要安装 Azure AD 密码保护代理服务，请运行 `AzureADPasswordProtectionProxySetup.exe` 软件安装程序。

    软件安装不需要重新启动，并可以使用标准 MSI 过程自动完成，如以下示例所示：
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > 在安装 `AzureADPasswordProtectionProxySetup.exe` 包之前，必须运行 Windows 防火墙服务，以避免安装错误。
    >
    > 如果将 Windows 防火墙配置为不运行，则解决方法是在安装过程中暂时启用并运行防火墙服务。 安装后，代理软件在 Windows 防火墙上没有特定的依赖关系。
    >
    > 如果使用的是第三方防火墙，还必须将其配置为满足部署要求。 其中包括允许对端口135和代理 RPC 服务器端口进行入站访问。 有关详细信息，请参阅上一节的[部署要求](#deployment-requirements)。

1. Azure AD 密码保护代理软件包含新的 PowerShell 模块 `AzureADPasswordProtection`。 以下步骤运行此 PowerShell 模块中的各种 cmdlet。

    若要使用此模块，请以管理员身份打开 PowerShell 窗口并按如下所示导入新模块：
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. 若要检查 Azure AD 密码保护代理服务是否正在运行，请使用以下 PowerShell 命令：

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    结果应显示 "*正在运行*"**状态**。

1. 代理服务正在计算机上运行，但没有凭据与 Azure AD 通信。 使用 `Register-AzureADPasswordProtectionProxy` cmdlet 向 Azure AD 注册 Azure AD 密码保护代理服务器。

    此 cmdlet 需要 Azure 租户的全局管理员凭据。 还需要在目录林根级域中本地 Active Directory 域管理员权限。 还必须使用具有本地管理员权限的帐户运行此 cmdlet：

    为 Azure AD 密码保护代理服务成功执行此命令后，该命令的其他调用会成功，但这是不必要的。

    `Register-AzureADPasswordProtectionProxy` cmdlet 支持以下三种身份验证模式。 前两种模式支持 Azure 多重身份验证，但第三种模式不支持。

    > [!TIP]
    > 在第一次为特定 Azure 租户运行此 cmdlet 时，可能会出现明显的延迟。 除非报告失败，否则不必担心这一延迟。

     * 交互式身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式在 Server Core 操作系统上不起作用。 相反，请使用以下身份验证模式之一。 此外，如果启用了 Internet Explorer 增强的安全配置，则此模式可能会失败。 解决方法是禁用该配置，注册代理，然后重新启用它。

     * 设备代码身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        出现提示时，请在链接之后打开 web 浏览器并输入身份验证代码。

     * 无提示（基于密码）身份验证模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果你的帐户需要 Azure 多重身份验证，此模式将会失败。 在这种情况下，请使用上述两种身份验证模式之一，或者使用不需要 MFA 的其他帐户。
        >
        > 如果 Azure 设备注册（在 Azure AD 密码保护的涵盖下使用）已配置为全局需要 MFA，则还可能会看到所需的 MFA。 若要解决这一要求，你可以使用一个不同的帐户，该帐户支持使用之前两种身份验证模式之一进行 MFA，或者你也可以暂时放宽 Azure 设备注册 MFA 要求。
        >
        > 若要进行此更改，请搜索并选择 Azure 门户中的**Azure Active Directory** ，然后选择 "**设备" > 设备设置**"。 设置 "**需要多重身份验证" 将设备加入**到 "*否*"。 完成注册后，请确保将此设置重新配置为 *"是"* 。
        >
        > 建议绕过 MFA 要求，仅用于测试目的。

    目前不需要指定 *-ForestCredential*参数，该参数保留供将来使用。

    只需在服务的生存期内注册 Azure AD 密码保护代理服务。 然后，Azure AD 密码保护代理服务将自动执行任何其他必要的维护。

1. 现在使用 `Register-AzureADPasswordProtectionForest` PowerShell cmdlet 向本地 Active Directory 林中注册必要的凭据，以便与 Azure 进行通信。

    > [!NOTE]
    > 如果在你的环境中安装了多个 Azure AD 密码保护代理服务器，则使用哪个代理服务器注册林并不重要。

    Cmdlet 需要 Azure 租户的全局管理员凭据。 还必须使用具有本地管理员权限的帐户运行此 cmdlet。 它还需要本地 Active Directory 企业管理员权限。 针对每个林运行此步骤一次。

    `Register-AzureADPasswordProtectionForest` cmdlet 支持以下三种身份验证模式。 前两种模式支持 Azure 多重身份验证，但第三种模式不支持。

    > [!TIP]
    > 在第一次为特定 Azure 租户运行此 cmdlet 时，可能会出现明显的延迟。 除非报告失败，否则不必担心这一延迟。

     * 交互式身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式在 Server Core 操作系统上不起作用。 改为使用以下两种身份验证模式之一。 此外，如果启用了 Internet Explorer 增强的安全配置，则此模式可能会失败。 解决方法是禁用该配置，注册林，然后重新启用它。  

     * 设备代码身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        出现提示时，请在链接之后打开 web 浏览器并输入身份验证代码。

     * 无提示（基于密码）身份验证模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果你的帐户需要 Azure 多重身份验证，此模式将会失败。 在这种情况下，请使用上述两种身份验证模式之一，或者使用不需要 MFA 的其他帐户。
        >
        > 如果 Azure 设备注册（在 Azure AD 密码保护的涵盖下使用）已配置为全局需要 MFA，则还可能会看到所需的 MFA。 若要解决这一要求，你可以使用一个不同的帐户，该帐户支持使用之前两种身份验证模式之一进行 MFA，或者你也可以暂时放宽 Azure 设备注册 MFA 要求。
        >
        > 若要进行此更改，请搜索并选择 Azure 门户中的**Azure Active Directory** ，然后选择 "**设备" > 设备设置**"。 设置 "**需要多重身份验证" 将设备加入**到 "*否*"。 完成注册后，请确保将此设置重新配置为 *"是"* 。
        >
        > 建议绕过 MFA 要求，仅用于测试目的。

       仅当当前已登录用户也是根域的 Active Directory 域管理员时，这些示例才会成功。 如果不是这种情况，可以通过 *-ForestCredential*参数提供备用的域凭据。

    在林的生存期内，只需注册 Active Directory 林一次。 之后，林中的 Azure AD 密码保护 DC 代理会自动执行任何其他必要的维护。 为林成功运行 `Register-AzureADPasswordProtectionForest` 后，cmdlet 的其他调用将会成功，但这是不必要的。
    
    若要成功 `Register-AzureADPasswordProtectionForest`，必须在 Azure AD 密码保护代理服务器的域中至少有一个运行 Windows Server 2012 或更高版本的 DC。 在执行此步骤之前，无需在任何域控制器上安装 Azure AD 密码保护 DC 代理软件。

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>将代理服务配置为通过 HTTP 代理进行通信

如果你的环境需要使用特定的 HTTP 代理来与 Azure 通信，请使用以下步骤来配置 Azure AD 密码保护服务。

在 `%ProgramFiles%\Azure AD Password Protection Proxy\Service` 文件夹中创建*AzureADPasswordProtectionProxy*文件。 包括以下内容：

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

如果 HTTP 代理要求身份验证，请添加*useDefaultCredentials*标记：

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

在这两种情况下，请将 `http://yourhttpproxy.com:8080` 替换为特定 HTTP 代理服务器的地址和端口。

如果你的 HTTP 代理配置为使用授权策略，则必须向承载用于密码保护的代理服务的计算机的 Active Directory 计算机帐户授予访问权限。

建议你在创建或更新*AzureADPasswordProtectionProxy*文件后，停止并重新启动 Azure AD 密码保护代理服务。

代理服务不支持使用特定凭据连接到 HTTP 代理。

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>将代理服务配置为侦听特定端口

Azure AD 密码保护 DC 代理软件使用 RPC over TCP 与代理服务通信。 默认情况下，Azure AD 密码保护代理服务会在任何可用的动态 RPC 终结点上进行侦听。 可以将服务配置为在特定 TCP 端口上侦听，如有必要，因为网络拓扑或环境中的防火墙要求。

<a id="static" /></a>若要将服务配置为在静态端口下运行，请使用 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet，如下所示：

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> 要使这些更改生效，你必须停止并重新启动 Azure AD 密码保护代理服务。

若要将服务配置为在动态端口下运行，请使用相同的过程，但将*StaticPort*设置回零：

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> 要使这些更改生效，你必须停止并重新启动 Azure AD 密码保护代理服务。

在端口配置发生更改后，Azure AD 密码保护代理服务需要手动重启。 进行这些配置更改后，无需在域控制器上重新启动 Azure AD 密码保护 DC 代理服务。

若要查询服务的当前配置，请使用 `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet，如以下示例中所示

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

以下示例输出显示 Azure AD 密码保护代理服务正在使用动态端口：

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>安装 DC 代理服务

若要安装 Azure AD 密码保护 DC 代理服务，请运行 `AzureADPasswordProtectionDCAgentSetup.msi` 包。

你可以使用标准 MSI 过程来自动完成软件安装，如以下示例中所示：

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

如果希望让安装程序自动重新启动计算机，则可以省略 `/norestart` 标志。

软件安装或卸载需要重新启动。 此要求是因为密码筛选器 Dll 仅通过重新启动加载或卸载。

在域控制器上安装了 DC 代理软件并重新启动该计算机后，本地 Azure AD 密码保护的安装已完成。 无需（也无法）进行其他配置。 针对本地 Dc 的密码更改事件使用 Azure AD 的已配置禁止密码列表。

若要从 Azure 门户启用本地 Azure AD 密码保护或配置自定义的禁止密码，请参阅[启用本地 Azure AD 密码保护](howto-password-ban-bad-on-premises-operations.md)。

> [!TIP]
> 你可以在尚未成为域控制器的计算机上安装 Azure AD 密码保护 DC 代理。 在这种情况下，服务将启动并运行，但在将计算机提升为域控制器之前会保持不活动状态。

## <a name="upgrading-the-proxy-service"></a>升级代理服务

Azure AD Password Protection proxy service 支持自动升级。 自动升级使用与代理服务并行安装的 Microsoft Azure AD 连接代理更新程序服务。 自动升级默认情况下启用，并且可以使用 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet 启用或禁用。

可以使用 `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet 查询当前设置。 建议始终启用自动升级设置。

`Get-AzureADPasswordProtectionProxy` cmdlet 可用于查询林中所有当前已安装 Azure AD 密码保护代理服务器的软件版本。

### <a name="manual-upgrade-process"></a>手动升级过程

手动升级是通过运行 `AzureADPasswordProtectionProxySetup.exe` 软件安装程序的最新版本来完成的。 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)提供了该软件的最新版本。

不需要卸载 Azure AD 密码保护代理服务的当前版本-安装程序执行就地升级。 升级代理服务时，不需要重新启动。 可以使用标准 MSI 过程自动执行软件升级，如 `AzureADPasswordProtectionProxySetup.exe /quiet`。

## <a name="upgrading-the-dc-agent"></a>升级 DC 代理

如果有较新版本的 Azure AD 密码保护 DC 代理软件，则可通过运行 `AzureADPasswordProtectionDCAgentSetup.msi` 软件包的最新版本来完成升级。 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)提供了该软件的最新版本。

不需要卸载最新版本的 DC 代理软件，安装程序将执行就地升级。 升级 DC 代理软件时，始终需要重新启动-此要求由核心 Windows 行为引起。

可以使用标准 MSI 过程自动执行软件升级，如 `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`。

如果希望让安装程序自动重新启动计算机，则可以省略 `/norestart` 标志。

`Get-AzureADPasswordProtectionDCAgent` cmdlet 可用于查询林中所有当前已安装 Azure AD 密码保护 DC 代理的软件版本。

## <a name="next-steps"></a>后续步骤

现在，你已安装了在本地服务器上 Azure AD 密码保护所需的服务，请在[Azure 门户中启用本地 Azure AD 密码保护](howto-password-ban-bad-on-premises-operations.md)，以完成部署。
