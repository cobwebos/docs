---
title: 了解如何执行虚拟机内部的审核
description: 了解 Azure Policy 如何使用来宾配置审核 Azure 虚拟机内部的设置。
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 0a571084819c5dfed3f8d6891b59032ef2eecdd6
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856394"
---
# <a name="understand-azure-policys-guest-configuration"></a>了解 Azure Policy 的来宾配置

除了审核与[修正](../how-to/remediate-resources.md) Azure 资源之外，Azure Policy 能够审核虚拟机内部的设置。 验证由来宾配置扩展和客户端执行。 扩展通过客户端验证设置，例如操作系统的配置、应用程序配置或状态以及环境设置等等。

> [!IMPORTANT]
> 目前，来宾配置仅支持内置策略。

## <a name="extension-and-client"></a>扩展和客户端

为审核虚拟机内部的设置，已启用[虚拟机扩展](../../../virtual-machines/extensions/overview.md)。 该扩展下载适用的策略分配和相应的配置定义。

### <a name="register-guest-configuration-resource-provider"></a>注册来宾配置资源提供程序

必须注册资源提供程序，之后才能使用来宾配置。 可以通过门户或通过 PowerShell 注册。

#### <a name="registration---portal"></a>注册 - 门户

若要通过 Azure 门户注册资源提供程序的来宾配置，请按照下列步骤操作：

1. 启动 Azure 门户，单击“所有服务”。 搜索并选择“订阅”。

1. 找到并单击要启用来宾配置的订阅。

1. 在“订阅”页的左侧菜单中，单击“资源提供程序”。

1. 筛选或滚动直至找到“Microsoft.GuestConfiguration”，然后在同一行上单击“注册”。

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

### <a name="supported-client-types"></a>支持的客户端类型

下表显示了 Azure 映像上支持的操作系统列表：

|发布者|名称|版本|
|-|-|-|
|Canonical|Ubuntu Server|14.04、16.04、18.04|
|Credativ|Debian|8、9|
|Microsoft|Windows Server|2012 Datacenter、2012 R2 Datacenter、2016 Datacenter|
|OpenLogic|CentOS|7.3、7.4、7.5|
|Red Hat|Red Hat Enterprise Linux|7.4、7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> 自定义虚拟机映像当前不支持来宾配置。

### <a name="unsupported-client-types"></a>不支持的客户端类型

下表列出了不受支持的操作系统：

|操作系统|说明|
|-|-|
|Windows 客户端 | 不支持客户端操作系统（例如 Windows 7 和 Windows 10）。
|Windows Server 2016 Nano Server | 不支持。|

## <a name="guest-configuration-definition-requirements"></a>来宾配置定义要求

来宾配置运行的每个审核都需要两个策略定义：“DeployIfNotExists”和“AuditIfNotExists”。 DeployIfNotExists 用于准备虚拟机的来宾配置代理和其他组件以支持[验证工具](#validation-tools)。

“DeployIfNotExists”策略定义验证并更正以下项目：

- 验证虚拟机已分配要评估的配置。 如果当前不存在任何分配，则获取分配并通过以下操作准备虚拟机：
  - 使用[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)对虚拟机进行身份验证
  - 安装 Microsoft.GuestConfiguration 扩展的最新版本
  - 安装[验证工具](#validation-tools)和依赖项（如果需要）

一旦 DeployIfNotExists 符合，AuditIfNotExists 策略定义会使用本地验证工具来确定分配的配置分配是符合还是不符合。 验证工具向来宾配置客户端提供结果。 客户端将结果转发给来宾扩展，使其可通过来宾配置资源提供程序使用。

Azure Policy 使用来宾配置资源提供程序 complianceStatus 属性在“符合性”节点中报告符合性。 有关详细信息，请参阅[获取符合性数据](../how-to/getting-compliance-data.md)。

> [!NOTE]
> 对于每个来宾配置定义，必须同时存在 DeployIfNotExists 和 AuditIfNotExists 策略定义。

来宾配置的所有内置策略包含在一个计划内，以对分配中使用的定义分组。 名为“[预览]：审核 Linux 和 Windows 虚拟机内的密码安全设置”的内置计划包含 18 个策略。 对于 Windows 有六个 DeployIfNotExists 和 AuditIfNotExists 对，对于 Linux 有三个对。 在每种情况下，都可使用定义内的逻辑验证仅基于[策略规则](definition-structure.md#policy-rule)定义评估目标操作系统。

## <a name="next-steps"></a>后续步骤

- 在 [Azure Policy 示例](../samples/index.md)中查看示例
- 查看[策略定义结构](definition-structure.md)
- 查看[了解策略效果](effects.md)
- 了解如何[以编程方式创建策略](../how-to/programmatically-create.md)
- 了解如何[获取符合性数据](../how-to/getting-compliance-data.md)
- 了解如何[修正不符合的资源](../how-to/remediate-resources.md)
- 参阅[使用 Azure 管理组来组织资源](../../management-groups/index.md)，了解什么是管理组