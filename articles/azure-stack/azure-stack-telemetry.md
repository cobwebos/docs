---
title: Azure Stack 遥测 | Microsoft Docs
description: 介绍如何使用 PowerShell 配置 Azure Stack 遥测设置。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: comartin
ms.openlocfilehash: ed3f09f942bdaa803ae8024d5c02230173190107
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248191"
---
# <a name="azure-stack-telemetry"></a>Azure Stack 遥测

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure 堆栈遥测自动将系统数据上载到 Microsoft 通过连接的用户体验。 Microsoft 团队使用的数据的 Azure 堆栈遥测收集以改善客户体验。 此数据也用于安全、 运行状况、 质量和性能分析。

对于 Azure 堆栈运算符，遥测可以提供有价值见解企业部署，并为你提供可帮助形状以后版本的 Azure 堆栈的声音。

> [!NOTE]
> 你还可以配置 Azure 堆栈将转发到 Azure 的计费的使用情况信息。 选择即用即付计费模式的多节点 Azure Stack 客户一定要这样做。 使用情况报告遥测从独立控制，不需要为选择的容量模型的多节点客户还是 Azure 堆栈开发工具包用户。 对于上述方案，可以[使用注册脚本](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting)来关闭用量报告。

上的 Windows Server 2016 连接用户体验和遥测的组件，它使用基于 azure 的堆栈遥测[事件跟踪的 Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx) TraceLogging 技术来收集和存储事件和数据。 Azure 堆栈组件使用相同的技术来发布事件和通过使用公共操作系统事件日志记录和跟踪 Api 收集的数据。 这些 Azure 堆栈组件的示例包括这些提供程序： 网络资源、 存储资源、 监视资源和更新资源。 连接的用户体验和遥测组件对数据使用 SSL 进行加密，并使用证书固定到 Microsoft 数据管理服务通过 HTTPS 传输数据。

> [!IMPORTANT]
> 若要启用遥测数据通信流，必须在你的网络中打开端口 443 (HTTPS)。 互连用户体验与遥测组件连接到 Microsoft 数据管理服务（位于 https://v10.vortex-win.data.microsoft.com）。 互连用户体验与遥测组件还连接到 https://settings-win.data.microsoft.com 来下载配置信息。

## <a name="privacy-considerations"></a>隐私注意事项

ETW 服务将遥测数据发回到受保护的云存储。 最小特权原则指导到遥测数据的访问。 仅 Microsoft 人员具有有效的业务需要被授予访问权限的遥测数据。 Microsoft 不共享个人客户数据与第三方除客户的决定中所述的有限目的; 二是[Microsoft 隐私声明](https://privacy.microsoft.com/PrivacyStatement)。 与 Oem 和合作伙伴共享的业务报告包含聚合的匿名信息数据。 数据共享决策由 Microsoft 内部团队（包括隐私、法律和数据管理利益干系人）做出。

Microsoft 认为在中，和实践信息最小化。 我们努力收集仅需要并将其用于存储只相当于需要提供服务或用于分析的信息。 许多有关 Azure Stack 系统和 Azure 服务工作原则的信息在六个月内删除。 汇总或聚合的数据保留更长一段时间。

我们了解客户信息的隐私和安全都很重要。  Microsoft 客户隐私和 Azure 堆栈中的客户数据的保护采用仔细和全面的方法。 IT 管理员随时可以控制功能和隐私的自定义设置。 我们对于透明度和信任的承诺很明确：

- 我们打开与客户有关我们收集到的数据类型。
- 我们将在控件中的企业客户-它们可以自定义他们自己的隐私设置。
- 我们将客户隐私和安全放在第一位。
- 我们透明有关如何获取使用遥测数据。
- 我们使用遥测数据来改进客户体验。

Microsoft 不打算收集敏感数据，如信用卡号、 用户名和密码、 电子邮件地址或类似的敏感信息。 如果我们确定敏感信息是无意中收集到的，我们会予以删除。

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Microsoft 如何使用遥测数据的示例

遥测起着重要作用中帮助快速确定并修复客户部署和配置中的关键可靠性问题。 从遥测数据信息可帮助识别问题与服务或硬件配置。 Microsoft 的能够用于生态系统中，从客户和驱动器改进获取此数据引发的条形图集成 Azure 堆栈解决方案的质量。

遥测还能帮助 Microsoft 进一步了解客户如何部署组件、使用功能以及使用服务来实现业务目标。 这些 insights 帮助确定可能会直接影响客户体验和工作负荷的区域中的工程投资的优先级。

一些示例包括客户使用的容器、 存储和与 Azure 堆栈角色相关联的网络配置。 我们还使用到驱动器改进和智能到 Azure 堆栈管理和监视解决方案的见解。 这些改进使客户更容易以诊断问题并保存通过使更少的支持的 money 调用到 Microsoft。

## <a name="manage-telemetry-collection"></a>管理遥测数据的收集

我们不建议关闭你的组织中的遥测。 但是，在某些情况下这可能有必要。

在这些情况下，你可以配置通过使用注册表设置，在部署 Azure 堆栈之前或之后部署 Azure 堆栈使用遥测终结点发送给 Microsoft 的遥测级别。

### <a name="telemetry-levels-and-data-collection"></a>遥测数据级别和数据收集

在更改遥测设置之前，你应了解的遥测级别以及在每个级别收集哪些数据。

遥测设置按划分为四个级别 (0-3)，是累积和分类，如下所示：

**0 （安全）**</br>
仅限安全数据。 将保护操作系统所需的信息。 这包括有关连接的用户体验和遥测组件设置和 Windows Defender 的数据。 在此级别发出没有特定于 Azure 堆栈的遥测。

**1 （基本）**</br>
安全数据，以及基本运行状况和质量数据。 基本设备信息，包括： 质量相关数据、 应用程序兼容性、 应用程序使用情况数据和数据从**安全**级别。 将遥测级别设置为“基本”可启用 Azure Stack 遥测。 在此级别收集的数据包括：

- *基本的设备信息*，提供了有关类型和生态系统中的本机和虚拟 Windows Server 2016 实例的配置的了解。 这包括：

  - 机属性，例如 OEM 和模型。
  - 诸如网络适配器数目和它们的速度的网络属性。
  - 处理器和内存属性，例如的内核数，并已安装的内存量。
  - 存储特性，例如驱动器数、 驱动器和驱动器大小的类型。

- *遥测功能*，包括已上载的事件、 已删除的事件和最后的数据的百分比上载时间。
- *质量相关的信息*，帮助 Microsoft 初步了解 Azure Stack 的运行情况。 例如，在特定硬件配置的严重警报的计数。
- *兼容性数据*有助于提供的了解哪些资源提供程序安装系统和虚拟机。 这标识潜在的兼容性问题。

**2 （增强功能）**</br>
其他见解，包括： 如何使用的操作系统和 Azure 堆栈服务、 如何执行这些服务、 高级的可靠性数据和数据从**安全**和**基本**级别。

> [!NOTE]
> 这是默认遥测设置。

**3 （完整）**</br>
识别及帮助解决问题所需的全部数据，加上来自“安全”、“基本”和“增强”级别的数据。

> [!IMPORTANT]
> 这些遥测级别仅适用于 Microsoft Azure 堆栈组件。 Azure Stack 硬件合作伙伴在硬件生命周期主机中运行的非 Microsoft 软件组件和服务可能与这些遥测级别以外的云服务通信。 应该咨询 Azure Stack 硬件解决方案提供商，以了解其遥测策略，以及如何启用或禁用。

关闭 Windows 和 Azure Stack 遥测也会禁用 SQL 遥测。 有关 Windows Server 遥测设置的含义的详细信息，请参阅 [Windows 遥测白皮书](https://aka.ms/winservtelemetry)。

### <a name="asdk-set-the-telemetry-level-in-the-windows-registry"></a>ASDK： 在 Windows 注册表中设置的遥测级别

可以使用 Windows 注册表编辑器手动将遥测级别物理主机计算机上设置，在部署 Azure 堆栈之前。 如果管理策略（例如组策略）已存在，它会覆盖此注册表设置。

在开发工具包主机上部署 Azure 堆栈，启动进入 CloudBuilder.vhdx 前后在提升的 PowerShell 窗口中运行以下脚本：

```powershell
### Get current AllowTelmetry value on DVM Host
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
### Set & Get updated AllowTelemetry value for ASDK-Host
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name "AllowTelemetry" -Value '0' # Set this value to 0,1,2,or3.  
(Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" `
-Name AllowTelemetry).AllowTelemetry
```

### <a name="asdk-and-multi-node-enable-or-disable-telemetry-after-deployment"></a>ASDK 和多节点： 启用或禁用部署后的遥测

若要在部署之后启用或禁用遥测，必须能够访问 ERCS VM 上公开的特权终结点 (PEP)。

1. 若要启用：`Set-Telemetry -Enable`
2. 若要禁用：`Set-Telemetry -Disable`

参数的详细信息：
> .PARAMETER Enable - 启用遥测数据上传</br>
> .PARAMETER Disable - 禁用遥测数据上传  

**用于启用遥测的脚本：**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Enable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

**用于禁用遥测的脚本：**

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
$psSession = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred
Invoke-Command -Session $psSession {Set-Telemetry -Disable}
if($psSession)
{
    Remove-PSSession $psSession
}
```

## <a name="next-steps"></a>后续步骤

- [下载 Azure Stack 开发工具包部署包](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

- [部署 Azure Stack 开发工具包](azure-stack-run-powershell-script.md)
