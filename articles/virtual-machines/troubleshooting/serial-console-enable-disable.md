---
title: 启用和禁用 Azure 串行控制台 |Microsoft Docs
description: 如何启用和禁用 Azure 串行控制台服务
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: fa400d875a8f39d54d10820c603e12e97f0cd854
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452230"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>启用和禁用 Azure 串行控制台

就像任何其他资源一样，Azure 串行控制台也可以启用和禁用。 默认情况下，为全球 Azure 中的所有订阅启用串行控制台。 目前，禁用串行控制台将为整个订阅禁用服务。 为订阅禁用或重新启用串行控制台需要订阅的参与者级别访问权限或更高版本。

还可以通过禁用启动诊断来禁用单个 VM 或虚拟机规模集实例的串行控制台。 你将需要 VM/虚拟机规模集和启动诊断存储帐户上的参与者级别访问权限或更高权限。

## <a name="vm-level-disable"></a>VM 级禁用
可以通过禁用 "启动诊断" 设置来禁用特定 VM 或虚拟机规模集的串行控制台。 关闭 Azure 门户的启动诊断，禁用 VM 或虚拟机规模集的串行控制台。 如果在虚拟机规模集上使用串行控制台，请确保将虚拟机规模集实例升级到最新模型。


## <a name="subscription-level-enabledisable"></a>订阅级别的启用/禁用

### <a name="azure-cli"></a>Azure CLI

通过使用 Azure CLI 中的以下命令，可以禁用和重新启用整个订阅的串行控制台（你可以使用 "试用" 按钮来启动可在其中运行命令的 Azure Cloud Shell 的实例）：

若要对订阅禁用串行控制台，请使用以下命令：
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

若要为订阅启用串行控制台，请使用以下命令：
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

若要获取订阅的串行控制台的当前启用/禁用状态，请使用以下命令：
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

> [!NOTE]
> 运行此命令之前，请确保位于正确的云中（Azure 公有云、Azure US 政府云）。 你可以查看 `az cloud list` 并通过 `az cloud set -n <Name of cloud>`设置云。

### <a name="powershell"></a>PowerShell

还可以使用 PowerShell 来启用和禁用串行控制台。

若要对订阅禁用串行控制台，请使用以下命令：
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

若要为订阅启用串行控制台，请使用以下命令：
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>后续步骤
* 详细了解适用于[Linux vm 的 Azure 串行控制台](./serial-console-linux.md)
* 详细了解适用于[Windows vm 的 Azure 串行控制台](./serial-console-windows.md)
* 了解[Azure 串行控制台中的电源管理选项](./serial-console-power-options.md)