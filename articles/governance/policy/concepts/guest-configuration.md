---
title: 了解如何审核虚拟机的内容
description: 了解 Azure Policy 如何使用来宾配置代理审核虚拟机内部的设置。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 889e99e94b2c81a6654fcbe7851e93c40163a0c6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985314"
---
# <a name="understand-azure-policys-guest-configuration"></a>了解 Azure Policy 的来宾配置

除了审核和[修正](../how-to/remediate-resources.md) Azure 资源以外，Azure Policy 还可以审核计算机内部的设置。 验证由来宾配置扩展和客户端执行。 扩展通过客户端验证设置，例如：

- 操作系统的配置
- 应用程序配置或状态
- 环境设置

此时，大多数 Azure 策略来宾配置策略仅审核计算机内的设置。 它们不应用配置。 例外情况是[下面引用](#applying-configurations-using-guest-configuration)的一个内置策略。

## <a name="extension-and-client"></a>扩展和客户端

为了审核计算机内部的设置，将会启用一个[虚拟机扩展](../../../virtual-machines/extensions/overview.md)。 该扩展下载适用的策略分配和相应的配置定义。

### <a name="limits-set-on-the-extension"></a>在扩展中设置的限制

为了限制该扩展对计算机内部运行的应用程序造成影响，将不允许 Guest Configuration 的 CPU 利用率超过 5%。 内置定义和自定义定义都存在此限制。

## <a name="register-guest-configuration-resource-provider"></a>注册来宾配置资源提供程序

必须注册资源提供程序，之后才能使用来宾配置。 可以通过门户或通过 PowerShell 注册。 如果 Guest Configuration 策略的分配是通过门户完成的，则会自动注册资源提供程序。

### <a name="registration---portal"></a>注册 - 门户

若要通过 Azure 门户注册资源提供程序的来宾配置，请按照下列步骤操作：

1. 启动 Azure 门户，单击“所有服务”****。 搜索并选择“订阅”****。

1. 找到并单击要启用来宾配置的订阅。

1. 在“订阅”**** 页的左侧菜单中，单击“资源提供程序”****。

1. 筛选或滚动直至找到“Microsoft.GuestConfiguration”****，然后在同一行上单击“注册”****。

### <a name="registration---powershell"></a>注册 - PowerShell

若要通过 PowerShell 注册资源提供程序的来宾配置，请运行以下命令：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>验证工具

在计算机中，Guest Configuration 客户端使用本地工具运行审核。

下表显示了每个受支持操作系统上本地工具的列表：

|操作系统|验证工具|说明|
|-|-|-|
|Windows|[Windows PowerShell Desired State Configuration ](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| 如果 Ruby 和 Python 不在计算机上，则它们由来宾配置扩展安装。 |

### <a name="validation-frequency"></a>验证频率

来宾配置客户端每 5 分钟检查一次新内容。 收到来宾分配后，该配置的设置将每隔 15 分钟重新检查。
审核完成后，结果将发送给来宾配置资源提供程序。 当策略[评估触发器](../how-to/get-compliance-data.md#evaluation-triggers)执行时，会将计算机状态写入到来宾配置资源提供程序。 此项更新会导致 Azure Policy 评估 Azure 资源管理器属性。 按需 Azure Policy 评估从 Guest Configuration 资源提供程序检索最新值。 但是，它不会触发对计算机中的配置执行新的审核。

## <a name="supported-client-types"></a>支持的客户端类型

下表显示了 Azure 映像上支持的操作系统列表：

|发布者|名称|版本|
|-|-|-|
|Canonical|Ubuntu Server|14.04、16.04、18.04|
|Credativ|Debian|8、9|
|Microsoft|Windows Server|2012 Datacenter、2012 R2 Datacenter、2016 Datacenter、2019 Datacenter|
|Microsoft|Windows 客户端|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5, 7.6, 7.7|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6, 7.7|
|Suse|SLES|12 SP3|

### <a name="unsupported-client-types"></a>不支持的客户端类型

任何版本都不支持 Windows Server Nano Server。

## <a name="guest-configuration-extension-network-requirements"></a>Guest Configuration 扩展网络要求

若要与 Azure 中的 Guest Configuration 资源提供程序通信，计算机需要对端口 **443** 上的 Azure 数据中心拥有出站访问权限。 如果在 Azure 中使用不允许出站流量的专用虚拟网络，请使用[网络安全组](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)规则配置例外。
[服务标签](../../../virtual-network/service-tags-overview.md)"来宾和混合管理"可用于引用来宾配置服务。

## <a name="guest-configuration-definition-requirements"></a>来宾配置定义要求

Guest Configuration 运行的每个审核需要两个策略定义：**DeployIfNotExists** 定义和 **AuditIfNotExists** 定义。 **DeployIfNotExists** 定义用于在计算机上准备 Guest Configuration 代理，以及其他用于支持[验证工具](#validation-tools)的组件。

“DeployIfNotExists”策略定义验证并更正以下项目****：

- 验证是否为计算机分配了要评估的配置。 如果当前不存在任何分配，请通过以下方式获取分配并准备计算机：
  - 使用[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)对计算机进行身份验证
  - 安装 Microsoft.GuestConfiguration**** 扩展的最新版本
  - 安装[验证工具](#validation-tools)和依赖项（如果需要）

如果 **DeployIfNotExists ** 分配不合规，可以使用[修正任务](../how-to/remediate-resources.md#create-a-remediation-task)。

一旦 **DeployIfNotExists** 分配合规，**AuditIfNotExists** 策略分配就会使用本地验证工具来确定配置分配是合规还是不合规。 验证工具向来宾配置客户端提供结果。 客户端将结果转发给来宾扩展，使其可通过来宾配置资源提供程序使用。

Azure Policy 使用来宾配置资源提供程序 complianceStatus**** 属性在“符合性”**** 节点中报告符合性。 有关详细信息，请参阅[获取符合性数据](../how-to/get-compliance-data.md)。

> [!NOTE]
> 需有 **DeployIfNotExists** 策略，才能让 **AuditIfNotExists ** 策略返回结果。 如果没有 **DeployIfNotExists**，**AuditIfNotExists** 策略会将状态显示为“第 0 个，共 0 个”资源。

来宾配置的所有内置策略包含在一个计划内，以对分配中使用的定义分组。 名为_\["预览\]：审核 Linux 和 Windows 计算机内密码安全设置_"的内置计划包含 18 个策略。 对于 Windows 有六个 DeployIfNotExists**** 和 AuditIfNotExists**** 对，对于 Linux 有三个对。 [策略定义](definition-structure.md#policy-rule)逻辑将验证是否只评估目标操作系统。

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>根据行业基线审核操作系统设置

Azure Policy 中的某个计划提供根据 Microsoft“基线”审核虚拟机中的操作系统设置的功能。 定义：_\[预览\]：审核与 Azure 安全基线设置不匹配_的 Windows VM 包括一组完整的审核规则，这些规则基于活动目录组策略的设置。

大多数设置以参数的形式提供。 此功能允许您自定义审核的内容，以使策略与组织要求保持一致，或者将策略映射到第三方信息（如行业监管标准）。

某些参数支持整数值范围。 例如，可以使用范围运算符设置“最长密码期限”参数，以便为计算机所有者提供灵活性。 可以审核生效的“组策略”设置是否要求用户在 70 天之内，但至少在一天之后更改其密码。 如参数的信息泡泡中所述，若要为此业务策略设置有效的审核值，请将值设置为“1,70”。

如果使用 Azure 资源管理器部署模板分配策略，则可以使用参数文件从源代码管理管理这些设置。 使用 Git 之类的工具管理对审核策略的更改，并在每次签入时提供备注，以阐述为何不能将某个赋值用作预期值的理由。

#### <a name="applying-configurations-using-guest-configuration"></a>使用 Guest Configuration 应用配置

Azure Policy 的最新功能可以配置计算机内部的设置。 定义“在 Windows 计算机上配置时区”通过配置时区对计算机进行更改。__

分配以“配置”开头的定义时，还必须分配定义“部署必备组件以在 Windows VM 上启用 Guest Configuration 策略”。____ 如果需要，可将这些定义合并到一个计划中。

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>将策略分配给 Azure 以外的计算机

可用于来宾配置的审核策略包括**Microsoft.混合计算/计算机**资源类型。 自动包含在策略分配范围内的服务器 Azure [Arc](../../../azure-arc/servers/overview.md)上的任何计算机。

### <a name="multiple-assignments"></a>多个分配

Guest Configuration 策略目前仅支持为每台计算机分配相同的来宾分配，即使策略分配使用不同的参数，也是如此。

## <a name="client-log-files"></a>客户端日志文件

Guest Configuration 扩展将日志文件写入以下位置：

Windows：`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux：`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

其中 `<version>` 表示当前版本号。

### <a name="collecting-logs-remotely"></a>远程收集日志

排除来宾配置或模块的故障排除的第一步应该是使用`Test-GuestConfigurationPackage`cmdlet，按照如何为 Windows[创建自定义来宾配置审核策略](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows)的步骤。
如果这种做法无效，收集客户端日志可能会有助于诊断问题。

#### <a name="windows"></a>Windows

若要使用 Azure VM 的“运行命令”功能从 Windows 计算机中的日志文件捕获信息，可以参考以下示例 PowerShell 脚本。 有关详细信息，请参阅[使用“运行命令”在 Windows VM 中运行 PowerShell 脚本](../../../virtual-machines/windows/run-command.md)。

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

若要使用 Azure VM 的“运行命令”功能从 Linux 计算机中的日志文件捕获信息，可以参考以下示例 Bash 脚本。 有关详细信息，请参阅[使用“运行命令”在 Linux VM 中运行 shell 脚本](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Guest Configuration 示例

策略来宾配置内置计划的源可在以下位置提供：

- [内置策略定义 - 来宾配置](../samples/built-in-policies.md#guest-configuration)
- [内置计划 - 访客配置](../samples/built-in-initiatives.md#guest-configuration)
- [GitHub 存储库中的 Azure Policy 示例](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>后续步骤

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 查看 [Azure Policy 定义结构](definition-structure.md)。
- 查看[了解策略效果](effects.md)。
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取合规性数据](../how-to/get-compliance-data.md)。
- 了解如何[修复不合规资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
