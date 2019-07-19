---
title: 了解如何审核虚拟机的内容
description: 了解 Azure Policy 如何使用来宾配置审核 Azure 虚拟机内部的设置。
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 74e36d944450e1ce2c61481b2cb7e345860212af
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326884"
---
# <a name="understand-azure-policys-guest-configuration"></a>了解 Azure Policy 的来宾配置

除了审核和[修正](../how-to/remediate-resources.md)azure 资源以外, azure 策略还可以审核虚拟机内的设置。 验证由来宾配置扩展和客户端执行。 扩展通过客户端验证设置，例如操作系统的配置、应用程序配置或状态以及环境设置等等。

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>扩展和客户端

为审核虚拟机内部的设置，已启用[虚拟机扩展](../../../virtual-machines/extensions/overview.md)。 该扩展下载适用的策略分配和相应的配置定义。

### <a name="register-guest-configuration-resource-provider"></a>注册来宾配置资源提供程序

必须注册资源提供程序，之后才能使用来宾配置。 可以通过门户或通过 PowerShell 注册。 如果来宾配置策略的分配是通过门户完成的, 则会自动注册资源提供程序。

#### <a name="registration---portal"></a>注册 - 门户

若要通过 Azure 门户注册资源提供程序的来宾配置，请按照下列步骤操作：

1. 启动 Azure 门户，单击“所有服务”  。 搜索并选择“订阅”  。

1. 找到并单击要启用来宾配置的订阅。

1. 在“订阅”  页的左侧菜单中，单击“资源提供程序”  。

1. 筛选或滚动直至找到“Microsoft.GuestConfiguration”  ，然后在同一行上单击“注册”  。

#### <a name="registration---powershell"></a>注册 - PowerShell

若要通过 PowerShell 注册资源提供程序的来宾配置，请运行以下命令：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>验证工具

在虚拟机内，来宾配置客户端使用本地工具运行审核。

下表显示了每个受支持操作系统上本地工具的列表：

|操作系统|验证工具|说明|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby 和 Python 由来宾配置扩展安装。 |

### <a name="validation-frequency"></a>验证频率

来宾配置客户端每 5 分钟检查一次新内容。 在收到来宾分配后，将按 15 分钟的时间间隔检查设置。 在审核完成后，结果会立即发送到来宾配置资源提供程序。 当策略[评估触发器](../how-to/get-compliance-data.md#evaluation-triggers)执行时，会将计算机状态写入到来宾配置资源提供程序。 这会导致 Azure Policy 评估 Azure 资源管理器属性。 按需 Azure 策略评估检索来宾配置资源提供程序中的最新值。 但是，它不会触发对虚拟机中的配置执行新的审核。

### <a name="supported-client-types"></a>支持的客户端类型

下表显示了 Azure 映像上支持的操作系统列表：

|发布者|名称|版本|
|-|-|-|
|Canonical|Ubuntu Server|14.04、16.04、18.04|
|Credativ|Debian|8、9|
|Microsoft|Windows Server|2012 datacenter、2012 R2 Datacenter、2016 Datacenter、2019 Datacenter|
|Microsoft|Windows 客户端|Windows 10|
|OpenLogic|CentOS|7.3、7.4、7.5|
|Red Hat|Red Hat Enterprise Linux|7.4、7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> 来宾配置可以审核运行受支持的操作系统的节点。 如果你想要审核使用自定义映像的虚拟机, 则需要复制**DeployIfNotExists**定义, 并修改**If**部分以包括你的映像属性。

### <a name="unsupported-client-types"></a>不支持的客户端类型

任何版本都不支持 Windows Server Nano Server。

### <a name="guest-configuration-extension-network-requirements"></a>来宾配置扩展网络要求

要与 Azure 中的来宾配置资源提供程序通信, 虚拟机需要对端口**443**上的 Azure 数据中心的出站访问权限。 如果你使用的是 Azure 中的专用虚拟网络, 并且不允许出站流量, 则必须使用[网络安全组](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)规则配置例外。 目前, Azure 策略来宾配置不存在服务标记。

对于 IP 地址列表, 你可以下载[Microsoft Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)。 此文件每周更新，包含当前部署的范围以及即将对 IP 范围进行的更新。 只需允许对部署了 Vm 的区域中的 Ip 进行出站访问。

> [!NOTE]
> Azure 数据中心 IP 地址 XML 文件列出了 Microsoft Azure 数据中心使用的 IP 地址范围。 文件中包含计算、SQL 和存储范围。 每周都将发布更新的文件。 该文件反映当前已部署的范围和任何即将对 IP 范围进行的更改。 数据中心至少在一周后才会使用文件中显示的新范围。 建议每周下载新的 XML 文件。 然后，更新网站以正确地标识 Azure 中运行的服务。 Azure ExpressRoute 用户应注意，此文件过去经常在每个月的第一周更新 Azure 空间的边界网关协议 (BGP) 播发。

## <a name="guest-configuration-definition-requirements"></a>来宾配置定义要求

每个按来宾配置审核运行都需要两个策略定义: **DeployIfNotExists**定义和**审核**定义。 **DeployIfNotExists**定义用于通过来宾配置代理和其他组件准备虚拟机, 以支持[验证工具](#validation-tools)。

“DeployIfNotExists”策略定义验证并更正以下项目  ：

- 验证虚拟机已分配要评估的配置。 如果当前不存在任何分配，则获取分配并通过以下操作准备虚拟机：
  - 使用[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)对虚拟机进行身份验证
  - 安装 Microsoft.GuestConfiguration  扩展的最新版本
  - 安装[验证工具](#validation-tools)和依赖项（如果需要）

如果**DeployIfNotExists**分配不符合, 则可以使用[补救任务](../how-to/remediate-resources.md#create-a-remediation-task)。

一旦**DeployIfNotExists**分配符合,**审核**策略分配将使用本地验证工具来确定配置分配是符合还是不符合。
验证工具向来宾配置客户端提供结果。 客户端将结果转发给来宾扩展，使其可通过来宾配置资源提供程序使用。

Azure Policy 使用来宾配置资源提供程序 complianceStatus  属性在“符合性”  节点中报告符合性。 有关详细信息，请参阅[获取符合性数据](../how-to/getting-compliance-data.md)。

> [!NOTE]
> **审核**策略需要**DeployIfNotExists**策略来返回结果。
> 如果没有**DeployIfNotExists**,**审核**策略会将 "0 个 0" 资源显示为状态。

来宾配置的所有内置策略包含在一个计划内，以对分配中使用的定义分组。 名为“[预览]：  审核 Linux 和 Windows 虚拟机内的密码安全设置”的内置计划包含 18 个策略。 对于 Windows，有六个 **DeployIfNotExists** 和 **Audit** 对，对于 Linux，有三个对。 在每种情况下，都可使用定义内的逻辑验证仅基于[策略规则](definition-structure.md#policy-rule)定义评估目标操作系统。

## <a name="multiple-assignments"></a>多个分配

来宾配置策略目前仅支持对每个虚拟机分配相同的来宾分配, 即使策略分配使用不同的参数也是如此。

## <a name="client-log-files"></a>客户端日志文件

来宾配置扩展将日志文件写入以下位置:

Windows：`C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux：`/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

其中`<version>`指的是当前版本号。

## <a name="guest-configuration-samples"></a>来宾配置示例

策略来宾配置的示例可在以下位置找到:

- [示例索引-来宾配置](../samples/index.md#guest-configuration)
- [Azure 策略示例 GitHub](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)存储库。

## <a name="next-steps"></a>后续步骤

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 查看 [Azure Policy 定义结构](definition-structure.md)。
- 查看[了解策略效果](effects.md)。
- 了解如何以[编程方式创建策略](../how-to/programmatically-create.md)。
- 了解如何[获取相容性数据](../how-to/getting-compliance-data.md)。
- 了解如何[修正不合规的资源](../how-to/remediate-resources.md)。
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/index.md)，了解什么是管理组。
