---
title: 了解如何审核虚拟机的内容
description: 了解 Azure Policy 如何使用来宾配置代理审核虚拟机内部的设置。
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: e941938fce09e8729856322a5b6572b46a3714be
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92075478"
---
# <a name="understand-azure-policys-guest-configuration"></a>了解 Azure Policy 的来宾配置

Azure 策略可以审核虚拟机中运行的计算机的设置，这二者都适用于在 Azure 和 [Arc 连接的计算机](../../../azure-arc/servers/overview.md)中运行的计算机 验证由来宾配置扩展和客户端执行。 扩展通过客户端验证设置，例如：

- 操作系统的配置
- 应用程序配置或状态
- 环境设置

目前，大多数 Azure 策略来宾配置策略定义仅审核计算机内部的设置。 它们不会应用配置。 例外情况是[下面引用的一个内置策略](#applying-configurations-using-guest-configuration)。

## <a name="enable-guest-configuration"></a>启用来宾配置

若要审核环境中计算机的状态，包括在 Azure 和 Arc 连接的计算机中的计算机，请查看以下详细信息。

## <a name="resource-provider"></a>资源提供程序

必须注册资源提供程序，之后才能使用来宾配置。 如果来宾配置策略的分配是通过门户完成的，则会自动注册资源提供程序。 可以通过[门户](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)、[Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) 或 [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) 手动注册。

## <a name="deploy-requirements-for-azure-virtual-machines"></a>部署 Azure 虚拟机的要求

为了审核计算机内部设置，需要启用[虚拟机扩展](../../../virtual-machines/extensions/overview.md)，并且计算机必须具有系统托管标识。 该扩展下载适用的策略分配和相应的配置定义。 该标识用于在计算机读取和写入来宾配置服务时对计算机进行身份验证。 已连接 Arc 的计算机不需要该扩展，因为已连接 Arc 的计算机代理中包含该扩展。

> [!IMPORTANT]
> 审核 Azure 虚拟机需要来宾配置扩展和托管标识。 若要大规模部署扩展，请分配以下策略计划：
> 
> `Deploy prerequisites to enable Guest Configuration policies on virtual machines`

### <a name="limits-set-on-the-extension"></a>对扩展设置的限制

为了限制扩展对计算机内运行的应用程序的影响，来宾配置不得超过 CPU 的5%。 对于内置和自定义的定义都存在此限制。 对于已连接 Arc 的计算机中的来宾配置服务也是如此。

### <a name="validation-tools"></a>验证工具

在计算机内，来宾配置客户端使用本地工具运行审核。

下表列出了每个受支持的操作系统上使用的本地工具。 对于内置内容，来宾配置会自动处理这些工具的加载。

|操作系统|验证工具|说明|
|-|-|-|
|Windows|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| 侧加载到仅由 Azure Policy 使用的文件夹。 不会与 Windows PowerShell DSC 冲突。 PowerShell Core 不会添加到系统路径。|
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 在默认位置安装 Chef InSpec 版本 2.2.61，并将其添加到系统路径。 还会安装 InSpec 包的依赖项，包括 Ruby 和 Python。 |

### <a name="validation-frequency"></a>验证频率

来宾配置客户端每 5 分钟检查一次新内容。 在收到来宾分配后，将按 15 分钟的时间间隔重新检查该配置的设置。 审核完成后，结果会发送到来宾配置资源提供程序。 当策略[评估触发器](../how-to/get-compliance-data.md#evaluation-triggers)执行时，会将计算机状态写入到来宾配置资源提供程序。 此更新会使 Azure Policy 评估 Azure 资源管理器属性。 按需 Azure Policy 评估从来宾配置资源提供程序检索最新值。 但是，它不会触发对计算机中的配置执行新的审核。

## <a name="supported-client-types"></a>支持的客户端类型

来宾配置策略定义包含新版本。 如果来宾配置代理不兼容，则会排除 Azure 市场中提供的旧版操作系统。 下表显示了 Azure 映像上支持的操作系统列表：

|发布者|名称|版本|
|-|-|-|
|Canonical|Ubuntu Server|14.04 及更高版本|
|Credativ|Debian|8 及更高版本|
|Microsoft|Windows Server|2012 及更高版本|
|Microsoft|Windows 客户端|Windows 10|
|OpenLogic|CentOS|7.3 及更高版本|
|Red Hat|Red Hat Enterprise Linux|7.4 - 7.8|
|Suse|SLES|12 SP3-SP5|

来宾配置策略定义支持自定义虚拟机映像，前提是它们是上表中的操作系统之一。

## <a name="network-requirements"></a>网络要求

Azure 中的虚拟机可以使用其本地网络适配器或专用链接来与来宾配置服务通信。

Azure Arc 计算机使用本地网络基础结构连接到 Azure 服务并报告符合性状态。

### <a name="communicate-over-virtual-networks-in-azure"></a>通过 Azure 中的虚拟网络进行通信

使用虚拟网络通信的虚拟机将需要在端口 `443` 上对 Azure 数据中心进行出站访问。 如果在 Azure 中使用不允许出站流量的专用虚拟网络，请使用网络安全组规则配置例外。 服务标记“GuestAndHybridManagement”可用于引用来宾配置服务。

### <a name="communicate-over-private-link-in-azure"></a>通过 Azure 中的专用链接进行通信

虚拟机可以使用 [专用链接](../../../private-link/private-link-overview.md) 来与来宾配置服务通信。 将标记应用到名称 `EnablePrivateNeworkGC` 和值 `TRUE` 以启用此功能。 在将来宾配置策略定义应用到计算机之前或之后，可以应用标记。

流量使用 Azure [虚拟公共 IP 地址](../../../virtual-network/what-is-ip-address-168-63-129-16.md) 进行路由，用 azure 平台资源建立经过身份验证的安全通道。

### <a name="azure-arc-connected-machines"></a>Azure Arc 连接的计算机

位于 Azure 的外部的节点通过 Azure Arc 连接需要连接到来宾配置服务。 有关 [Azure Arc 文档](../../../azure-arc/servers/overview.md)中提供的网络和代理要求的详细信息。

要与 Azure 中的来宾配置资源提供程序通信，计算机需要对端口**443**上的 Azure 数据中心进行出站访问。 如果 Azure 中的网络不允许出站流量，请使用[网络安全组](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)规则配置异常。 [服务标记](../../../virtual-network/service-tags-overview.md)“GuestAndHybridManagement”可用于引用来宾配置服务。

对于专用数据中心内的 Arc 连接服务器，允许使用以下模式的流量：

- 端口：只需使用 TCP 443 即可进行出站 Internet 访问
- 全局 URL：`*.guestconfiguration.azure.com`

## <a name="managed-identity-requirements"></a>托管标识要求

[部署先决条件以在虚拟机上启用 Guest Configuration 策略](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12794019-7a00-42cf-95c2-882eed337cc8)计划中的策略定义会启用系统分配的托管标识（如果不存在）。 计划中有两个管理标识创建的策略定义。 策略定义中的 IF 条件基于 Azure 中计算机资源的当前状态确保行为正确。

如果计算机当前没有任何托管标识，则有效策略将为：[\[预览版\]:添加系统分配的托管标识，在没有标识的虚拟机上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

如果计算机当前具有用户分配的系统标识，则有效策略将为：[\[预览版\]:添加系统分配的托管标识，在具有用户分配的标识的虚拟机上启用来宾配置分配](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="guest-configuration-definition-requirements"></a>来宾配置定义要求

来宾配置策略定义使用 **AuditIfNotExists** 效果。 分配定义后，后端服务会自动处理 `Microsoft.GuestConfiguration` Azure 资源提供程序中所有要求的生命周期。

在计算机上满足所有要求之前， **AuditIfNotExists** 策略定义不会返回符合性结果。 [Azure 虚拟机的部署要求](#deploy-requirements-for-azure-virtual-machines)一节中介绍了这些要求

> [!IMPORTANT]
> 在旧版来宾配置中，需要计划以合并 DeployIfNoteExists 和 AuditIfNotExists 定义 。 不再需要 DeployIfNotExists 定义。 定义和计划标记为 `[Deprecated]`，但现有分配将继续发挥作用。 有关信息，请参阅博客文章： [为来宾配置审核策略发布的重要更改](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

Azure Policy 使用来宾配置资源提供程序 complianceStatus 属性在“合规性”节点中报告合规性 。 有关详细信息，请参阅[获取符合性数据](../how-to/get-compliance-data.md)。

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>按照行业基线审核操作系统设置

Azure Policy 中的一个计划提供了按照“基线”审核操作系统设置的功能。 定义 _\[预览\]：审核不匹配 Azure 安全基线设置的 Windows VM_ 包含一组基于 Active Directory 组策略的规则。

大多数设置都可用作参数。 参数允许你自定义要审核的内容。
根据你的要求调整策略，或将策略映射到第三方信息（如行业监管标准）。

某些参数支持整数值范围。 例如，“密码最长期限”设置可以审核有效组策略设置。 “1,70”范围将确认用户必须至少每 70 天更改一次密码，但不得少于一天。

如果使用 Azure 资源管理器模板（ARM 模板）分配策略，请使用参数文件管理异常。 将文件签入到版本控制系统（如 Git）。 有关文件更改的注释证明了赋值为何是预期值的例外情况。

#### <a name="applying-configurations-using-guest-configuration"></a>使用来宾配置应用配置

只有“在 Windows 计算机上配置时区”这一定义通过配置时区对计算机进行更改。 不支持用于在计算机内配置设置的自定义策略定义。

分配以“配置”开头的定义时，还必须分配定义“部署必备组件以在 Windows VM 上启用来宾配置策略”。 如果需要，可将这些定义合并到一个计划中。

> [!NOTE]
> 内置时区策略是唯一支持在计算机内配置设置的定义，并且不支持配置计算机内部设置的自定义策略定义。

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>将策略分配给 Azure 之外的计算机

适用于来宾配置的审核策略定义包括 **HybridCompute/计算机** 资源类型。 将自动包括载入到策略分配范围内的 [Azure Arc for servers](../../../azure-arc/servers/overview.md) 的任何计算机。

### <a name="multiple-assignments"></a>多个分配

来宾配置策略定义目前仅支持对每台计算机分配相同的来宾分配，即使策略分配使用不同的参数也是如此。

## <a name="client-log-files"></a>客户端日志文件

来宾配置扩展将日志文件写入以下位置：

Windows： `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux：`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

其中 `<version>` 指的是当前版本号。

### <a name="collecting-logs-remotely"></a>远程收集日志

对来宾配置相关配置或模块进行故障排除的第一步应该是使用 `Test-GuestConfigurationPackage` cmdlet，具体步骤请参阅[如何为 Windows 创建自定义来宾配置审核策略](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)。
如果未成功，则收集客户端日志有助于诊断问题。

#### <a name="windows"></a>Windows

使用 [Azure VM 运行命令](../../../virtual-machines/windows/run-command.md)从日志文件中捕获信息，下面的示例 PowerShell 脚本可能会很有帮助。

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

使用 [Azure VM 运行命令](../../../virtual-machines/linux/run-command.md)从日志文件中捕获信息，下面的示例 Bash 脚本可能会很有帮助。

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>来宾配置示例

来宾配置内置策略示例在以下位置提供：

- [内置策略定义 - 来宾配置](../samples/built-in-policies.md#guest-configuration)
- [内置计划 - 来宾配置](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy 示例 GitHub 存储库](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>后续步骤

- 了解如何从[来宾配置符合性视图](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)中查看每个设置的详细信息
- 在 [Azure Policy 示例](../samples/index.md)中查看示例。
- 查看 [Azure Policy 定义结构](./definition-structure.md)。
- 查看[了解策略效果](./effects.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取符合性数据](../how-to/get-compliance-data.md)。
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
