---
title: 部署本地 Azure AD 密码保护
description: 了解如何在本地活动目录域服务环境中规划和部署 Azure AD 密码保护
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ac9b76dd8d3c950b14f6d7b331f15647427ac89
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652738"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>规划和部署本地 Azure 活动目录密码保护

用户通常创建使用常见本地单词（如学校、运动队或名人）的密码。 这些密码很容易猜测，并且对基于字典的攻击很弱。 要在组织中强制实施强密码，Azure 活动目录 （Azure AD） 密码保护提供了全局和自定义禁止的密码列表。 如果这些禁用密码列表中有匹配项，则密码更改请求将失败。

为了保护本地活动目录域服务 （AD DS） 环境，可以安装和配置 Azure AD 密码保护以使用上置目录 DC。 本文介绍如何在本地环境中安装和注册 Azure AD 密码保护代理服务和 Azure AD 密码保护 DC 代理。

有关 Azure AD 密码保护在本地环境中的工作方式的详细信息，请参阅[如何为 Windows 服务器活动目录强制实施 Azure AD 密码保护](concept-password-ban-bad-on-premises.md)。

## <a name="deployment-strategy"></a>部署策略

下图显示了 Azure AD 密码保护的基本组件如何在本地活动目录环境中协同工作：

![Azure AD 密码保护组件如何配合工作](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

在部署软件之前，最好先查看软件的工作原理。 有关详细信息，请参阅[Azure AD 密码保护的概念概述](concept-password-ban-bad-on-premises.md)。

我们建议您在*审核*模式下开始部署。 审核模式是默认的初始设置，可以继续设置密码。 将被阻止的密码将记录在事件日志中。 在审核模式下部署代理服务器和 DC 代理后，监视密码策略在强制实施策略时对用户的影响。

在审核阶段，许多组织发现以下情况适用：

* 需要改进现有的操作过程，以使用更安全的密码。
* 用户通常使用不安全的密码。
* 他们需要告知用户即将发生的安全强制更改、可能对他们产生的影响以及如何选择更安全的密码。

还可以进行更强大的密码验证来影响现有的 Active Directory 域控制器部署自动化。 我们建议在审计期间评估期间至少进行一次 DC 升级和一次 DC 降级，以帮助发现此类问题。 有关详细信息，请参阅以下文章：

* [Ntdsutil.exe 无法设置弱目录服务修复模式密码](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [域控制器副本升级失败，因为目录服务修复模式密码较弱](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [由于本地管理员密码薄弱，域控制器降级失败](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

在该功能在审核模式下运行一段合理期间后，您可以将配置从*审核*切换到 *"强制"，* 以需要更安全的密码。 在此期间进行额外的监视是一个好主意。

### <a name="multiple-forest-considerations"></a>多种林注意事项

跨多个林部署 Azure AD 密码保护不需要满足其他要求。

每个林都独立配置，如下节所述，用于[部署上部 Azure AD 密码保护](#download-required-software)。 每个 Azure AD 密码保护代理只能支持其联接到的林中的域控制器。

任何林中的 Azure AD 密码保护软件都不知道部署在其他林中的密码保护软件，而不考虑 Active Directory 信任配置。

### <a name="read-only-domain-controller-considerations"></a>只读域控制器注意事项

密码更改或设置事件不会处理和持久化在只读域控制器 （RODCs） 上。 相反，它们被转发到可写域控制器。 您不必在 RODCs 上安装 Azure AD 密码保护 DC 代理软件。

此外，不支持在只读域控制器上运行 Azure AD 密码保护代理服务。

### <a name="high-availability-considerations"></a>高可用性注意事项

密码保护的主要问题是当林中的 DC 尝试从 Azure 下载新策略或其他数据时，Azure AD 密码保护代理服务器的可用性。 每个 Azure AD 密码保护 DC 代理在决定调用哪个代理服务器时都使用简单的循环样式算法。 代理跳过未响应的代理服务器。

对于大多数完全连接的 Active Directory 部署，这些部署具有目录和 sysvol 文件夹状态的正常运行复制，两个 Azure AD 密码保护代理服务器足以确保可用性。 此配置可及时下载新策略和其他数据。 如果需要，可以部署其他 Azure AD 密码保护代理服务器。

Azure AD 密码保护 DC 代理软件的设计可缓解与高可用性相关的常见问题。 Azure AD 密码保护 DC 代理维护最近下载的密码策略的本地缓存。 即使所有已注册的代理服务器都不可用，Azure AD 密码保护 DC 代理仍继续强制执行其缓存的密码策略。

大型部署中密码策略的合理更新频率通常是几天，而不是小时或更少。 因此，代理服务器的短暂中断不会显著影响 Azure AD 密码保护。

## <a name="deployment-requirements"></a>部署要求

有关许可的信息，请参阅[Azure AD 密码保护许可要求](concept-password-ban-bad.md#license-requirements)。

以下核心要求适用：

* 安装了 Azure AD 密码保护组件的所有计算机（包括域控制器）都必须安装通用 C 运行时。
    * 您可以通过确保从 Windows 更新获得所有更新来获取运行时。 或者，您可以在特定于操作系统的更新包中获取它。 有关详细信息，请参阅[Windows 中通用 C 运行时的更新](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)。
* 您需要在林根域中具有活动目录域管理员权限的帐户才能将 Windows 服务器活动目录林与 Azure AD 注册。
* 必须在运行 Windows Server 2012 的域中的所有域控制器上启用密钥分发服务。 默认情况下，此服务是通过手动触发器启动启用的。
* 每个域中至少有一个域控制器和至少一台承载 Azure AD 密码保护代理服务的服务器之间必须存在网络连接。 此连接必须允许域控制器访问 RPC 终结点映射器端口 135 和代理服务上的 RPC 服务器端口。
    * 默认情况下，RPC 服务器端口是动态 RPC 端口，但可以配置为[使用静态端口](#static)。
* 将安装 Azure AD 密码保护代理服务的所有计算机都必须具有对以下终结点的网络访问权限：

    |**端点**|**目的**|
    | --- | --- |
    |`https://login.microsoftonline.com`|身份验证请求|
    |`https://enterpriseregistration.windows.net`|Azure AD 密码保护功能|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD 密码保护 DC 代理

以下要求适用于 Azure AD 密码保护 DC 代理：

* 将安装 Azure AD 密码保护 DC 代理软件的所有计算机都必须运行 Windows Server 2012 或更高版本。
    * 活动目录域或林不需要位于 Windows Server 2012 域功能级别 （DFL） 或林功能级别 （FFL）。 如[设计原则](concept-password-ban-bad-on-premises.md#design-principles)所述，DC 代理或代理软件的运行不需要最低 DFL 或 FFL。
* 运行 Azure AD 密码保护 DC 代理的所有计算机都必须安装 .NET 4.5。
* 运行 Azure AD 密码保护 DC 代理服务的任何活动目录域都必须使用分布式文件系统复制 （DFSR） 进行系统复制。
   * 如果您的域尚未使用 DFSR，则必须在安装 Azure AD 密码保护之前进行迁移。 有关详细信息，请参阅[SYSVOL 复制迁移指南：FRS 到 DFS 复制](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Azure AD 密码保护 DC 代理软件当前将安装在仍在使用 FRS（DFSR 前身技术）进行系统复制的域控制器上，但该软件在此环境中无法正常工作。
    >
    > 其他负面的副作用包括单个文件无法复制，sysvol 还原过程似乎成功，但默默地无法复制所有文件。
    >
    > 迁移域以尽快使用 DFSR，这既是 DFSR 固有的优势，也是为了取消阻止 Azure AD 密码保护的部署。 在仍在使用 FRS 的域中运行时，软件的未来版本将自动禁用。

### <a name="azure-ad-password-protection-proxy-service"></a>Azure AD 密码保护代理服务

以下要求适用于 Azure AD 密码保护代理服务：

* 将安装 Azure AD 密码保护代理服务的所有计算机都必须运行 Windows Server 2012 R2 或更高版本。

    > [!NOTE]
    > Azure AD 密码保护代理服务部署是部署 Azure AD 密码保护的必填项，即使域控制器可能具有出站直接 Internet 连接。

* 将安装 Azure AD 密码保护代理服务的所有计算机都必须安装 .NET 4.7。
    * .NET 4.7 应该已安装在完全更新的 Windows 服务器上。 如有必要，请下载并运行在 Windows[的 .NET 框架 4.7 脱机安装程序中找到的安装程序](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)。
* 必须配置承载 Azure AD 密码保护代理服务的所有计算机，以便授予域控制器登录到代理服务的能力。 此功能通过"从网络访问此计算机"权限分配进行控制。
* 必须将承载 Azure AD 密码保护代理服务的所有计算机配置为允许出站 TLS 1.2 HTTP 流量。
* 全局*管理员*帐户，用于向 Azure AD 注册 Azure AD 密码保护代理服务和林。
* 必须为[应用程序代理环境设置过程](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)中指定的端口和 URL 集启用网络访问。

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>微软 Azure AD 连接代理更新程序先决条件

Microsoft Azure AD 连接代理更新程序服务与 Azure AD 密码保护代理服务并排安装。 需要额外的配置才能使 Microsoft Azure AD 连接代理更新程序服务能够正常工作：

* 如果环境使用 HTTP 代理服务器，请按照"[使用现有本地代理服务器"](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)中指定的准则操作。
* 微软 Azure AD 连接代理更新程序服务还需要[TLS 要求](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements)中指定的 TLS 1.2 步骤。

> [!WARNING]
> Azure AD 密码保护代理和 Azure AD 应用程序代理安装不同版本的 Microsoft Azure AD 连接代理更新程序服务，这就是为什么说明引用应用程序代理内容的原因。 并排安装时，这些不同版本不兼容，因此不建议在同一台计算机上安装 Azure AD 密码保护代理和应用程序代理。

## <a name="download-required-software"></a>下载所需的软件

本地 Azure AD 密码保护部署需要两个安装程序：

* Azure AD 密码保护 DC 代理 （*AzureAD 密码保护 DC 代理.msi*）
* Azure AD 密码保护代理 （*AzureAD 密码保护代理.exe*）

从[微软下载中心](https://www.microsoft.com/download/details.aspx?id=57071)下载两个安装程序。

## <a name="install-and-configure-the-proxy-service"></a>安装和配置代理服务

Azure AD 密码保护代理服务通常位于本地 AD DS 环境中的成员服务器上。 安装后，Azure AD 密码保护代理服务将与 Azure AD 通信，以维护 Azure AD 租户全局和客户禁止的密码列表的副本。

在下一节中，您将 Azure AD 密码保护 DC 代理安装在本地 AD DS 环境中的域控制器上。 这些 DC 代理与代理服务通信，获取最新的禁止密码列表，用于在处理域中的密码更改事件时使用。

选择一个或多个服务器来承载 Azure AD 密码保护代理服务。 以下注意事项适用于服务器：

* 每个此类服务只能为单个林提供密码策略。 主机必须加入该林中的域。 根域和子域都支持。 需要在林的每个域中至少有一个 DC 和密码保护计算机之间的网络连接。
* 您可以在域控制器上运行 Azure AD 密码保护代理服务以进行测试，但该域控制器则需要互联网连接。 此连接可能是一个安全问题。 我们建议仅对此配置进行测试。
* 我们建议至少两台 Azure AD 密码保护代理服务器进行冗余，如上一节关于[高可用性注意事项](#high-availability-considerations)的章节中所述。
* 不支持在只读域控制器上运行 Azure AD 密码保护代理服务。

要安装 Azure AD 密码保护代理服务，请完成以下步骤：

1. 要安装 Azure AD 密码保护代理服务，`AzureADPasswordProtectionProxySetup.exe`请运行软件安装程序。

    软件安装不需要重新启动，并且可以使用标准 MSI 过程进行自动化，如以下示例所示：
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > 在安装软件包之前，Windows`AzureADPasswordProtectionProxySetup.exe`防火墙服务必须运行，以避免安装错误。
    >
    > 如果 Windows 防火墙配置为未运行，解决方法是在安装期间临时启用和运行防火墙服务。 安装后，代理软件对 Windows 防火墙没有特定的依赖关系。
    >
    > 如果您使用的是第三方防火墙，则仍必须将其配置为满足部署要求。 其中包括允许对端口 135 和代理 RPC 服务器端口进行入站访问。 有关详细信息，请参阅上一节关于[部署要求](#deployment-requirements)。

1. Azure AD 密码保护代理软件包括新的 PowerShell`AzureADPasswordProtection`模块。 以下步骤运行此 PowerShell 模块的各种 cmdlet。

    要使用此模块，请使用管理员打开 PowerShell 窗口，然后导入新模块，如下所示：
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. 要检查 Azure AD 密码保护代理服务是否正在运行，请使用以下 PowerShell 命令：

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    结果应显示 *"正在运行***"的状态**。

1. 代理服务在计算机上运行，但没有凭据与 Azure AD 通信。 使用`Register-AzureADPasswordProtectionProxy`cmdlet 将 Azure AD 密码保护代理服务器注册到 Azure AD。

    此 cmdlet 需要 Azure 租户的全局管理员凭据。 您还需要林根域中的本地活动目录域管理员权限。 还必须使用具有本地管理员权限的帐户运行此 cmdlet：

    此命令成功一次后，Azure AD 密码保护代理服务，它的其他调用成功，但没有必要。

    `Register-AzureADPasswordProtectionProxy` cmdlet 支持以下三种身份验证模式。 前两种模式支持 Azure 多重身份验证，但第三种模式不支持。

    > [!TIP]
    > 首次为特定 Azure 租户运行此 cmdlet 时，在完成之前可能会出现明显的延迟。 除非报告失败，否则不要担心此延迟。

     * 交互式身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式在服务器核心操作系统上不起作用。 而是使用以下身份验证模式之一。 此外，如果启用了 Internet 资源管理器增强的安全配置，此模式可能会失败。 解决方法是禁用该配置、注册代理，然后重新启用它。

     * 设备代码身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        当出现提示时，按照链接打开 Web 浏览器并输入身份验证代码。

     * 无提示（基于密码）身份验证模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果帐户需要 Azure 多重身份验证，则此模式将失败。 在这种情况下，请使用前两种身份验证模式之一，或者使用不需要 MFA 的不同帐户。
        >
        > 如果 Azure 设备注册（Azure AD 密码保护在封面下使用）已配置为全局需要 MFA，则可能还会看到所需的 MFA。 要满足此要求，可以使用支持 MFA 与前两种身份验证模式之一之一的不同帐户，也可以暂时放宽 Azure 设备注册 MFA 要求。
        >
        > 要进行此更改，请在 Azure 门户中搜索并选择**Azure 活动目录**，然后选择 **"设备>设备设置**"。 设置**需要多因素身份验证才能将设备联接**到*No*。 注册完成后，请确保将此设置重新配置为 *"是*"。
        >
        > 我们建议仅出于测试目的绕过 MFA 要求。

    您当前不必指定 *-ForestCredential*参数，该参数是为将来的功能保留的。

    在服务的生存期内，只需注册一次 Azure AD 密码保护代理服务。 之后，Azure AD 密码保护代理服务将自动执行任何其他必要的维护。

1. 现在，使用`Register-AzureADPasswordProtectionForest`PowerShell cmdlet 将本地活动目录林注册为与 Azure 通信所需的凭据。

    > [!NOTE]
    > 如果环境中安装了多个 Azure AD 密码保护代理服务器，则使用哪个代理服务器注册林并不重要。

    cmdlet 需要 Azure 租户的全局管理员凭据。 您还必须使用具有本地管理员权限的帐户运行此 cmdlet。 它还需要本地活动目录企业管理员权限。 针对每个林运行此步骤一次。

    `Register-AzureADPasswordProtectionForest` cmdlet 支持以下三种身份验证模式。 前两种模式支持 Azure 多重身份验证，但第三种模式不支持。

    > [!TIP]
    > 首次为特定 Azure 租户运行此 cmdlet 时，在完成之前可能会出现明显的延迟。 除非报告失败，否则不要担心此延迟。

     * 交互式身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > 此模式在服务器核心操作系统上不起作用。 而是使用以下两种身份验证模式之一。 此外，如果启用了 Internet 资源管理器增强的安全配置，此模式可能会失败。 解决方法是禁用该配置、注册林，然后重新启用它。  

     * 设备代码身份验证模式：

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        当出现提示时，按照链接打开 Web 浏览器并输入身份验证代码。

     * 无提示（基于密码）身份验证模式：

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > 如果帐户需要 Azure 多重身份验证，则此模式将失败。 在这种情况下，请使用前两种身份验证模式之一，或者使用不需要 MFA 的不同帐户。
        >
        > 如果 Azure 设备注册（Azure AD 密码保护在封面下使用）已配置为全局需要 MFA，则可能还会看到所需的 MFA。 要满足此要求，可以使用支持 MFA 与前两种身份验证模式之一之一的不同帐户，也可以暂时放宽 Azure 设备注册 MFA 要求。
        >
        > 要进行此更改，请在 Azure 门户中搜索并选择**Azure 活动目录**，然后选择 **"设备>设备设置**"。 设置**需要多因素身份验证才能将设备联接**到*No*。 注册完成后，请确保将此设置重新配置为 *"是*"。
        >
        > 我们建议仅出于测试目的绕过 MFA 要求。

       仅当当前登录用户也是根域的活动目录域管理员时，这些示例才成功。 如果不是这样，您可以通过 *-ForestCredential*参数提供替代域凭据。

    在林的生存期内，活动目录林的注册仅需要一次。 之后，林中的 Azure AD 密码保护 DC 代理将自动执行任何其他必要的维护。 成功`Register-AzureADPasswordProtectionForest`运行林后，cmdlet 的其他调用将成功，但没有必要。
    
    为了`Register-AzureADPasswordProtectionForest`成功，在 Azure AD 密码保护代理服务器的域中必须至少有一个运行 Windows Server 2012 或更高版本的 DC 可用。 在此步骤之前，不需要在任何域控制器上安装 Azure AD 密码保护 DC 代理软件。

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>将代理服务配置为通过 HTTP 代理进行通信

如果环境需要使用特定的 HTTP 代理与 Azure 通信，请使用以下步骤配置 Azure AD 密码保护服务。

在`%ProgramFiles%\Azure AD Password Protection Proxy\Service`文件夹中创建*AzureAD 密码保护代理.exe.config*文件。 包括以下内容：

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

如果您的 HTTP 代理需要身份验证，请添加*使用默认凭据*标记：

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

在这两种情况下，请`http://yourhttpproxy.com:8080`替换为特定 HTTP 代理服务器的地址和端口。

如果您的 HTTP 代理配置为使用授权策略，则必须授予对承载代理服务的计算机的 Active Directory 计算机帐户的访问权限，以便进行密码保护。

我们建议您在创建或更新*AzureAD 密码保护代理文件*后停止并重新启动 Azure AD 密码保护代理服务。

代理服务不支持使用特定凭据连接到 HTTP 代理。

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>配置代理服务以侦听特定端口

Azure AD 密码保护 DC 代理软件使用通过 TCP 的 RPC 与代理服务通信。 默认情况下，Azure AD 密码保护代理服务侦听任何可用的动态 RPC 终结点。 如有必要，由于环境中的网络拓扑或防火墙要求，您可以将服务配置为侦听特定的 TCP 端口。

<a id="static" /></a>要将服务配置为在静态端口下运行，`Set-AzureADPasswordProtectionProxyConfiguration`请使用 cmdlet，如下所示：

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> 您必须停止并重新启动 Azure AD 密码保护代理服务，才能使这些更改生效。

要将服务配置为在动态端口下运行，请使用相同的过程，但将*StaticPort*设置为零：

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> 您必须停止并重新启动 Azure AD 密码保护代理服务，才能使这些更改生效。

Azure AD 密码保护代理服务需要在端口配置发生任何更改后手动重新启动。 进行这些配置更改后，不必在域控制器上重新启动 Azure AD 密码保护 DC 代理服务。

要查询服务的当前配置，`Get-AzureADPasswordProtectionProxyConfiguration`请使用 cmdlet，如以下示例所示

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

要安装 Azure AD 密码保护 DC 代理`AzureADPasswordProtectionDCAgentSetup.msi`服务，请运行包。

您可以使用标准 MSI 过程自动安装软件，如以下示例所示：

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

如果您希望`/norestart`安装程序自动重新启动计算机，则可以省略该标志。

软件安装或卸载需要重新启动。 此要求是因为密码筛选器 DLL 仅通过重新启动加载或卸载。

在域控制器上安装 DC 代理软件并重新启动该计算机后，将完成打开的 Azure AD 密码保护。 无需（也无法）进行其他配置。 针对预置 DC 的密码更改事件使用 Azure AD 中配置的禁止密码列表。

要从 Azure 门户启用预览 Azure AD 密码保护或配置自定义禁止的密码，请参阅[启用本地 Azure AD 密码保护](howto-password-ban-bad-on-premises-operations.md)。

> [!TIP]
> 您可以在尚未成为域控制器的计算机上安装 Azure AD 密码保护 DC 代理。 在这种情况下，服务启动并运行，但在计算机升级为域控制器之前保持非活动状态。

## <a name="upgrading-the-proxy-service"></a>升级代理服务

Azure AD 密码保护代理服务支持自动升级。 自动升级使用 Microsoft Azure AD 连接代理更新程序服务，该服务与代理服务并排安装。 默认情况下，自动升级处于打开状态，并且可能使用`Set-AzureADPasswordProtectionProxyConfiguration`cmdlet 启用或禁用。

可以使用`Get-AzureADPasswordProtectionProxyConfiguration`cmdlet 查询当前设置。 我们建议始终启用自动升级设置。

`Get-AzureADPasswordProtectionProxy` cmdlet 可用于查询林中当前安装的所有 Azure AD 密码保护代理服务器的软件版本。

### <a name="manual-upgrade-process"></a>手动升级过程

通过运行最新版本`AzureADPasswordProtectionProxySetup.exe`的软件安装程序来完成手动升级。 该软件的最新版本可在[微软下载中心](https://www.microsoft.com/download/details.aspx?id=57071)。

卸载 Azure AD 密码保护代理服务的当前版本不需要 - 安装程序执行就地升级。 升级代理服务时，不需要重新启动。 软件升级可以使用标准 MSI 过程（如`AzureADPasswordProtectionProxySetup.exe /quiet`） 自动进行。

## <a name="upgrading-the-dc-agent"></a>升级 DC 代理

当 Azure AD 密码保护 DC 代理软件的较新版本可用时，通过运行最新版本的`AzureADPasswordProtectionDCAgentSetup.msi`软件包来完成升级。 该软件的最新版本可在[微软下载中心](https://www.microsoft.com/download/details.aspx?id=57071)。

卸载 DC 代理软件的当前版本不需要 - 安装程序执行就地升级。 升级 DC 代理软件时，始终需要重新启动 - 此要求是由核心 Windows 行为引起的。

软件升级可以使用标准 MSI 过程（如`msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`） 自动进行。

如果您希望安装程序自动重新启动计算机`/norestart`，则可以省略该标志。

`Get-AzureADPasswordProtectionDCAgent` cmdlet 可用于查询林中当前安装的所有 Azure AD 密码保护 DC 代理的软件版本。

## <a name="next-steps"></a>后续步骤

现在，您已在本地服务器上安装了 Azure AD 密码保护所需的服务，请在 Azure[门户中启用预置 Azure AD 密码保护](howto-password-ban-bad-on-premises-operations.md)以完成部署。
