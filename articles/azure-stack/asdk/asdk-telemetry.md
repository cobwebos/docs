---
title: "Azure 堆栈遥测 |Microsoft 文档"
description: "描述如何配置使用 PowerShell 的 Azure 堆栈遥测设置。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: d48b6a02666348f2ef7c1b2a73982d219c79bf54
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/17/2018
---
# <a name="azure-stack-telemetry"></a>Azure 堆栈遥测

Azure 堆栈系统数据或遥测，会自动上载到 Microsoft 通过连接的用户体验。 Microsoft 团队主要是为了提高我们客户体验，以及对安全、 运行状况、 质量和性能分析使用从 Azure 堆栈遥测收集的数据。

作为 Azure 堆栈操作员，遥测可以提供有价值见解企业部署，并为你提供可帮助形状以后版本的 Azure 堆栈的声音。

> [!NOTE]
> 计费之前，还可以将 azure 堆栈配置为到 Azure 的正向使用情况信息。 这是必需的多节点 Azure 堆栈用户选择为你的使用付费计费。 使用情况报告遥测从独立控制，不需要为选择的容量模型的多节点客户还是 Azure 堆栈开发工具包用户。 对于这些方案中，使用情况报告可关闭[使用注册脚本](https://docs.microsoft.com/azure/azure-stack/azure-stack-usage-reporting)。 

上的 Windows Server 2016 连接用户体验和遥测的组件，它使用基于 azure 的堆栈遥测[事件跟踪的 Windows (ETW)](https://msdn.microsoft.com/library/dn904632(v=vs.85).aspx)跟踪日志记录技术，用于收集和存储遥测事件和数据。 Azure 堆栈组件使用相同的日志记录技术来发布事件和通过使用公共操作系统事件日志记录和跟踪 Api 收集的数据。 Azure 堆栈组件的示例包括网络资源提供程序、 存储资源提供程序、 监视资源提供程序中，和更新资源提供程序。 连接的用户体验和遥测组件对数据使用 SSL 进行加密，并使用固定证书通过 HTTPS 的遥测数据传输到 Microsoft 数据管理服务。

> [!NOTE]
> 若要支持遥测数据流，必须在你的网络中打开端口 443 (HTTPS)。 连接的用户体验和遥测组件连接到 Microsoft 数据管理服务在https://v10.vortex-win.data.microsoft.com。连接的用户体验和遥测组件还链接到https://settings-win.data.microsoft.com下载配置信息。

## <a name="privacy-considerations"></a>隐私注意事项
ETW 服务将返回到受保护的云存储路由遥测数据。 遥测数据的最小特权指南访问权限原则。 仅 Microsoft 人员具有有效的业务需要允许对遥测数据的访问。 Microsoft 客户的决定中所述的有限目的; 二是不共享个人数据与第三方客户的除[Azure 堆栈隐私声明](http://windows.microsoft.com/windows/preview-privacy-statement)。 我们并与 Oem 和包含聚合的匿名信息的遥测信息的合作伙伴共享业务报表。 内部 Microsoft 团队包括隐私、 法律和数据管理利益干系人进行共享决策的数据。

Microsoft 认为中和实践信息最小化。 我们努力收集仅和的信息，我们需要我们将其存储为仅只要它需要提供服务或分析。 很多有关如何工作的 Azure 堆栈系统和 Azure 服务的信息是在六个月内删除。 汇总或聚合的数据保留更长一段。

我们了解隐私和安全的客户的信息非常重要。 我们已经仔细和全面的方法与客户隐私和与 Azure 堆栈的客户数据的保护。 IT 管理员可以控制以在任何时候自定义功能和隐私设置。 我们致力于透明度和信任十分明显：
- 我们已打开与客户有关我们收集到的数据类型。
- 我们将在控件中的企业客户-它们可以自定义他们自己的隐私设置。
- 首先我们将客户隐私和安全性。
- 我们是有关如何使用遥测获取透明。
- 我们使用遥测来改善客户体验。

Microsoft 不会收集敏感信息，如信用卡号、 用户名和密码、 电子邮件地址或其他同样敏感信息。 如果我们确定已无意中收到敏感信息，我们将其删除。

## <a name="examples-of-how-microsoft-uses-the-telemetry-data"></a>Microsoft 使用遥测数据的方式的示例
遥测发挥重要作用，帮助我们能够快速确定并修复客户部署和配置中的关键可靠性问题。 深入了解我们收集的遥测数据帮助我们快速确定与服务或硬件配置问题。 Microsoft 的能够从客户和到生态系统的驱动器改进获取此数据可帮助提高我们集成的 Azure 堆栈解决方案的质量的门槛。 

遥测还有助于 Microsoft 能够更好地了解客户部署组件的方式、 使用的功能，并使用服务来实现业务目标。 可帮助确定该数据中获取信息的优先级客户体验和工作负荷会直接影响的区域中的工程投资。

一些示例包括客户使用情况的容器、 存储和与 Azure 堆栈角色相关联的网络配置。 我们还使用到驱动器改进和智能到我们的管理和监视解决方案的一些见解。 这可帮助客户诊断质量问题并保存通过使更少的支持的 money 调用到 Microsoft。

## <a name="manage-telemetry-collection"></a>管理遥测数据收集
我们不建议关闭遥测组织中根据遥测提供驱动器改进的产品功能和稳定性的数据。 我们知道，但是，，在某些情况下这可能有必要。 

在这些情况下，你可以配置通过使用注册表设置预部署或使用遥测终结点后期部署发送给 Microsoft 的遥测级别。

### <a name="set-telemetry-level-in-the-windows-registry"></a>在 Windows 注册表中设置遥测级别
Windows 注册表编辑器用于部署 Azure 堆栈之前的物理主机计算机上手动设置遥测级别。 如果管理策略已存在，如组策略，它将重写此注册表设置。 

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

遥测级别是累积和分类划分为四个级别 (0-3):

**0 （安全性）**。 仅限安全数据。 信息将帮助保持操作系统所需安全，包括有关连接的用户体验和遥测组件设置和 Windows Defender 的数据。 在此级别发出没有 Azure 堆栈特定遥测。

**1 （基本）**。 安全数据和基本运行状况和质量的数据。 基本设备信息，包括： 质量相关数据、 应用程序兼容性，应用程序使用情况数据和中的安全级别的数据。 将遥测级别设置为基本启用 Azure 堆栈遥测。 在此级别收集的数据包括：

- **基本的设备信息**有助于提供了解类型和生态系统中的本机和虚拟化 Windows Server 2016 实例的配置包括：
 - 机属性，例如 OEM，模型， 
 - 网络属性，例如的数量和速度的网络适配器
 - 处理器和内存属性，例如内核数，内存大小 
 - 存储特性，如驱动器、 类型和大小的数量。
- **遥测功能**，包括 %的已上载的事件、 已删除的事件和上次上载时间。
- **质量相关信息**，它可帮助 Microsoft 开发 Azure 堆栈的执行方式的基本了解。 一个示例是在特定硬件配置的严重警报的计数。
- * * 兼容性数据，它有助于提供的了解哪些资源提供程序系统和虚拟机上安装并标识潜在的兼容性问题。

**2 （增强）**。 其他见解，包括： 如何使用操作系统和其他 Azure 堆栈服务、 如何执行方法、 高级的可靠性数据和从基本和安全级别的数据。 

**3 （完整）**。 所有识别并帮助解决问题，所需的数据以及从数据**安全**，**基本**，和**增强**级别。

> [!NOTE]
> 默认遥测级别值为 2 （增强型）。

关闭 Windows 和 Azure 堆栈遥测禁用 SQL 遥测。 有关 Windows Server 遥测设置的影响的其他信息，引用[Windows 遥测白皮书](https://aka.ms/winservtelemetry)。 

> [!IMPORTANT]
> 这些遥测级别仅适用于 Microsoft Azure 堆栈组件。 非 Microsoft 软件组件和从 Azure 堆栈硬件合作伙伴的硬件生命周期主机中运行的服务可能会超过这些遥测水平与其云服务进行通信。 你应使用 Azure 堆栈硬件解决方案供应商以了解其遥测策略，以及如何选择加入或退出。 

### <a name="enable-or-disable-telemetry-after-deployment"></a>启用或禁用部署后的遥测

若要启用或禁用遥测部署后，你需要能够访问到特权终结点 (PEP) ERCS Vm 上公开。
1.  若要启用： `Set-Telemetry -Enable`
2.  若要禁用： `Set-Telemetry -Disable`

参数的详细信息： 
> .参数启用-启用遥测数据上载 

> .参数禁用-关闭遥测数据上载  

**若要启用遥测的脚本：**
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

**若要禁用遥测的脚本：**
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
[添加应用商店项](asdk-marketplace-item.md)

