---
title: Connected Machine Windows 代理概述
description: 本文详细概述了可用的支持 Azure Arc 的服务器代理，它支持监视混合环境中托管的虚拟机。
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 990b5999a8483c6417049ac5ab965843c2b13659
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908175"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>支持 Azure Arc 的服务器代理概述

通过启用了 Azure Arc 的服务器连接的计算机代理，你可以管理在公司网络或其他云提供商的 Azure 外部托管的 Windows 和 Linux 计算机。 本文提供该代理的详细概述、系统和网络要求以及不同的部署方法。

## <a name="agent-component-details"></a>代理组件详细信息

Azure 连接的计算机代理包包含多个逻辑组件，这些组件捆绑在一起。

* 混合实例元数据服务 (HIMDS) 管理与 Azure 的连接和连接的计算机的 Azure 标识。

* 来宾配置代理提供来宾内策略和来宾配置功能，如评估计算机是否符合所需的策略。

    [对于断开连接的计算机](../../governance/policy/concepts/guest-configuration.md)，请注意以下行为：

    * 以断开连接的计算机为目标的来宾配置策略分配不受影响。
    * 来宾分配在本地存储14天。 在14天内，如果连接的计算机代理重新连接到服务，则重新应用策略分配。
    * 分配将在14天后删除，14天后不会重新分配到计算机。

* 扩展代理管理 VM 扩展，包括安装、卸载和升级。 将从 Azure 下载扩展并将其复制到 `%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads` Windows 上的文件夹，并将其复制到 `/opt/GC_Ext/downloads` 。 在 Windows 上，扩展安装到以下路径 `%SystemDrive%\Packages\Plugins\<extension>` ，在 Linux 上安装了扩展 `/var/lib/waagent/<extension>` 。

## <a name="download-agents"></a>下载代理

可从以下位置下载适用于 Windows 和 Linux 的 Azure Connected Machine 代理包。

* Microsoft 下载中心提供的 [Windows 代理 Windows Installer 包](https://aka.ms/AzureConnectedMachineAgent)。

* Linux 代理包通过 Microsoft 的[包存储库](https://packages.microsoft.com/)使用分发版的首选包格式（.RPM 或 .DEB）进行分发。

适用于 Windows 和 Linux 的 Azure Connected Machine 代理可以手动或自动升级到最新版本，具体取决于你的要求。 有关详细信息，请参阅[此文](manage-agent.md)。

## <a name="prerequisites"></a>先决条件

### <a name="supported-operating-systems"></a>支持的操作系统

Azure Connected Machine 代理正式支持以下版本的 Windows 和 Linux 操作系统： 

- Windows Server 2012 R2 及更高版本（包括 Windows Server Core）
- Ubuntu 16.04 和 18.04 LTS (x64) 
- CentOS Linux 7 (x64) 
- SUSE Linux Enterprise Server (SLES) 15 (x64) 
- Red Hat Enterprise Linux (RHEL) 7 (x64) 
- Amazon Linux 2 (x64) 

### <a name="required-permissions"></a>所需的权限

* 若要将计算机加入，你必须是 **Azure Connected Machine 加入**角色的成员。

* 若要读取、修改、重新载入和删除计算机，你是 **Azure 连接的计算机资源管理员** 角色的成员。 

### <a name="azure-subscription-and-service-limits"></a>Azure 订阅和服务限制

在将计算机配置为启用 Azure Arc 的服务器之前，请查看 Azure 资源管理器 [订阅限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) 和 [资源组限制](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) ，以规划要连接的计算机的数量。

### <a name="transport-layer-security-12-protocol"></a>传输层安全性1.2 协议

为了确保传输到 Azure 的数据的安全性，我们强烈建议你将计算机配置为使用传输层安全性 (TLS) 1.2。 我们发现旧版 TLS/安全套接字层 (SSL) 容易受到攻击，尽管目前出于向后兼容，这些协议仍可正常工作，但我们**不建议使用**。

|平台/语言 | 支持 | 更多信息 |
| --- | --- | --- |
|Linux | Linux 分发版往往依赖于 [OpenSSL](https://www.openssl.org) 来提供 TLS 1.2 支持。 | 请检查 [OpenSSL 变更日志](https://www.openssl.org/news/changelog.html)，确认你的 OpenSSL 版本是否受支持。|
| Windows Server 2012 R2 和更高版本 | 受支持，并且默认已启用。 | 确认是否仍在使用[默认设置](/windows-server/security/tls/tls-registry-settings)。|

### <a name="networking-configuration"></a>网络配置

适用于 Linux 和 Windows 的 Connected Machine 代理通过 TCP 端口 443 安全地与 Azure Arc 进行出站通信。 如果计算机通过防火墙或代理服务器建立连接以通过 Internet 进行通信，请查看下文了解网络配置要求。

如果防火墙或代理服务器限制了出站连接，请确保不要阻止下面列出的 URL。 如果只允许代理与服务进行通信所需的 IP 范围或域名，则还必须允许访问以下服务标记和 URL。

服务标记：

* AzureActiveDirectory
* AzureTrafficManager

URL：

| 代理资源 | 说明 |
|---------|---------|
|`management.azure.com`|Azure 资源管理器|
|`login.windows.net`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`agentserviceapi.azure-automation.net`|来宾配置|
|`*-agentservice-prod-1.azure-automation.net`|来宾配置|
|`*.guestconfiguration.azure.com` |来宾配置|
|`*.his.arc.azure.com`|混合标识服务|

有关每个服务标记/区域的 IP 地址列表，请参阅 JSON 文件 - [Azure IP 范围和服务标记 - 公有云](https://www.microsoft.com/download/details.aspx?id=56519)。 Microsoft 每周将发布包含每个 Azure 服务及其使用的 IP 范围的更新。 有关详细信息，请查看[服务标记](../../virtual-network/security-overview.md#service-tags)。

除了上表中列出的 URL 以外，还需要服务标记 IP 地址范围信息，因为大多数服务当前没有服务标记注册。 因此，IP 地址可能会变化。 如果防火墙配置需要 IP 地址范围，则应使用 **AzureCloud** 服务标记允许对所有 Azure 服务的访问。 请勿禁用对这些 URL 的安全监视或检查，但就像允许其他 Internet 流量一样允许这些 URL。

### <a name="register-azure-resource-providers"></a>注册 Azure 资源提供程序

启用 Azure Arc 的服务器依赖于订阅中的以下 Azure 资源提供程序，以便使用此服务：

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

如果未注册这些提供程序，可使用以下命令注册：

Azure PowerShell：

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI：

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

此外，还可以按照 [Azure 门户](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)下的步骤，在 Azure 门户中注册资源提供程序。

## <a name="installation-and-configuration"></a>安装和配置

可以根据要求使用不同的方法，将混合环境中的计算机直接连接到 Azure。 下表详细介绍了每种方法，让你确定最适合组织的方法。

| 方法 | 说明 |
|--------|-------------|
| 交互式 | 遵循[从 Azure 门户连接计算机](onboard-portal.md)中的步骤，在一台或多台计算机上手动安装代理。<br> 在 Azure 门户中，可以生成一个脚本并在计算机上执行，以自动完成代理安装和配置的步骤。|
| 大规模 | 遵循[使用服务主体连接计算机](onboard-service-principal.md)中的步骤，为多台计算机安装并配置代理。<br> 此方法创建一个服务主体来以非交互方式连接计算机。|
| 大规模 | 按照[使用 Windows PowerShell DSC](onboard-dsc.md) 方法为多台计算机安装和配置代理。<br> 此方法使用服务主体以非交互方式将计算机与 PowerShell DSC 连接。 |

## <a name="connected-machine-agent-technical-overview"></a>连接的计算机代理技术概述

### <a name="windows-agent-installation-details"></a>Windows 代理安装详细信息

可以使用以下三种方法之一安装适用于 Windows 的 Connected Machine 代理：

* 双击文件 `AzureConnectedMachineAgent.msi`。
* 通过从命令外壳运行 Windows Installer 包 `AzureConnectedMachineAgent.msi` 来手动进行安装。
* 从 PowerShell 会话中使用脚本化方法。

安装适用于 Windows 的 Connected Machine 代理后，将应用下列其他系统范围的配置更改。

* 安装期间将创建以下安装文件夹。

    |Folder |说明 |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |包含代理支持文件的默认安装路径。|
    |%ProgramData%\AzureConnectedMachineAgent |包含代理配置文件。|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |包含获取的令牌。|
    |%ProgramData%\AzureConnectedMachineAgent\Config |包含代理配置文件 `agentconfig.json`，该文件记录其在服务中的注册信息。|
    |%SystemDrive%\Program Files\ArcConnectedMachineAgent\ExtensionService\GC | 包含来宾配置代理文件的安装路径。 |
    |%ProgramData%\GuestConfig |包含应用于 Azure) 策略的 (。|
    |%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads | 将从 Azure 下载扩展，并在此处复制。|

* 安装代理期间，将在目标计算机上创建以下 Windows 服务。

    |Service name |显示名称 |进程名称 |说明 |
    |-------------|-------------|-------------|------------|
    |himds |Azure 混合实例元数据服务 |himds.exe |此服务 (IMDS) 实现 Azure 实例元数据服务，以管理到 Azure 和连接的计算机的 Azure 标识的连接。|
    |DscService |来宾配置服务 |dsc_service.exe |在 Azure 中使用的所需状态配置 (DSC) v2 用于实现来宾内策略。|

* 安装代理期间，将创建以下环境变量。

    |名称 |默认值 |说明 |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* 有几个日志文件可用于故障排除。 下表对它们进行了说明。

    |日志 |说明 |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |记录代理 (HIMDS) 服务的详细信息以及与 Azure 的交互。|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |当使用 verbose (-v) 参数时，包含 azcmagent 工具命令的输出。|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |记录 DSC 服务活动的详细信息，<br> 尤其是 HIMDS 服务和 Azure 策略之间的连接。|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |记录有关 DSC 服务遥测和详细日志记录的详细信息。|
    |%SystemDrive%\ProgramData\GuestConfig\ ext_mgr_logs|记录有关扩展代理组件的详细信息。|
    |%SystemDrive%\ProgramData\GuestConfig\ extension_logs\<Extension>|记录安装的扩展中的详细信息。|

* 将创建本地安全组“混合代理扩展应用程序”。

* 卸载代理期间，不会删除以下项目。

    * %ProgramFiles%\AzureConnectedMachineAgent\Logs
    * %ProgramData%\AzureConnectedMachineAgent 和子目录
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Linux 代理安装详细信息

适用于 Linux 的 Connected Machine 代理以 Microsoft [包存储库](https://packages.microsoft.com/)中分发版的首选包格式（.RPM 或 .DEB）提供。 可以使用 shell 脚本捆绑包 [Install_linux_azcmagent.sh](https://aka.ms/azcmagent) 安装和配置该代理。

安装适用于 Linux 的 Connected Machine 代理后，将应用下列其他系统范围的配置更改。

* 安装期间将创建以下安装文件夹。

    |Folder |说明 |
    |-------|------------|
    |/var/opt/azcmagent/ |包含代理支持文件的默认安装路径。|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | 包含来宾配置代理文件的安装路径。|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |包含获取的令牌。|
    |/var/lib/GuestConfig |包含应用于 Azure) 策略的 (。|
    |/opt/GC_Ext/downloads|将从 Azure 下载扩展，并在此处复制。|

* 安装代理期间，将在目标计算机上创建以下守护程序。

    |Service name |显示名称 |进程名称 |说明 |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Azure 混合实例元数据服务 |/opt/azcmagent/bin/himds |此服务 (IMDS) 实现 Azure 实例元数据服务，以管理到 Azure 和连接的计算机的 Azure 标识的连接。|
    |dscd.service |来宾配置服务 |/opt/DSC/dsc_linux_service |这是 Azure 中用于实现来宾内策略的 Desired State Configuration (DSC v2) 代码库。|

* 有几个日志文件可用于故障排除。 下表对它们进行了说明。

    |日志 |说明 |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |记录代理 (HIMDS) 服务的详细信息以及与 Azure 的交互。|
    |/var/opt/azcmagent/log/azcmagent.log |当使用 verbose (-v) 参数时，包含 azcmagent 工具命令的输出。|
    |/opt/logs/dsc.log |记录 DSC 服务活动的详细信息，<br> 特别是 himds 服务与 Azure Policy 之间的连接。|
    |/opt/logs/dsc.telemetry.txt |记录有关 DSC 服务遥测和详细日志记录的详细信息。|
    |/var/lib/GuestConfig/ext_mgr_logs |记录有关扩展代理组件的详细信息。|
    |/var/log/GuestConfig/extension_logs|记录安装的扩展中的详细信息。|

* 安装代理期间，将创建以下环境变量。 这些变量在 `/lib/systemd/system.conf.d/azcmagent.conf` 中设置。

    |名称 |默认值 |说明 |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* 卸载代理期间，不会删除以下项目。

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>后续步骤

若要开始评估启用了 Azure Arc 的服务器，请按照将 [混合计算机连接到 azure 中的 Azure 门户](onboard-portal.md)一文。
