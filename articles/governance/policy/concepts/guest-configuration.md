---
title: Learn to audit the contents of virtual machines
description: Learn how Azure Policy uses the Guest Configuration agent to audit settings inside virtual machines.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: f68bbc64ee8f0da02d213895a70e4c533b9a5f63
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463790"
---
# <a name="understand-azure-policys-guest-configuration"></a>了解 Azure Policy 的来宾配置

Beyond auditing and [remediating](../how-to/remediate-resources.md) Azure resources, Azure Policy can audit settings inside a machine. 验证由来宾配置扩展和客户端执行。 扩展通过客户端验证设置，例如：

- The configuration of the operating system
- 应用程序配置或状态
- 环境设置

目前，Azure Policy 来宾配置仅审核计算机中的设置。 它不应用配置。

## <a name="extension-and-client"></a>扩展和客户端

To audit settings inside a machine, a [virtual machine extension](../../../virtual-machines/extensions/overview.md) is enabled. 该扩展下载适用的策略分配和相应的配置定义。

### <a name="limits-set-on-the-extension"></a>Limits set on the extension

To limit the extension from impacting applications running inside the machine, the Guest Configuration isn't allowed to exceed more than 5% of CPU utilization. This limitation exists for both built-in and custom definitions.

## <a name="register-guest-configuration-resource-provider"></a>注册来宾配置资源提供程序

必须注册资源提供程序，之后才能使用来宾配置。 可以通过门户或通过 PowerShell 注册。 The resource provider is registered automatically if assignment of a Guest Configuration policy is done through the portal.

### <a name="registration---portal"></a>注册 - 门户

若要通过 Azure 门户注册资源提供程序的来宾配置，请按照下列步骤操作：

1. 启动 Azure 门户，单击“所有服务”。 搜索并选择“订阅”。

1. 找到并单击要启用来宾配置的订阅。

1. 在“订阅”页的左侧菜单中，单击“资源提供程序”。

1. 筛选或滚动直至找到“Microsoft.GuestConfiguration”，然后在同一行上单击“注册”。

### <a name="registration---powershell"></a>注册 - PowerShell

若要通过 PowerShell 注册资源提供程序的来宾配置，请运行以下命令：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>验证工具

Inside the machine, the Guest Configuration client uses local tools to run the audit.

下表显示了每个受支持操作系统上本地工具的列表：

|操作系统|验证工具|说明|
|-|-|-|
|Windows|[Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby 和 Python 由来宾配置扩展安装。 |

### <a name="validation-frequency"></a>验证频率

来宾配置客户端每 5 分钟检查一次新内容。 在收到来宾分配后，将按 15 分钟的时间间隔检查设置。 在审核完成后，结果会立即发送到来宾配置资源提供程序。 当策略[评估触发器](../how-to/get-compliance-data.md#evaluation-triggers)执行时，会将计算机状态写入到来宾配置资源提供程序。 This update causes Azure Policy to evaluate the Azure Resource Manager properties. An on-demand Azure Policy evaluation retrieves the latest value from the Guest Configuration resource provider. However, it doesn't trigger a new audit of the configuration within the machine.

## <a name="supported-client-types"></a>支持的客户端类型

下表显示了 Azure 映像上支持的操作系统列表：

|发布者|名称|版本|
|-|-|-|
|Canonical|Ubuntu Server|14.04、16.04、18.04|
|Credativ|Debian|8、9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Windows 客户端|Windows 10|
|OpenLogic|CentOS|7.3、7.4、7.5|
|Red Hat|Red Hat Enterprise Linux|7.4、7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> Guest Configuration can audit nodes running a supported OS. If you would like to audit virtual machines that use a custom image, you need to duplicate the **DeployIfNotExists** definition and modify the **If** section to include your image properties.

### <a name="unsupported-client-types"></a>不支持的客户端类型

Windows Server Nano Server isn't supported in any version.

## <a name="guest-configuration-extension-network-requirements"></a>Guest Configuration Extension network requirements

To communicate with the Guest Configuration resource provider in Azure, machines require outbound access to Azure datacenters on port **443**. If you're using a private virtual network in Azure that doesn't allow outbound traffic, configure exceptions with [Network Security Group](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) rules. A service tag doesn't currently exist for Azure Policy Guest Configuration.

For IP address lists, you can download [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653). 此文件每周更新，包含当前部署的范围以及即将对 IP 范围进行的更新。 You only need to allow outbound access to the IPs in the regions where your VMs are deployed.

> [!NOTE]
> Azure 数据中心 IP 地址 XML 文件列出了 Microsoft Azure 数据中心使用的 IP 地址范围。 文件中包含计算、SQL 和存储范围。 每周都将发布更新的文件。 该文件反映当前已部署的范围和任何即将对 IP 范围进行的更改。 数据中心至少在一周后才会使用文件中显示的新范围。 建议每周下载新的 XML 文件。 然后，更新网站以正确地标识 Azure 中运行的服务。 Azure ExpressRoute 用户应注意，此文件过去经常在每个月的第一周更新 Azure 空间的边界网关协议 (BGP) 播发。

## <a name="guest-configuration-definition-requirements"></a>来宾配置定义要求

Each audit run by Guest Configuration requires two policy definitions, a **DeployIfNotExists** definition and an **AuditIfNotExists** definition. The **DeployIfNotExists** definition is used to prepare the machine with the Guest Configuration agent and other components to support the [validation tools](#validation-tools).

“DeployIfNotExists”策略定义验证并更正以下项目：

- Validate the machine has been assigned a configuration to evaluate. If no assignment is currently present, get the assignment and prepare the machine by:
  - Authenticating to the machine using a [managed identity](../../../active-directory/managed-identities-azure-resources/overview.md)
  - 安装 Microsoft.GuestConfiguration 扩展的最新版本
  - 安装[验证工具](#validation-tools)和依赖项（如果需要）

If the **DeployIfNotExists** assignment is Non-compliant, a [remediation task](../how-to/remediate-resources.md#create-a-remediation-task) can be used.

Once the **DeployIfNotExists** assignment is Compliant, the **AuditIfNotExists** policy assignment uses the local validation tools to determine if the configuration assignment is Compliant or Non-compliant. 验证工具向来宾配置客户端提供结果。 客户端将结果转发给来宾扩展，使其可通过来宾配置资源提供程序使用。

Azure Policy 使用来宾配置资源提供程序 complianceStatus 属性在“符合性”节点中报告符合性。 有关详细信息，请参阅[获取符合性数据](../how-to/get-compliance-data.md)。

> [!NOTE]
> The **DeployIfNotExists** policy is required for the **AuditIfNotExists** policy to return results. Without the **DeployIfNotExists**, the **AuditIfNotExists** policy shows "0 of 0" resources as status.

来宾配置的所有内置策略包含在一个计划内，以对分配中使用的定义分组。 The built-in initiative named _\[Preview\]: Audit Password security settings inside Linux and Windows machines_ contains 18 policies. 对于 Windows 有六个 DeployIfNotExists 和 AuditIfNotExists 对，对于 Linux 有三个对。 The [policy definition](definition-structure.md#policy-rule) logic validates that only the target operating system is evaluated.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditing operating system settings following industry baselines

One of the initiatives available in Azure Policy provides the ability to audit operating system settings inside virtual machines following a "baseline" from Microsoft. The definition, _\[Preview\]: Audit Windows VMs that do not match Azure security baseline settings_ includes a complete set of audit rules based on settings from Active Directory Group Policy.

Most of the settings are available as parameters. This functionality allows you to customize what is audited to align the policy with your organizational requirements or to map the policy to third party information such as industry regulatory standards.

Some parameters support an integer value range. For example, the Maximum Password Age parameter can be set using a range operator to give flexibility to machine owners. You could audit that the effective Group Policy setting requiring users to change their passwords should be no more than 70 days, but shouldn't be less than one day. As described in the info-bubble for the parameter, to make this business policy the effective audit value, set the value to "1,70".

If you assign the policy using an Azure Resource Manager deployment template, you can use a parameters file to manage these settings from source control. Using a tool such as Git to manage changes to Audit policies with comments at each check-in documents evidence as to why an assignment should be an exception to the expected value.

#### <a name="applying-configurations-using-guest-configuration"></a>Applying configurations using Guest Configuration

The latest feature of Azure Policy configures settings inside machines. The definition _Configure the time zone on Windows machines_ makes changes to the machine by configuring the time zone.

When assigning definitions that begin with _Configure_, you must also assign the definition _Deploy prerequisites to enable Guest Configuration Policy on Windows VMs_. You can combine these definitions in an initiative if you choose.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Assigning policies to machines outside of Azure

The Audit policies available for Guest Configuration include the **Microsoft.HybridCompute/machines** resource type. Any machines onboarded to [Azure Arc for Servers](../../../azure-arc/servers/overview.md) that are in the scope of the policy assignment are automatically included.

### <a name="multiple-assignments"></a>Multiple assignments

Guest Configuration policies currently only support assigning the same Guest Assignment once per machine, even if the Policy assignment uses different parameters.

## <a name="built-in-resource-modules"></a>Built-in resource modules

When installing the Guest Configuration extension, the 'GuestConfiguration' PowerShell module is included with the latest version of DSC resource modules. This module can be downloaded from the PowerShell Gallery by using the 'Manual Download' link from the module page [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). The '.nupkg' file format can be renamed to '.zip' to uncompress and review.

## <a name="client-log-files"></a>Client log files

The Guest Configuration extension writes log files to the following locations:

Windows：`C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux：`/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Where `<version>` refers to the current version number.

### <a name="collecting-logs-remotely"></a>Collecting logs remotely

The first step in troubleshooting Guest Configuration configurations or modules should be to use the `Test-GuestConfigurationPackage` cmdlet following the steps in [Test a Guest Configuration package](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).
If that isn't successful, collecting client logs can help diagnose issues.

#### <a name="windows"></a>Windows

To use the Azure VM Run Command capability to capture information from log files in Windows machines, the following example PowerShell script can be helpful. For more information, see [Run PowerShell scripts in your Windows VM with Run Command](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

To use the Azure VM Run Command capability to capture information from log files in Linux machines, the following example Bash script can be helpful. For more information, see [Run shell scripts in your Linux VM with Run Command](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Guest Configuration samples

Samples for Policy Guest Configuration are available in the following locations:

- [Samples index - Guest Configuration](../samples/index.md#guest-configuration)
- [Azure Policy samples GitHub repo](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)

## <a name="next-steps"></a>后续步骤

- Review examples at [Azure Policy samples](../samples/index.md).
- 查看 [Azure Policy 定义结构](definition-structure.md)。
- 查看[了解策略效果](effects.md)。
- Understand how to [programmatically create policies](../how-to/programmatically-create.md).
- Learn how to [get compliance data](../how-to/get-compliance-data.md).
- Learn how to [remediate non-compliant resources](../how-to/remediate-resources.md).
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/overview.md)，了解什么是管理组。
