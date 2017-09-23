---
title: "排查 StorSimple 8000 系列部署问题 | Microsoft Docs"
description: "介绍如何诊断和修复首次部署 StorSimple 时发生的错误。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: d6642231bf9d012b0015902c82d2bfde3b86285c
ms.contentlocale: zh-cn
ms.lasthandoff: 07/08/2017

---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>排查 StorSimple 设备部署问题
## <a name="overview"></a>概述
本文为 Microsoft Azure StorSimple 部署提供有用的故障排除指南。 它描述了常见问题、可能的原因和建议的步骤，以帮助你解决在配置 StorSimple 时可能遇到的问题。 

此信息适用于 StorSimple 8000 系列物理设备和 StorSimple 云设备。

> [!NOTE]
> 你首次部署设备时可能会遇到与设备配置相关的问题，或者在设备运行时可能会出现这些问题。 本文着重介绍首次部署问题的故障排除。 若要对操作设备进行故障排除，请转到[使用诊断工具对操作设备进行故障排除](storsimple-8000-diagnostics.md)。

本文还介绍了用于排查 StorSimple 部署问题的工具，并提供了逐步故障排除示例。

## <a name="first-time-deployment-issues"></a>首次部署问题
如果你是第一次部署设备时遇到问题，请考虑以下方面：

* 如果你要对物理设备进行故障排除，请按照[安装 StorSimple 8100 设备](storsimple-8100-hardware-installation.md)或[安装 StorSimple 8600 设备](storsimple-8600-hardware-installation.md)中的说明安装和配置硬件。
* 检查部署的先决条件。 请确保你具有[部署配置清单](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist)中描述的所有信息。
* 查看 StorSimple 发行说明以查看是否描述了问题。 发行说明包含已知安装问题的解决方法。 

在设备部署期间，当用户运行设置向导时以及通过 Windows PowerShell for StorSimple 注册设备时，用户会遇到最常见的问题。 （你使用 Windows PowerShell for StorSimple 来注册和配置你的 StorSimple 设备。 有关设备注册的详细信息，请参阅[步骤 3：通过 Windows PowerShell for StorSimple 配置和注册设备](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)）。

以下部分可以帮助你解决在首次配置 StorSimple 设备时遇到的问题。

## <a name="first-time-setup-wizard-process"></a>首次设置向导过程
以下步骤概述了设置向导过程。 有关详细的设置信息，请参阅[部署本地 StorSimple 设备](storsimple-8000-deployment-walkthrough-u2.md)。

1. 运行 [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) cmdlet 以启动设置向导，它将指导你完成其余步骤。 
2. 配置网络：设置向导允许你为 StorSimple 设备上的 DATA 0 网络接口配置网络设置。 这些设置包括：
   * 虚拟 IP (VIP)、子网掩码和网关 – [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) cmdlet 在后台执行。 它为 StorSimple 设备上的 DATA 0 网络接口配置 IP 地址、子网掩码和网关。
   * 主 DNS 服务器 – [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) cmdlet 在后台执行。 它配置 StorSimple 解决方案的 DNS 设置。
   * NTP 服务器 – [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet 在后台执行。 它配置 StorSimple 解决方案的 NTP 服务器设置。
   * 可选 Web 代理 – [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) cmdlet 在后台执行。 它设置和启用 StorSimple 解决方案的 Web 代理配置。
3. 设置密码：下一步是设置设备管理员密码。
   设备管理员密码用于登录到你的设备。 默认设备密码为 **Password1**。
        
     > [!IMPORTANT]
     > 密码在注册之前收集，但仅在成功注册设备后应用。 如果无法应用密码，系统将提示你再次提供密码，直到收集所需的密码（满足复杂性要求）。
     
4. 注册设备：最后一步是使用在 Microsoft Azure 中运行的 StorSimple 设备管理器服务注册设备。 注册需要从 Azure 门户[获取服务注册密钥](storsimple-8000-manage-service.md#get-the-service-registration-key)，并在设置向导中提供此密钥。 **成功注册设备后，系统会提供一个服务数据加密密钥。请确保将此加密密钥保存在安全位置，因为在服务中注册所有后续设备时都将用到它。**

## <a name="common-errors-during-device-deployment"></a>部署设备期间的常见错误
下表列出了执行以下操作时可能遇到的常见错误：

* 配置所需的网络设置。
* 配置可选的 Web 代理设置。
* 设置设备管理员密码。
* 注册设备。

## <a name="errors-during-the-required-network-settings"></a>配置所需的网络设置期间的错误
| 否。 | 错误消息 | 可能的原因 | 建议的操作 |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard：此命令只能在主动控制器上运行。 |在被动控制器上执行配置。 |从主动控制器运行此命令。 有关详细信息，请参阅[标识设备上的主动控制器](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)。 |
| 2 |Invoke-HcsSetupWizard：设备未准备就绪。 |DATA 0 上的网络连接存在问题。 |检查 DATA 0 上的物理网络连接。 |
| 3 |Invoke-HcsSetupWizard：有一个 IP 地址与网络上的另一个系统冲突（异常来自 HRESULT：0x80070263）。 |为 DATA 0 提供的 IP 已被另一个系统使用。 |提供一个未使用的新 IP。 |
| 4 |Invoke-HcsSetupWizard：群集资源失败。 （异常来自 HRESULT：0x800713AE）。 |VIP 重复。 提供的 IP 已在使用中。 |提供一个未使用的新 IP。 |
| 5 |Invoke-HcsSetupWizard：IPv4 地址无效。 |提供的 IP 地址的格式不正确。 |检查格式并再次提供你的 IP 地址。 有关详细信息，请参阅 [Ipv4 寻址][1]。 |
| 6 |Invoke-HcsSetupWizard：IPv6 地址无效。 |提供的 IP 地址的格式不正确。 |检查格式并再次提供你的 IP 地址。 有关详细信息，请参阅 [Ipv6 寻址][2]。 |
| 7 |Invoke-HcsSetupWizard：端点映射程序中没有更多可用的端点。 （异常来自 HRESULT：0x800706D9） |群集功能不工作。 |[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)以了解后续步骤。 |

## <a name="errors-during-the-optional-web-proxy-settings"></a>配置可选 Web 代理设置期间的错误
| 否。 | 错误消息 | 可能的原因 | 建议的操作 |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard：参数无效（异常来自 HRESULT：0x80070057） |为代理设置提供的一个参数无效。 |提供的 URI 的格式不正确。 使用以下格式：http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 |Invoke-HcsSetupWizard：RPC 服务器不可用（异常来自 HRESULT：0x800706ba） |根本原因是以下项之一：<ol><li>群集未启动。</li><li>被动控制器不能与主动控制器通信，并且命令从被动控制器运行。</li></ol> |具体取决于根本原因：<ol><li>[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)以确保群集已启动。</li><li>从主动控制器运行命令。 如果要从被动控制器运行命令，则需要确保被动控制器可以与主动控制器通信。 如果此连接中断，你需要[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)。</li></ol> |
| 3 |Invoke-HcsSetupWizard：RPC 调用失败（异常来自 HRESULT：0x800706be） |群集已关闭。 |[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)以确保群集已启动。 |
| 4 |Invoke-HcsSetupWizard：找不到群集资源（异常来自 HRESULT：0x8007138f） |找不到群集资源。 安装不正确时可能出现这个错误。 |你可能需要将设备重置为出厂默认设置。 [联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md) 以创建群集资源。 |
| 5 |Invoke-HcsSetupWizard：群集资源无法联机（异常来自 HRESULT：0x8007138c） |群集资源未联机。 |[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)以了解后续步骤。 |

## <a name="errors-related-to-device-administrator-password"></a>与设备管理员密码相关的错误
默认的设备管理员密码为 **Password1**。 此密码在首次登录后过期；因此，你将需要使用设置向导来更改它。 当你首次注册设备时，必须提供新的设备管理员密码。 

请确保你的密码满足以下要求：

* 你的设备管理员密码应介于 8 到 15 个字符之间。
* 密码应包含以下 4 种字符类型中的 3 种：小写字符、大写字符、数字字符和特殊字符。 
* 你的密码不能与最近用过的 24 个密码相同。

此外，请记住密码每年过期，并且只有在成功注册设备后才能更改。 如果注册由于任何原因失败，密码将不会更改。

有关设备管理员密码的详细信息，请转到[使用 StorSimple 设备管理器服务更改 StorSimple 密码](storsimple-8000-change-passwords.md)。

设置设备管理员密码和 StorSimple Snapshot Manager 密码时，可能会遇到以下一个或多个错误。

| 否。 | 错误消息 | 建议的操作 |
| --- | --- | --- |
| 1 |密码超过最大长度。 |你的设备管理员密码必须介于 8 到 15 个字符之间。 |
| 2 |密码不符合所需的长度。 |你的设备管理员密码必须介于 8 到 15 个字符之间。|
| 3 |密码必须包含小写字符。 |密码必须包含以下 4 种字符类型中的 3 种：小写字符、大写字符、数字字符和特殊字符。 请确保你的密码满足这些要求。 |
| 4 |密码必须包含数字字符。 |密码必须包含以下 4 种字符类型中的 3 种：小写字符、大写字符、数字字符和特殊字符。 请确保你的密码满足这些要求。 |
| 5 |密码必须包含特殊字符。 |密码必须包含以下 4 种字符类型中的 3 种：小写字符、大写字符、数字字符和特殊字符。 请确保你的密码满足这些要求。 |
| 6 |密码必须包含以下 4 种字符类型中的 3 种：大写字符、小写字符、数字字符和特殊字符。 |你的密码不包含所需类型的字符。 请确保你的密码满足这些要求。 |
| 7 |参数与确认信息不匹配。 |请确保你的密码满足所有要求，并确保你输入的密码正确。 |
| 8 |你的密码不能与默认密码匹配。 |默认密码为 *Password1*。 你需要在首次登录后更改此密码。 |
| 9 |你输入的密码与设备密码不匹配。 请重新键入密码。 |检查密码并再次键入。 |

密码在设备注册之前收集，但仅在成功注册后才应用。 密码恢复工作流需要注册设备。

> [!IMPORTANT]
> 一般情况下，如果尝试应用密码失败，则软件会重复尝试收集密码，直到成功。 在极少数情况下，无法应用密码。 在这种情况下，你可以注册设备并继续操作，但密码不会更改。 注册后，可以通过 Azure 门户更改设备管理员密码。


可以在 Azure 门户中通过 StorSimple 设备管理器服务重置密码。 有关详细信息，请转到[更改设备管理员密码](storsimple-8000-change-passwords.md#change-the-device-administrator-password)。

## <a name="errors-during-device-registration"></a>设备注册期间的错误
可以使用在 Microsoft Azure 中运行的 StorSimple 设备管理器服务来注册设备。 在设备注册过程中，你可能会遇到以下一个或多个问题。

| 否。 | 错误消息 | 可能的原因 | 建议的操作 |
| --- | --- | --- | --- |
| 1 |错误 350027：无法使用 StorSimple 设备管理器注册设备。 | |等候几分钟时间，然后重试操作。 如果该问题仍然存在，请[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)。 |
| 2 |错误 350013：注册设备时发生错误。 这可能是由于服务注册密钥不正确。 | |请使用正确的服务注册密钥再次注册该设备。 有关详细信息，请参阅[获取服务注册密钥](storsimple-8000-manage-service.md#get-the-service-registration-key)。 |
| 3 |错误 350063：已通过对 StorSimple 设备管理器服务的身份认证，但注册失败。 请稍后重试操作。 |此错误表示已通过使用 ACS 的身份验证，但对服务的注册调用失败。 这可能是偶发性网络故障的结果。 |如果该问题仍然存在，请[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)。 |
| 4 |错误 350049：在注册期间无法访问服务。 |当对服务进行调用时，接收到 Web 异常。 在某些情况下，稍后重试该操作即可解决此问题。 |请检查你的 IP 地址和 DNS 名称，然后重试该操作。 如果该问题仍然存在，请[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)。 |
| 5 |错误 350031：设备已注册。 | |无需执行任何操作。 |
| 6 |错误 350016：设备注册失败。 | |请确保注册密钥正确。 |
| 7 |Invoke-HcsSetupWizard：注册设备时发生错误；这可能是由于 IP 地址或 DNS 名称不正确。 请检查你的网络设置，然后重试。 如果该问题仍然存在，请[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)。 （错误 350050） |确保你的设备可以 ping 外部网络。 如果你没有连接到外部网络，则注册可能会失败并出现此错误。 此错误可能是以下一个或多个原因的组合：<ul><li>IP 不正确</li><li>子网不正确</li><li>网关不正确</li><li>DNS 设置不正确</li></ul> |请参阅[分步故障排除示例](#step-by-step-storsimple-troubleshooting-example)中的步骤。 |
| 8 |Invoke-HcsSetupWizard：由于内部服务错误 [0x1FBE2]，当前操作失败。 请稍后重试操作。 如果该问题仍然存在，请联系 Microsoft 支持部门。 |这是为服务或代理中所有用户不可见错误抛出的通用错误。 最常见的原因可能是 ACS 身份验证已失败。 导致此故障的可能原因是 NTP 服务器配置存在问题，并且设备上的时间设置不正确。 |更正时间（如果有问题），然后重试注册操作。 如果使用 Set-HcsSystem -Timezone 命令调整时区，请将时区中的每个单词的首字母大写（例如“Pacific Standard Time”）。  如果此问题仍然存在，请[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)以了解后续步骤。 |
| 9 |警告：无法激活设备。 你的设备管理员和 StorSimple Snapshot Manager 密码尚未更改。 |如果注册失败，则设备管理员和 StorSimple Snapshot Manager 密码不会更改。 | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>用于排查 StorSimple 部署问题的工具
StorSimple 包含多个工具，可用于对 StorSimple 解决方案进行故障排除。 其中包括：

* 支持包和设备日志。
* 专为故障排除而设计的 cmdlet。

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>可用于故障排除的支持包和设备日志
支持包包含所有可帮助 Microsoft 支持团队排查设备问题的相关日志。 你可以使用 Windows PowerShell for StorSimple 生成加密的支持包，然后与支持人员共享。

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>查看支持包的日志或内容
1. 使用 Windows PowerShell for StorSimple 生成支持包，如[创建和管理支持包](storsimple-8000-create-manage-support-package.md)中所述。
2. 在客户端计算机上本地下载[解密脚本](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65)。
3. 使用此[分步过程](storsimple-8000-create-manage-support-package.md#edit-a-support-package)打开和解密支持包。
4. 解密的支持包日志采用 etw/etvx 格式。 你可以执行以下步骤以在 Windows 事件查看器中查看这些文件：
   
   1. 在 Windows 客户端上运行 **eventvwr** 命令。 这将启动事件查看器。
   2. 在“**操作**”窗格中，单击“**打开保存的日志**”并指向采用 etvx/etw 格式的日志文件（支持包）。 你现在可以查看该文件。 打开文件后，你可以右键单击并将该文件另存为文本。
      
      > [!IMPORTANT]
      > 你还可以使用 **Get-WinEvent** cmdlet 以在 Windows PowerShell 中打开这些文件。 有关详细信息，请参阅 Windows PowerShell cmdlet 参考文档中的 [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx)。
     
5. 当在事件查看器中打开日志时，请查找包含与设备配置相关的问题的以下日志：
   
   * hcs_pfconfig/Operational Log
   * hcs_pfconfig/Config
6. 在这些日志文件中，搜索与设置向导调用的 cmdlet 相关的字符串。 有关这些 cmdlet 的列表，请参阅[首次设置向导过程](#first-time-setup-wizard-process)。
7. 如果你无法找出问题的原因，你可以[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)以了解后续步骤。 在联系 Microsoft 支持部门以寻求协助时，请使用[创建支持请求](storsimple-8000-contact-microsoft-support.md#create-a-support-request)中的步骤。

## <a name="cmdlets-available-for-troubleshooting"></a>可用于故障排除的 cmdlet
使用以下 Windows PowerShell cmdlet 检测连接错误。

* `Get-NetAdapter`：使用此 cmdlet 检测网络接口的运行状况。
* `Test-Connection`：使用此 cmdlet 检查网络内部和外部的网络连接。
* `Test-HcsmConnection`：使用此 cmdlet 检查成功注册的设备的连接。
* `Sync-HcsTime`：使用此 cmdlet 显示设备时间并强制与 NTP 服务器进行时间同步。
* `Enable-HcsPing` 和 `Disable-HcsPing`：使用这些 cmdlet 允许主机 ping 你的 StorSimple 设备上的网络接口。 默认情况下，StorSimple 网络接口不响应 ping 请求。
* `Trace-HcsRoute`：将此 cmdlet 用作路由跟踪工具。 它在一段时间内在到达最终目的地的路上向每个路由器发送数据包，然后基于从每个跃点返回的数据包来计算结果。 由于 `Trace-HcsRoute` 显示了任何给定路由器或链接的数据包丢失程度，因此可以准确指出哪些路由器或链接可能导致网络问题。
* `Get-HcsRoutingTable`：使用此 cmdlet 显示本地 IP 路由表。

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>使用 Get-NetAdapter cmdlet 进行故障排除
为第一次设备部署配置网络接口时，StorSimple 设备管理器服务 UI 中的硬件状态不可用，因为尚未使用该服务注册此设备。 此外，“硬件运行状况”边栏选项卡可能不会始终正确反映设备的状态，特别是在存在影响服务同步的问题时。 在这些情况下，可以使用 `Get-NetAdapter` cmdlet 确定网络接口的运行状况和状态。

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>要查看设备上的所有网络适配器的列表
1. 启动 Windows PowerShell for StorSimple，然后键入 `Get-NetAdapter`。 
2. 使用 `Get-NetAdapter` cmdlet 的输出和以下准则来了解网络接口的状态。
   
   * 如果接口正常运行并已启用，**ifIndex** 状态显示为 **Up**。
   * 如果接口正常运行但未以物理方式连接（通过网络电缆），**ifIndex** 显示为 **Disabled**。
   * 如果接口正常运行但未启用，**ifIndex** 状态显示为 **NotPresent**。
   * 如果接口不存在，则不会出现在此列表中。 StorSimple 设备管理器服务 UI 仍会显示此接口处于失败状态。

有关如何使用此 cmdlet 的详细信息，请转到 Windows PowerShell cmdlet 参考中的 [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx)。

以下部分显示 `Get-NetAdapter` cmdlet 的输出示例。

 在这些示例中，控制器 0 是被动控制器，并且配置如下：

* 设备上存在 DATA 0、DATA 1、DATA 2 和 DATA 3 网络接口。
* DATA 4 和 DATA 5 网络接口卡不存在；因此，它们不会列在输出中。
* DATA 0 已启用。

控制器 1 是主动控制器，配置如下：

* 设备上存在 DATA 0、DATA 1、DATA 2 和 DATA 3、DATA 4 和 DATA 5 网络接口。
* DATA 0 已启用。

**示例输出 – 控制器 0**

以下是控制器 0（被动控制器）的输出。 DATA 1、DATA 2 和 DATA 3 未连接。 DATA 4和 DATA 5 未列出，因为它们不存在于设备上。

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**示例输出 – 控制器 1**

以下是控制器 1（主动控制器）的输出。 只有设备上的 DATA 0 网络接口已配置并正常工作。

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>使用 Test-Connection cmdlet 进行故障排除
你可以使用 `Test-Connection` cmdlet 确定 StorSimple 设备是否可以连接到外部网络。 如果所有网络参数（包括 DNS）在设置向导中正确配置，你可以使用 `Test-Connection` cmdlet 来 ping 网络外部的已知地址，例如 outlook.com。

如果禁用了 ping，则应启用 ping 以使用此 cmdlet 排查连接问题。

请参阅 `Test-Connection` cmdlet 的以下输出示例。

> [!NOTE]
> 在第一个示例中，设备配置了不正确的 DNS。 在第二个示例中，DNS 是正确的。

**示例输出 – 不正确的 DNS**

在以下示例中，没有 IPV4 和 IPV6 地址的输出，这表示 DNS 未解析。 这意味着没有到外部网络的连接，并且需要提供正确的 DNS。

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**示例输出 – 正确的 DNS**

在以下示例中，DNS 返回 IPV4 地址，这表示 DNS 配置正确。 这确认了与外部网络的连接。

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>使用 Test-HcsmConnection cmdlet 进行故障排除
对已连接到 StorSimple 设备管理器服务并且已注册该服务的设备使用 `Test-HcsmConnection` cmdlet。 借助此 cmdlet 可验证注册设备与相应 StorSimple 设备管理器服务之间的连接。 可以在 Windows PowerShell for StorSimple 上运行此命令。

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>若要运行 Test-HcsmConnection cmdlet
1. 请确保设备已注册。
2. 检查设备状态。 如果设备已停用、处于维护模式或脱机，可能会看到以下错误之一：
   
   * ErrorCode.CiSDeviceDecommissioned – 这表示设备已停用。
   * ErrorCode.DeviceNotReady – 这表示设备处于维护模式。
   * ErrorCode.DeviceNotReady – 这表示设备未联机。
3. 验证 StorSimple 设备管理器服务是否正在运行（使用 [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) cmdlet）。 如果该服务未运行，你可能会看到以下错误：
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – 这表示运行 Get-ClusterResource 时出现异常。
4. 检查访问控制服务 (ACS) 令牌。 如果它引发了 Web 异常，则可能是网关问题、缺少代理身份验证、DNS 不正确或身份验证失败的结果。 你可能会看到以下错误：
   
   * ErrorCode.CiSApplianceGateway – 这表示 HttpStatusCode.BadGateway 异常：域名解析器服务无法解析主机名。
   * ErrorCode.CiSApplianceProxy – 这表示 HttpStatusCode.ProxyAuthenticationRequired 异常（HTTP 状态代码 407）：客户端无法使用代理服务器进行身份验证。
   * ErrorCode.CiSApplianceDNSError – 这表示 WebExceptionStatus.NameResolutionFailure 异常：域名解析器服务无法解析主机名。
   * ErrorCode.CiSApplianceACSError – 这表示服务返回了身份验证错误，但存在连接。
     
     如果它没有引发 Web 异常，请检查 ErrorCode.CiSApplianceFailure。 这表示设备故障。
5. 检查云服务连接。 如果服务引发 Web 异常，你可能会看到以下错误：
   
   * ErrorCode.CiSApplianceGateway – 这表示 HttpStatusCode.BadGateway 异常：中间代理服务器从另一个代理或原始服务器接收到错误请求。
   * ErrorCode.CiSApplianceProxy – 这表示 HttpStatusCode.ProxyAuthenticationRequired 异常（HTTP 状态代码 407）：客户端无法使用代理服务器进行身份验证。
   * ErrorCode.CiSApplianceDNSError – 这表示 WebExceptionStatus.NameResolutionFailure 异常：域名解析器服务无法解析主机名。
   * ErrorCode.CiSApplianceACSError – 这表示服务返回了身份验证错误，但存在连接。
     
     如果它没有引发 Web 异常，请检查 ErrorCode.CiSApplianceSaasServiceError。 这表示 StorSimple 设备管理器服务有问题。
6. 检查 Azure 服务总线连接。 ErrorCode.CiSApplianceServiceBusError 表示该设备无法连接到服务总线。

日志文件 CiSCommandletLog0Curr.errlog and CiSAgentsvc0Curr.errlog 将包含详细信息，例如异常详细信息。

有关如何使用 cmdlet 的详细信息，请转到 Windows PowerShell 参考文档中的 [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx)。

> [!IMPORTANT]
> 你可以为主动和被动控制器运行此 cmdlet。

请参阅 `Test-HcsmConnection` cmdlet 的以下输出示例。

**示例输出 – 运行 StorSimple Update 3 的成功注册的设备**

      Controller1>Test-HcsmConnection

      Checking device registration state  ... Success
      Device registered successfully

      Checking primary NTP server [time.windows.com] ... Success

      Checking web proxy  ... NOT SET

      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET

      Checking device online  ... Success

      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success

      Checking connectivity from device to service  ... This will take a few minutes.

      Checking connectivity from device to service  ... Success

      Checking connectivity from service to device  ... Success

      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**示例输出 – 脱机设备** 

此示例来自 Azure 门户中状态为“脱机”的设备。

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

该设备无法使用当前的 Web 代理配置进行连接。 这可能是 Web 代理配置或网络连接的问题。 在这种情况下，你应确保你的 Web 代理设置正确，并且你的 Web 代理服务器处于联机状态且可访问。

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>使用 Sync-HcsTime cmdlet 进行故障排除
使用此 cmdlet 显示设备时间。 如果设备时间与 NTP 服务器有时差，则可以使用此 cmdlet 来强制与 NTP 服务器同步时间。
- 如果设备和 NTP 服务器之间的时差大于 5 分钟，你将看到一条警告。 
- 如果时差超过 15 分钟，则设备将脱机。 你仍然可以使用此 cmdlet 强制执行时间同步。 
- 但是，如果时差超过 15 小时，则你将无法强制同步时间，并且将显示错误消息。

**示例输出 – 使用 Sync-HcsTime 的强制时间同步**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>使用 Enable-HcsPing 和 Disable-HcsPing cmdlet 进行故障排除
使用这些 cmdlet 可确保设备上的网络接口响应 ICMP ping 请求。 默认情况下，StorSimple 网络接口不响应 ping 请求。 使用此 cmdlet 是了解你的设备是否联机和可访问的最简单方法。

**示例输出 – Enable-HcsPing 和 Disable-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>使用 Trace-HcsRoute cmdlet 进行故障排除
将此 cmdlet 用作路由跟踪工具。 它在一段时间内在到达最终目的地的路上向每个路由器发送数据包，然后基于从每个跃点返回的数据包来计算结果。 由于此 cmdlet 显示了任何给定路由器或链接的数据包丢失程度，因此可以准确指出哪些路由器或链接可能导致网络问题。

**示例输出显示了如何使用 Trace-HcsRoute 跟踪数据包的路由**

     Controller0>Trace-HcsRoute -Target 10.126.174.25

     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]

     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]

     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>使用 Get-HcsRoutingTable cmdlet 进行故障排除
使用此 cmdlet 查看 StorSimple 设备的路由表。 路由表是一组规则，可以帮助确定通过 Internet 协议 (IP) 网络传输的数据包将被定向到哪里。

路由表显示接口和将数据路由到指定网络的网关。 它还给出路由度量，其决定到达特定目的地所采用的路径。 路由度量越低，优先级越高。

例如，如果你有 2 个网络接口连接到 Internet，即 DATA 2 和 DATA 3。 如果 DATA 2和 DATA 3 的路由度量分别为 15 和 261，则具有较低路由度量的 DATA 2 是用于连接 Internet 的首选接口。

如果在 StorSimple 设备上运行 Update 1，则 DATA 0 网络接口对云流量具有最高优先级。 这意味着即使有其他启用云的接口，云流量也将通过 DATA 0 路由。

如果你运行 `Get-HcsRoutingTable` cmdlet 而不指定任何参数（如以下示例所示），则此 cmdlet 将输出 IPv4 和 IPv6 路由表。 或者，你可以指定 `Get-HcsRoutingTable -IPv4` 或 `Get-HcsRoutingTable -IPv6` 以获取相关的路由表。

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================

      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================

      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None

      Controller0>

## <a name="step-by-step-storsimple-troubleshooting-example"></a>分步 StorSimple 故障排除示例
以下示例显示了 StorSimple 部署的分步故障排除。 在示例方案中，设备注册失败，并显示一条错误消息，指示网络设置或 DNS 名称不正确。

返回的错误消息为：

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

该错误可能由以下任何原因引起：

* 硬件安装不正确
* 网络接口故障
* IP 地址、子网掩码、网关、主 DNS 服务器或 Web 代理不正确
* 注册密钥不正确
* 防火墙设置不正确

### <a name="to-locate-and-fix-the-device-registration-problem"></a>若要找出并修复设备注册问题
1. 请检查你的设备配置：在主动控制器上运行 `Invoke-HcsSetupWizard`。
   
   > [!NOTE]
   > 设置向导必须在主动控制器上运行。 要验证是否已连接到主动控制器，请查看串行控制台中显示的标题。 标题表示你是连接到控制器 0 还是控制器 1，以及控制器是主动还是被动。 有关详细信息，请转到[标识设备上的主动控制器](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)。
   
2. 请确保设备正确连接电缆：检查设备背板上的网络电缆。 缆线特定于设备型号。 有关详细信息，请转到[安装 StorSimple 8100 设备](storsimple-8100-hardware-installation.md)或[安装 StorSimple 8600 设备](storsimple-8600-hardware-installation.md)。
   
   > [!NOTE]
   > 如果使用 10 GbE 网络端口，则需要使用提供的 QSFP-SFP 适配器和 SFP 电缆。 有关详细信息，请参阅[建议用于 10 GbE 端口的电缆、交换机和收发器的列表](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)。
  
3. 验证网络接口的运行状况：
   
   * 使用 Get-NetAdapter cmdlet 可检测 DATA 0 的网络接口的运行状况。 
   * 如果链接不工作，**ifindex** 状态将指示接口已关闭。 然后，你需要检查端口到设备和交换机的网络连接。 你还需要排查出损坏的电缆。 
   * 如果怀疑主动控制器上的 DATA 0 端口出现故障，可以通过连接到控制器 1 上的 DATA 0 端口来确认。 若要确认这一点，请从控制器 0 的设备背面断开网络电缆，将电缆连接到控制器 1，然后再次运行 Get-NetAdapter cmdlet。
     如果控制器上的 DATA 0 端口出现故障，请[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)以了解后续步骤。 你可能需要更换系统上的控制器。
4. 验证与交换机的连接：
   
   * 确保主机箱中控制器 0 和控制器 1 上的 DATA 0 网络接口位于同一子网中。 
   * 检查集线器或路由器。 通常情况下，应将两个控制器连接到同一集线器或路由器。 
   * 确保用于连接的交换机在同一 vLAN 中的两个控制器都具有 DATA 0。
5. 排除任何用户失误：
   
   * 再次运行设置向导（运行 **Invoke-hcssetupwizard**）并再次输入值以确保没有错误。 
   * 验证使用的注册密钥。 相同的注册密钥可用于将多个设备连接到 StorSimple 设备管理器服务。 使用[获取服务注册密钥](storsimple-8000-manage-service.md#get-the-service-registration-key)中的过程，以确保你使用正确的注册密钥。
     
     > [!IMPORTANT]
     > 如果你有多个服务正在运行，你将需要确保相应服务的注册密钥用于注册设备。 如果使用错误的 StorSimple 设备管理器服务注册了设备，则需要[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)以了解后续步骤。 你可能必须执行设备的出厂重置（可能导致数据丢失），然后将其连接到预期的服务。
     > 
     > 
6. 使用 Test-Connection cmdlet 验证你是否连接到外部网络。 有关详细信息，请转到[使用 Test-Connection cmdlet 进行故障排除](#troubleshoot-with-the-test-connection-cmdlet)。
7. 检查防火墙干扰。 如果你已验证虚拟 IP (VIP)、子网、网关和 DNS 设置都正确，但是仍然看到连接问题，则你的防火墙可能会阻止设备与外部网络之间的通信。 你需要确保端口 80 和 443 在 StorSimple 设备上可用于出站通信。 有关详细信息，请参阅 [StorSimple 设备的网络要求](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)。
8. 查看日志。 转到[可用于故障排除的支持包和设备日志](#support-packages-and-device-logs-available-for-troubleshooting)。
9. 如果上述步骤无法解决问题，请[联系 Microsoft 支持部门](storsimple-8000-contact-microsoft-support.md)以获得协助。

## <a name="next-steps"></a>后续步骤
[了解如何使用诊断工具对 StorSimple 设备进行故障排除](storsimple-8000-diagnostics.md)。

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 

