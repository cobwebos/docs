---
title: 使用资源管理器模板启用用于 VM 的 Azure Monitor
description: 本文介绍如何使用 Azure PowerShell 或 Azure 资源管理器模板为一个或多个 Azure 虚拟机或虚拟机规模集启用用于 VM 的 Azure Monitor。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 89a9a1b762e02237a8ee08dca5d6eedefabaafbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87328131"
---
# <a name="enable-azure-monitor-for-vms-using-resource-manager-templates"></a>使用资源管理器模板启用用于 VM 的 Azure Monitor
本文介绍如何使用资源管理器模板为虚拟机或虚拟机规模集启用用于 VM 的 Azure Monitor。 此过程可用于以下操作：

- Azure 虚拟机
- Azure 虚拟机规模集
- 与 Azure Arc 连接的混合虚拟机

## <a name="prerequisites"></a>必备条件

- [创建并配置 Log Analytics 工作区](vminsights-configure-workspace.md)。 
- 请参阅 [支持的操作系统](vminsights-enable-overview.md#supported-operating-systems) ，以确保正在启用的虚拟机或虚拟机规模集的操作系统受支持。 

## <a name="resource-manager-templates"></a>资源管理器模板

我们已创建示例性的 Azure 资源管理器模板，用于载入虚拟机和虚拟机规模集。 这些模板中包含的方案可以用来在现有的资源上启用监视，并可创建新的启用了监视的资源。

>[!NOTE]
>模板需要部署在与启用虚拟机或虚拟机规模集相同的资源组中。


Azure 资源管理器模板在存档文件 (.zip) 中提供，该文件可以从 GitHub 存储库[下载](https://aka.ms/VmInsightsARMTemplates)。 文件内容中包含文件夹，这些文件夹代表每个带模板和参数文件的部署方案。 在运行它们之前，请修改参数文件并指定所需值。 

下载文件包含下述适用于不同方案的模板：

- **ExistingVmOnboarding** 模板可启用用于 VM 的 Azure Monitor，前提是虚拟机已存在。
- **NewVmOnboarding** 模板用于创建虚拟机并启用用于 VM 的 Azure Monitor 来监视它。
- **ExistingVmssOnboarding** 模板可启用用于 VM 的 Azure Monitor，前提是虚拟机规模集已存在。
- **NewVmssOnboarding** 模板用于创建虚拟机规模集并启用用于 VM 的 Azure Monitor 来监视它们。
- **ConfigureWorkspace** 模板可将 Log Analytics 工作区配置为支持用于 VM 的 Azure Monitor，只需启用相关解决方案以及 Linux 和 Windows 操作系统性能计数器集合即可。

>[!NOTE]
>如果虚拟机规模集已经存在，且升级策略已设置为“手动”，**** 则不需在运行 **ExistingVmssOnboarding** Azure 资源管理器模板后默认为实例启用用于 VM 的 Azure Monitor。 必须手动升级实例。

## <a name="deploy-templates"></a>部署模板
可以使用 [适用于资源管理器模板的任何部署方法](../../azure-resource-manager/templates/deploy-powershell.md) （包括以下使用 POWERSHELL 和 CLI 的示例）部署模板。

```powershell
New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile <Template.json> -TemplateParameterFile <Parameters.json>
```


```azurecli
az group deployment create --resource-group <ResourceGroupName> --template-file <Template.json> --parameters <Parameters.json>
```



## <a name="next-steps"></a>后续步骤

现已为虚拟机启用了监视，可在用于 VM 的 Azure Monitor 中使用此信息进行分析。

- 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。

- 若要确定虚拟机的性能瓶颈和总体利用率，请参阅 [查看 AZURE Vm 性能](vminsights-performance.md)。
