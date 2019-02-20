---
title: 部署 Azure AD 密码保护预览版
description: 部署 Azure AD 密码保护预览版以禁止错误的本地密码
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
ms.openlocfilehash: 0a4a4b760652ce38e27e12e9eb73fbe7692eddbc
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204366"
---
# <a name="preview-deploy-azure-ad-password-protection"></a>预览版：部署 Azure AD 密码保护

|     |
| --- |
| Azure AD 密码保护是 Azure Active Directory 的一项公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

了解[如何为 Windows Server Active Directory 实施 Azure AD 密码保护](concept-password-ban-bad-on-premises.md)后，下一步是规划和执行部署。

## <a name="deployment-strategy"></a>部署策略

Microsoft 建议在审核模式下开始任何部署。 审核模式是默认的初始设置，在其中可以继续设置密码，如果阻止设置密码，会在事件日志中创建条目。 在审核模式下完全部署代理服务器和 DC 代理之后，应执行定期监视，以确定在实施密码策略后，对用户和环境造成了哪些影响。

在审核阶段，许多组织发现：

* 需要改进现有的操作过程，以使用更安全的密码。
* 用户经常选择不安全的密码。
* 需要告知用户即将对安全措施做出的更改以及这些更改对用户造成的影响，并帮助用户更好地了解如何选择更安全的密码。

在审核模式下运行该功能一段合理的时间后，可将实施配置从“审核”转换为“实施”，因而需要更安全的密码。 在此期间最好是执行有针对性的监视。

## <a name="deployment-requirements"></a>部署要求

* 将安装 Azure AD 密码保护 DC 代理服务的所有域控制器都必须运行 Windows Server 2012 或更高版本。
* 将安装 Azure AD 密码保护代理服务器服务的所有计算机都必须运行 Windows Server 2012 R2 或更高版本。
* 安装了 Azure AD 密码保护组件的所有计算机（包括域控制器）都必须安装 Universal C 运行时。
这最好通过使用 Windows 更新完全修补计算机来完成。 否则，可以安装合适的特定于 OS 的更新包 - 请参阅[适用于 Windows 中的 Universal C 运行时的更新](https://support.microsoft.com/help/2999226/update-for-universal-c-runtime-in-windows)。
* 在每个域中的至少一台域控制器和托管 Azure AD 密码保护代理服务的至少一台服务器之间必须存在网络连接。 此连接必须允许域控制器访问代理服务器服务上的 RPC 终结点映射器端口 (135) 和 RPC 服务器端口。 RPC 服务器端口默认情况下是一个动态 RPC 端口，但可以将其配置为使用静态端口（请参阅下文）。
* 托管 Azure AD 密码保护代理服务的所有计算机必须具有对以下终结点的网络访问权限：

    |终结点 |目的|
    | --- | --- |
    |`https://login.microsoftonline.com`|身份验证请求|
    |`https://enterpriseregistration.windows.net`|Azure AD 密码保护功能|

* 拥有全局管理员帐户，以用来向 Azure AD 注册 Azure AD 密码保护代理服务器服务和林。
* 拥有在林根域中具有 Active Directory 域管理员权限的帐户，以用来向 Azure AD 注册 Windows Server Active Directory 林。
* 运行 DC 代理服务软件的任何 Active Directory 域都必须使用 DFSR 进行 sysvol 复制。

## <a name="single-forest-deployment"></a>单林部署

下图显示了 Azure AD 密码保护的基本组件如何在本地 Active Directory 环境中配合工作。  

![Azure AD 密码保护组件如何配合工作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

### <a name="download-the-software"></a>下载软件

可从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=57071)下载 Azure AD 密码保护的两个必需安装程序

### <a name="install-and-configure-the-azure-ad-password-protection-proxy-service"></a>安装和配置 Azure AD 密码保护代理服务

1. 选择用于托管 Azure AD 密码保护代理服务的一个或多个服务器。
   * 每个此类服务只能为单个林提供密码策略，主机必须已加入该林中的某个域（根域和子域均受支持）。 为使 Azure AD 密码保护代理服务发挥其作用，必须在林的每个域中的至少一个 DC，与 Azure AD 密码保护代理计算机之间建立网络连接。
   * 支持在域控制器上安装并运行 Azure AD 密码保护代理服务用于测试；缺点是域控制器需要建立 Internet 连接，这可能会造成安全问题。 Microsoft 建议仅将此配置用于测试目的。
   * 建议至少配置两个代理服务器来实现冗余。 参阅[高可用性](howto-password-ban-bad-on-premises-deploy.md#high-availability)

2. 使用 AzureADPasswordProtectionProxySetup.msi MSI 包安装 Azure AD 密码保护代理服务。
   * 安装该软件不需要重新启动。 可以使用标准的 MSI 过程将软件安装自动化，例如：`msiexec.exe /i AzureADPasswordProtectionProxySetup.msi /quiet /qn`

      > [!NOTE]
      > 在安装 AzureADPasswordProtectionProxySetup.msi MSI 包之前，Windows 防火墙服务必须正在运行，否则将发生安装错误。 如果 Windows 防火墙配置为不运行，则解决方法是在安装过程中暂时启用并启动 Windows 防火墙服务。 代理软件在安装后对 Windows 防火墙软件没有特定的依赖关系。 如果使用的是第三方防火墙，则还必须对其进行配置以满足部署要求（允许对端口 135 和代理 RPC 服务器端口（无论是动态的还是静态的）进行入站访问）。 [请参阅部署要求](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements)

3. 以管理员身份打开 PowerShell 窗口。
   * Azure AD 密码保护代理软件包含名为 AzureADPasswordProtection 的新 PowerShell 模块。 以下步骤假设从 PowerShell 模块运行各个 cmdlet、已打开新的 PowerShell 窗口，并已导入新模块，如下所示：

      ```PowerShell
      Import-Module AzureADPasswordProtection
      ```

   * 使用以下 PowerShell 命令检查服务是否正在运行：`Get-Service AzureADPasswordProtectionProxy | fl`。
     该命令应生成 **Status** 为“Running”的结果。

4. 注册代理。
   * 完成步骤 3 后，Azure AD 密码保护代理服务会在计算机上运行，但尚未获得用来与 Azure AD 通信的凭据。 必须注册到 Azure AD 才能使用 `Register-AzureADPasswordProtectionProxy` PowerShell cmdlet。 该 cmdlet 需要 Azure 租户的全局管理员凭据，以及在本地林根域中的 Active Directory 域管理员特权。 针对给定的代理服务成功运行该 cmdlet 后，对 `Register-AzureADPasswordProtectionProxy` 的其他调用会继续成功，但没有必要。

      Register-AzureADPasswordProtectionProxy cmdlet 支持下面所述的三种不同身份验证模式。

      * 交互式身份验证模式：

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > 此模式不适用于 Server Core 操作系统。 请按如下所述改用一种替代的身份验证模式。

         > [!NOTE]
         > 如果启用了 Internet Explorer 增强的安全性配置，则此模式可能会失败。 解决方法是禁用 IESC，注册代理服务器，然后重新启用 IESC。

      * 设备代码身份验证模式：

         ```PowerShell
         Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         可以遵照不同设备上显示的说明完成身份验证。

      * 无提示（基于密码）身份验证模式：

         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > 如果身份验证出于任何原因需要 MFA，则此模式将会失败。 如果出现这种情况，请使用前面所述的两种模式之一来完成基于 MFA 的身份验证。

      目前不需要指定 -ForestCredential 参数，该参数是为将来的功能保留的。

   > [!NOTE]
   > 在 Azure AD 密码保护代理服务的生存期，预期只需注册该服务一次。 注册后，代理服务会自动执行其他任何必要的维护。 针对给定的代理成功运行该 cmdlet 后，对“Register-AzureADPasswordProtectionProxy”的其他调用会继续成功，但没有必要。

   > [!TIP]
   > 首次针对给定的 Azure 租户运行此 cmdlet 时，在 cmdlet 完成执行之前，可能会出现明显的延迟（数秒）。 除非报告了失败，否则应将这种延迟视为警告。

5. 注册林。
   * 必须运行 `Register-AzureADPasswordProtectionForest` PowerShell cmdlet，使用用来与 Azure 通信的凭据将本地 Active Directory 初始化。 该 cmdlet 需要 Azure 租户的全局管理员凭据，以及在本地林根域中的 Active Directory 域管理员特权。 针对每个林运行此步骤一次。

      Register-AzureADPasswordProtectionForest cmdlet 支持下面所述的三种不同身份验证模式。

      * 交互式身份验证模式：

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
         ```
         > [!NOTE]
         > 此模式不适用于 Server Core 操作系统。 请按如下所述改用一种替代的身份验证模式。

         > [!NOTE]
         > 如果启用了 Internet Explorer 增强的安全性配置，则此模式可能会失败。 解决方法是禁用 IESC，注册代理服务器，然后重新启用 IESC。  

      * 设备代码身份验证模式：

         ```PowerShell
         Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceMode
         To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
         ```

         可以遵照不同设备上显示的说明完成身份验证。

      * 无提示（基于密码）身份验证模式：
         ```PowerShell
         $globalAdminCredentials = Get-Credential
         Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
         ```

         > [!NOTE]
         > 如果身份验证需要 MFA，则此模式将会失败。 如果出现这种情况，请使用前面所述的两种模式之一来完成基于 MFA 的身份验证。

      仅当当前登录的用户也是根域的 Active Directory 域管理员时，上述示例才会成功。 否则，可以通过 -ForestCredential 参数提供替代的域凭据。

   > [!NOTE]
   > 如果环境中安装了多个代理服务器，使用哪个代理服务器注册林并不重要。

   > [!TIP]
   > 首次针对给定的 Azure 租户运行此 cmdlet 时，在 cmdlet 完成执行之前，可能会出现明显的延迟（数秒）。 除非报告了失败，否则应将这种延迟视为警告。

   > [!NOTE]
   > 在 Active Directory 林的生存期内，预期只需注册该林一次。 注册后，林中运行的域控制器代理会自动执行其他任何必要的维护。 针对给定的林成功运行该 cmdlet 后，对 `Register-AzureADPasswordProtectionForest` 的其他调用会继续成功，但没有必要。

   > [!NOTE]
   > 要想使 `Register-AzureADPasswordProtectionForest` 成功，代理服务器的域中至少必须有一台 Windows Server 2012 或更高版本的域控制器可用。 但是，在执行此步骤之前，不需要在任何域控制器上安装 DC 代理软件。

6. 将 Azure AD 密码保护代理服务配置为通过 HTTP 代理通信

   如果环境要求使用特定的 HTTP 代理来与 Azure 通信，可按如下所示实现此目的。

   在 `%ProgramFiles%\Azure AD Password Protection Proxy\Service` 文件夹中创建名为 `proxyservice.exe.config`、包含以下内容的文件：

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

   如果 HTTP 代理需要身份验证，请按如下所示添加 useDefaultCredentials 标记：

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

   在这两种情况下，都需要将 `http://yourhttpproxy.com:8080` 替换为特定 HTTP 代理服务器的地址和端口。

   如果使用授权策略配置了 HTTP 代理，必须向托管 Azure AD 密码保护代理服务的计算机的 Active Directory 计算机帐户授予访问权限。

   创建或更新 `proxyservice.exe.config` 文件后，应停止再重启 Azure AD 密码保护代理服务。

   Azure AD 密码保护代理服务不支持使用特定的凭据连接到 HTTP 代理。

7. 可选：将 Azure AD 密码保护代理服务配置为侦听特定的端口。
   * 域控制器上的 Azure AD 密码保护 DC 代理软件使用“基于 TCP 的 RPC”来与 Azure AD 密码保护代理服务通信。 默认情况下，Azure AD 密码保护代理服务侦听任何可用的动态 RPC 终结点。 出于网络拓扑或防火墙要求，可根据需要将服务配置为侦听特定的 TCP 端口。
      * 若要将服务配置为在静态端口下运行，请使用 `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet。
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > 必须停止并重启服务，才能让这些更改生效。

      * 若要将服务配置为在动态端口下运行，请使用相同的过程，但要将 StaticPort 重新设置为 0，如下所示：
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > 必须停止并重启服务，才能让这些更改生效。

   > [!NOTE]
   > 在端口配置中进行任何更改后，需要手动重启 Azure AD 密码保护代理服务。 进行这种性质的配置更改后，不需要重启域控制器上运行的 DC 代理服务软件。

   * 可以使用 `Get-AzureADPasswordProtectionProxyConfiguration` cmdlet 查询当前的服务配置，如以下示例所示：

      ```PowerShell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-azure-ad-password-protection-dc-agent-service"></a>安装 Azure AD 密码保护 DC 代理服务

   使用 `AzureADPasswordProtectionDCAgent.msi` MSI 包安装 Azure AD 密码保护 DC 代理服务软件

   由于只能在重新启动后加载或卸载密码筛选器 dll（这是一项操作系统要求），在安装和卸载后需要重新启动软件安装。

   支持在不充当域控制器的计算机上安装 DC 代理服务。 在这种情况下，服务将会启动并运行，但在将计算机提升为域控制器之前，服务会一直处于非活动状态。

   可以使用标准的 MSI 过程将软件安装自动化，例如：

   `msiexec.exe /i AzureADPasswordProtectionDCAgent.msi /quiet /qn`

   > [!WARNING]
   > 上述示例 msiexec 命令会导致立即重新启动；指定 `/norestart` 标志可避免重新启动。

将 Azure AD 密码保护 DC 代理软件安装在域控制器上并重新启动后，安装即告完成。 无需（也无法）进行其他配置。

## <a name="multiple-forest-deployments"></a>多林部署

跨多个林部署 Azure AD 密码保护不需要满足其他要求。 根据“单林部署”部分中所述单独配置每个林。 每个 Azure AD 密码保护代理只能支持它所加入到的林中的域控制器。 不管 Active Directory 信任配置如何，给定林中的 Azure AD 密码保护软件都不知道其他林中部署的 Azure AD 密码保护软件。

## <a name="read-only-domain-controllers"></a>只读域控制器

密码更改\设置永远不会在只读域控制器 (RODC) 中处理和保留；这些操作会转发到可写域控制器。 因此，无需在 RODC 上安装 DC 代理软件。

## <a name="high-availability"></a>高可用性

确保 Azure AD 密码保护的高可用性的主要考虑因素在于，当林中的域控制器尝试从 Azure 下载新策略或其他数据时，如何确保代理服务器的可用性。 在确定要调用哪个代理服务器时，每个 DC 代理将使用简单的轮循机制算法，并跳过无响应的代理服务器。 对于大多数完全连接的且复制正常（目录状态和 sysvol 状态正常）的 Active Directory 部署，两 (2) 个代理服务器应足以确保可用性，因此可确保及时下载新策略和其他数据。 可按需部署其他代理服务器。

通过设计 DC 代理软件可以缓解与高可用性相关的常用问题。 DC 代理维护最近下载的密码策略的本地缓存。 即使所有已注册的代理服务器出于任何原因而不可用，DC 代理也仍会实施其缓存的密码策略。 在大型部署中，密码策略的合理更新频率通常是几天，而不是几小时或更短的时间更新一次。 因此，代理服务器的短暂中断不会给 Azure AD 密码保护功能的运行或其安全优势造成严重影响。

## <a name="next-steps"></a>后续步骤

在本地服务器上安装 Azure AD 密码保护所需的服务后，请完成[安装后的配置并收集报告信息](howto-password-ban-bad-on-premises-operations.md)，以完成部署。

[Azure AD 密码保护的概念性概述](concept-password-ban-bad-on-premises.md)
