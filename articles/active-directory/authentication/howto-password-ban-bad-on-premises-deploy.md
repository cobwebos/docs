---
title: 部署 Azure AD 密码保护-Azure Active Directory
description: 部署 Azure AD 密码保护，以禁止本地错误密码
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e2328bcd2b2d9fe957df82c46730091ffdf9366
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474291"
---
# <a name="deploy-azure-ad-password-protection"></a>部署 Azure AD 密码保护

现在，你已了解[如何为 Windows Server Active Directory Azure AD 强制密码保护](concept-password-ban-bad-on-premises.md)，下一步是规划和执行部署。

## <a name="deployment-strategy"></a>部署策略

建议您在审核模式下启动部署。 审核模式是默认初始设置，可以继续设置密码。 将被阻止的密码记录在事件日志中。 在审核模式下部署代理服务器和 DC 代理后，应监视在强制执行策略时密码策略对用户和环境的影响。

在审核阶段，许多组织发现：

* 需要改进现有的操作过程，以使用更安全的密码。
* 用户经常使用不安全的密码。
* 他们需要通知用户即将执行的安全强制更改、可能对这些更改的影响，以及如何选择更安全的密码。

还可以通过更强的密码验证来影响现有 Active Directory 域控制器部署自动化。 建议在审核期评估期间至少发生一次 DC 升级和一个 DC 降级，以帮助提前发现此类问题。  有关详细信息，请参阅：

* [Ntdsutil 无法设置弱目录服务修复模式密码](howto-password-ban-bad-on-premises-troubleshoot.md##ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [由于目录服务修复模式密码弱，域控制器副本升级失败](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [由于本地管理员密码弱，域控制器降级失败](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

在一个合理的时间段内，在审核模式下运行此功能后，可以将该配置从*审核*切换为 "*强制执行*"，以要求更安全的密码。 在此期间最好是执行有针对性的监视。

## <a name="deployment-requirements"></a>部署要求

* 可在本文[消除组织中的错误密码](concept-password-ban-bad.md#license-requirements)一文中找到 Azure AD 密码保护的许可要求。
* 将安装 Azure AD 密码保护 DC 代理软件的所有计算机都必须运行 Windows Server 2012 或更高版本。 此要求并不意味着 Active Directory 域或林还必须位于 Windows Server 2012 域或林功能级别。 如[设计原则](concept-password-ban-bad-on-premises.md#design-principles)中所述，要运行 DC 代理或代理软件，不需要最低 DFL 或 FFL。
* 安装了 DC 代理服务的所有计算机都必须安装 .NET 4.5。
* 将安装 Azure AD 密码保护代理服务的所有计算机都必须运行 Windows Server 2012 R2 或更高版本。
   > [!NOTE]
   > 代理服务部署是部署 Azure AD 密码保护的必需要求，即使域控制器可能有出站 internet 连接。 
   >
* 将安装 Azure AD 密码保护代理服务的所有计算机必须安装了 .NET 4.7。
  .NET 4.7 应已安装在完全更新的 Windows 服务器上。 如有必要，请下载并运行在[Windows .NET Framework 4.7 脱机安装程序](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)中找到的安装程序。
* 所有安装了 Azure AD password protection 组件的计算机都必须安装有通用 C 运行时。 可以通过确保所有更新都 Windows 更新来获取运行时。 也可以在特定于 OS 的更新包中获取它。 有关详细信息，请参阅[Windows 中的通用 C 运行时更新](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)。
* 每个域中至少一个域控制器之间必须存在网络连接，并且至少有一台服务器承载了密码保护的代理服务。 此连接必须允许域控制器访问 RPC 终结点映射器端口135和代理服务上的 RPC 服务器端口。 默认情况下，RPC 服务器端口是动态 RPC 端口，但可以配置为[使用静态端口](#static)。
* 将安装 Azure AD 密码保护代理服务的所有计算机必须具有对以下终结点的网络访问权限：

    |**终结点**|**用途**|
    | --- | --- |
    |`https://login.microsoftonline.com`|身份验证请求|
    |`https://enterpriseregistration.windows.net`|Azure AD 密码保护功能|
 
* Microsoft Azure AD 连接代理更新程序先决条件

  Microsoft Azure AD Connect Agent 更新程序服务与 Azure AD 密码保护代理服务并行安装。 需要额外的配置才能使 Microsoft Azure AD Connect Agent 更新服务能够正常工作：

  如果你的环境使用 http 代理服务器，则必须遵循[使用现有本地代理服务器](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)中所指定的准则。

  Microsoft Azure AD 连接代理更新程序服务还需要[tls 要求](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements)中指定的 tls 1.2 步骤。

  必须为[应用程序代理环境设置过程](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)中指定的端口和 url 集启用网络访问。

  > [!WARNING]
  > Azure AD 密码保护代理和应用程序代理安装不同版本的 Microsoft Azure AD 连接代理更新程序服务，这就是说明如何引用应用程序代理内容的原因。 并行安装时这些不同版本不兼容，因此不建议在同一台计算机上并行安装 Azure AD 密码保护代理和应用程序代理。

* 为密码保护承载代理服务的所有计算机必须配置为允许域控制器登录到代理服务。 此功能通过 "从网络访问此计算机" 权限分配来控制。
* 承载密码保护的代理服务的所有计算机必须配置为允许出站 TLS 1.2 HTTP 流量。
* 一个全局管理员帐户，用于注册包含 Azure AD 的密码保护和林的代理服务。
* 在目录林根级域中具有 Active Directory 域管理员权限的帐户，用于向 Azure AD 注册 Windows Server Active Directory 林。
* 运行 DC 代理服务软件的任何 Active Directory 域必须使用分布式文件系统复制（DFSR）进行 sysvol 复制。

  如果你的域尚未使用 DFSR，则必须先将其迁移到使用 DFSR，然后才能安装 Azure AD 密码保护。 有关详细信息，请参阅以下链接：

  [SYSVOL 复制迁移指南： FRS 到 DFS 复制](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

  > [!WARNING]
  > Azure AD 密码保护 DC 代理软件当前将安装在仍在使用 FRS 的域控制器（用于 DFSR 的前置技术）中进行 sysvol 复制，但该软件在此环境中将无法正常运行。 其他负面影响包括未能复制的单个文件，sysvol 还原过程会成功，但不会以静默方式复制所有文件。 应该尽快迁移你的域以使用 DFSR，这两种方法都适用于 DFSR 固有的优势，还会取消阻止 Azure AD 密码保护的部署。 在仍使用 FRS 的域中运行时，软件的未来版本将自动禁用。

* 必须在运行 Windows Server 2012 的域中的所有域控制器上启用密钥分发服务。 默认情况下，此服务通过手动触发器启动启用。

## <a name="single-forest-deployment"></a>单林部署

下图显示 Azure AD 密码保护的基本组件如何在本地 Active Directory 环境中协同工作。

![Azure AD 密码保护组件如何配合工作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

在部署软件之前，最好先查看软件的工作方式。 请参阅[Azure AD 密码保护的概念性概述](concept-password-ban-bad-on-premises.md)。

### <a name="download-the-software"></a>下载软件

Azure AD 密码保护有两个必需的安装程序。 可从[Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)获得。

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>安装和配置用于密码保护的代理服务

1. 选择一台或多台服务器来托管密码保护的代理服务。
   * 每个此类服务只能提供单个林的密码策略。 主计算机必须加入到该林的域中。 同时支持根域和子域。 在林的每个域中至少有一个 DC 与密码保护计算机之间需要网络连接。
   * 您可以在域控制器上运行代理服务，以便进行测试。 但该域控制器需要建立 internet 连接，这可能是一个安全问题。 建议仅对此配置进行测试。
   * 为实现冗余，建议至少有两个代理服务器。 请参阅[高可用性](howto-password-ban-bad-on-premises-deploy.md#high-availability)。

1. 使用 `AzureADPasswordProtectionProxySetup.exe` 软件安装程序安装 Azure AD 密码保护代理服务。
   * 安装该软件不需要重新启动。 可以使用标准的 MSI 过程将软件安装自动化，例如：

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > 安装 AzureADPasswordProtectionProxySetup 包之前，必须运行 Windows 防火墙服务，以避免安装错误。 如果将 Windows 防火墙配置为不运行，则解决方法是在安装过程中暂时启用并运行防火墙服务。 安装后，代理软件在 Windows 防火墙上没有特定的依赖关系。 如果使用的是第三方防火墙，还必须将其配置为满足部署要求。 其中包括允许对端口135和代理 RPC 服务器端口进行入站访问。 请参阅[部署要求](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)。

1. 以管理员身份打开 PowerShell 窗口。
   * 密码保护代理软件包含新的 PowerShell 模块*AzureADPasswordProtection*。 以下步骤运行此 PowerShell 模块中的各种 cmdlet。 导入新模块，如下所示：

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * 若要检查服务是否正在运行，请使用以下 PowerShell 命令：

      `Get-Service AzureADPasswordProtectionProxy | fl`。

     结果应显示 "正在运行"**状态**。

1. 注册代理。
   * 步骤3完成后，代理服务将在计算机上运行。 但该服务还没有必要的凭据与 Azure AD 进行通信。 需要注册到 Azure AD：

     `Register-AzureADPasswordProtectionProxy`

     此 cmdlet 需要 Azure 租户的全局管理员凭据。 还需要在目录林根级域中本地 Active Directory 域管理员权限。 在此命令对代理服务成功执行一次后，对它的其他调用将成功，但没有必要。

      `Register-AzureADPasswordProtectionProxy` cmdlet 支持以下三种身份验证模式。

     * 交互式身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式在 Server Core 操作系统上不起作用。 相反，请使用以下身份验证模式之一。 此外，如果启用了 Internet Explorer 增强的安全配置，则此模式可能会失败。 解决方法是禁用该配置，注册代理，然后重新启用它。

     * 设备代码身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        然后，按照其他设备上显示的说明完成身份验证。

     * 无提示（基于密码）身份验证模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果你的帐户需要 Azure 多重身份验证，此模式将会失败。 在这种情况下，请使用上述两种身份验证模式之一，或者使用不需要 MFA 的其他帐户。
        >
        > 如果 Azure 设备注册（在 Azure AD 密码保护的涵盖下使用）已配置为全局需要 MFA，则还可能会看到所需的 MFA。 若要解决此问题，你可以使用一个不同的帐户，该帐户支持使用之前两种身份验证模式之一进行 MFA，或者你也可以暂时放宽 Azure 设备注册 MFA 要求。 若要执行此操作，请在 Azure 管理门户中，依次前往 "Azure Active Directory"、"设备" 和 "设备设置"，然后将 "需要多重身份验证加入设备" 设置为 "否"。 完成注册后，请确保将此设置重新配置为 "是"。
        >
        > 建议绕过 MFA 要求，仅用于测试目的。

       目前不需要指定 *-ForestCredential*参数，该参数保留供将来使用。

   在服务的生存期内，只需为密码保护注册代理服务一次。 之后，代理服务将自动执行任何其他必要的维护。

   > [!TIP]
   > 在第一次为特定 Azure 租户运行此 cmdlet 时，可能会出现明显的延迟。 除非报告失败，否则不必担心这一延迟。

1. 注册林。
   * 必须使用 `Register-AzureADPasswordProtectionForest` PowerShell cmdlet 来初始化本地 Active Directory 林，其中包含所需的凭据才能与 Azure 进行通信。 Cmdlet 需要 Azure 租户的全局管理员凭据。 它还需要本地 Active Directory 企业管理员权限。 针对每个林运行此步骤一次。

      `Register-AzureADPasswordProtectionForest` cmdlet 支持以下三种身份验证模式。

     * 交互式身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式在 Server Core 操作系统上不起作用。 改为使用以下两种身份验证模式之一。 此外，如果启用了 Internet Explorer 增强的安全配置，则此模式可能会失败。 解决方法是禁用该配置，注册林，然后重新启用它。  

     * 设备代码身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        然后，按照其他设备上显示的说明完成身份验证。

     * 无提示（基于密码）身份验证模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果你的帐户需要 Azure 多重身份验证，此模式将会失败。 在这种情况下，请使用上述两种身份验证模式之一，或者使用不需要 MFA 的其他帐户。
        >
        > 如果 Azure 设备注册（在 Azure AD 密码保护的涵盖下使用）已配置为全局需要 MFA，则还可能会看到所需的 MFA。 若要解决此问题，你可以使用一个不同的帐户，该帐户支持使用之前两种身份验证模式之一进行 MFA，或者你也可以暂时放宽 Azure 设备注册 MFA 要求。 若要执行此操作，请在 Azure 管理门户中，依次前往 "Azure Active Directory"、"设备" 和 "设备设置"，然后将 "需要多重身份验证加入设备" 设置为 "否"。 完成注册后，请确保将此设置重新配置为 "是"。
        >
        > 建议绕过 MFA 要求，仅用于测试目的。

       仅当当前已登录用户也是根域的 Active Directory 域管理员时，这些示例才会成功。 如果不是这种情况，可以通过 *-ForestCredential*参数提供备用的域凭据。

   > [!NOTE]
   > 如果你的环境中安装了多个代理服务器，则使用哪个代理服务器注册林并不重要。
   >
   > [!TIP]
   > 在第一次为特定 Azure 租户运行此 cmdlet 时，可能会出现明显的延迟。 除非报告失败，否则不必担心这一延迟。

   在林的生存期内，只需注册 Active Directory 林一次。 之后，林中的域控制器代理将自动执行任何其他必要的维护。 为林成功运行 `Register-AzureADPasswordProtectionForest` 后，cmdlet 的其他调用会成功，但不必要。

   要使 `Register-AzureADPasswordProtectionForest` 成功，代理服务器的域中必须至少有一个运行 Windows Server 2012 或更高版本的域控制器。 在执行此步骤之前，无需在任何域控制器上安装 DC 代理软件。

1. 将用于密码保护的代理服务配置为通过 HTTP 代理进行通信。

   如果你的环境需要使用特定的 HTTP 代理来与 Azure 通信，请使用此方法：在%ProgramFiles%\Azure AD Password Protection Proxy\Service 文件夹中创建*AzureADPasswordProtectionProxy*文件。 包括以下内容：

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

   建议您在创建或更新*AzureADPasswordProtectionProxy*文件后停止并重新启动代理服务。

   代理服务不支持使用特定凭据连接到 HTTP 代理。

1. 可选：将代理服务配置为使用密码保护来侦听特定端口。
   * 域控制器上的用于密码保护的 DC 代理软件使用 TCP 上的 RPC 来与代理服务通信。 默认情况下，代理服务会侦听任何可用的动态 RPC 终结点。 但是，你可以将服务配置为侦听特定的 TCP 端口，前提是由于你的环境中的网络拓扑或防火墙要求而必须这样做。
      * <a id="static" /></a>将服务配置为在静态端口下运行，请使用 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet。

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > 必须停止并重启服务，才能让这些更改生效。

      * 若要将服务配置为在动态端口下运行，请使用相同的过程，但将*StaticPort*设置回零：

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > 必须停止并重启服务，才能让这些更改生效。

   > [!NOTE]
   > 密码保护的代理服务需要在端口配置发生更改后手动重启。 但在进行这些配置更改后，你无需在域控制器上重新启动 DC 代理服务软件。

   * 若要查询服务的当前配置，请使用 `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet：

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>安装 DC 代理服务

   使用 `AzureADPasswordProtectionDCAgentSetup.msi` 包为密码保护安装 DC 代理服务。

   软件安装或卸载需要重新启动。 此要求是因为密码筛选器 Dll 仅通过重新启动加载或卸载。

   你可以在尚未成为域控制器的计算机上安装 DC 代理服务。 在这种情况下，该服务将启动并运行，但在计算机升级为域控制器之前将保持不活动状态。

   你可以使用标准 MSI 过程来自动完成软件安装。 例如：

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   如果希望让安装程序自动重新启动计算机，则可以省略 `/norestart` 标志。

在域控制器上安装了 DC 代理软件并重新启动该计算机后，安装完成。 无需（也无法）进行其他配置。

## <a name="upgrading-the-proxy-agent"></a>升级代理程序

当 Azure AD 密码保护代理软件的较新版本可用时，可通过运行 `AzureADPasswordProtectionProxySetup.exe` 软件安装程序的最新版本来完成升级。 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)提供了该软件的最新版本。

不需要卸载当前版本的代理软件，安装程序将执行就地升级。 升级代理软件时，不需要重新启动。 可以使用标准 MSI 过程自动执行软件升级，例如： `AzureADPasswordProtectionProxySetup.exe /quiet`。

代理程序支持自动升级。 自动升级使用与代理服务并行安装的 Microsoft Azure AD 连接代理更新程序服务。 自动升级默认情况下启用，并且可以使用 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet 启用或禁用。 可以使用 `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet 查询当前设置。 Microsoft 建议始终启用自动升级设置。

`Get-AzureADPasswordProtectionProxy` cmdlet 可用于查询林中所有当前已安装的代理代理的软件版本。

## <a name="upgrading-the-dc-agent"></a>升级 DC 代理

如果有较新版本的 Azure AD 密码保护 DC 代理软件，则可通过运行 `AzureADPasswordProtectionDCAgentSetup.msi` 软件包的最新版本来完成升级。 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)提供了该软件的最新版本。

不需要卸载最新版本的 DC 代理软件，安装程序将执行就地升级。 升级 DC 代理软件时，始终需要重新启动-此要求由核心 Windows 行为引起。 

可以使用标准 MSI 过程自动执行软件升级，例如： `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`。

如果希望让安装程序自动重新启动计算机，则可以省略 `/norestart` 标志。

`Get-AzureADPasswordProtectionDCAgent` cmdlet 可用于查询林中所有当前已安装 DC 代理的软件版本。

## <a name="multiple-forest-deployments"></a>多林部署

跨多个林部署 Azure AD 密码保护不需要满足其他要求。 每个林都按 "单林部署" 部分中的说明单独配置。 每个密码保护代理只能支持其加入的林中的域控制器。 任何林中的密码保护软件都不知道在其他林中部署的密码保护软件，而不管 Active Directory 信任配置如何。

## <a name="read-only-domain-controllers"></a>只读域控制器

不会在只读域控制器（Rodc）上处理和保存密码更改/集。 它们将转发给可写的域控制器。 因此，不需要在 Rodc 上安装 DC 代理软件。

## <a name="high-availability"></a>高可用性

当林中的域控制器尝试从 Azure 下载新策略或其他数据时，密码保护的主要可用性是代理服务器的可用性。 每个 DC 代理在决定要调用的代理服务器时使用简单的轮循机制算法。 代理跳过未响应的代理服务器。 对于大多数完全连接的 Active Directory 部署，这些部署具有目录和 sysvol 文件夹状态的正常复制，两个代理服务器足以确保可用性。 这将导致及时下载新策略和其他数据。 但你可以部署其他代理服务器。

DC 代理软件的设计可减轻与高可用性相关的常见问题。 DC 代理维护最近下载的密码策略的本地缓存。 即使所有已注册的代理服务器都变为不可用，DC 代理仍会继续强制执行其缓存的密码策略。 大型部署中的密码策略合理的更新频率通常是天，而不是小时或更短时间。 因此，代理服务器的短暂中断不会显著影响 Azure AD 密码保护。

## <a name="next-steps"></a>后续步骤

现在，你已安装了在本地服务器上 Azure AD 密码保护所需的服务，接下来请[执行安装后配置并收集报表信息](howto-password-ban-bad-on-premises-operations.md)以完成部署。

[Azure AD 密码保护的概念性概述](concept-password-ban-bad-on-premises.md)
