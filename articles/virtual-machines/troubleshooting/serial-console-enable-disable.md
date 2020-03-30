---
title: 启用和禁用 Azure 串行控制台 |微软文档
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
ms.openlocfilehash: e09e08f8ba36cf576bc27551254225adee3bb0fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451300"
---
# <a name="enable-and-disable-the-azure-serial-console"></a>启用和禁用 Azure 串行控制台

与任何其他资源一样，可以启用和禁用 Azure 串行控制台。 默认情况下，全局 Azure 中的所有订阅都启用串行控制台。 目前，禁用串行控制台将禁用整个订阅的服务。 禁用或重新启用订阅的串行控制台需要订阅上的参与者级别或以上访问权限。

还可以通过禁用引导诊断来禁用单个 VM 或虚拟机规模集实例的串行控制台。 在 VM/虚拟机规模集和引导诊断存储帐户上，您将需要参与者级别访问权限或以上访问权限。

## <a name="vm-level-disable"></a>VM 级禁用
可以通过禁用引导诊断设置，为特定的 VM 或虚拟机规模设置禁用串行控制台。 关闭 Azure 门户的启动诊断，以禁用 VM 或虚拟机规模集的串行控制台。 如果在虚拟机规模集中使用串行控制台，请确保将虚拟机缩放集实例升级到最新型号。


## <a name="subscription-level-enabledisable"></a>订阅级启用/禁用

> [!NOTE]
> 在运行此命令之前，请确保您处于正确的云中（Azure 公共云、Azure 美国政府云）。 您可以使用 签入`az cloud list`并设置云`az cloud set -n <Name of cloud>`。

### <a name="azure-cli"></a>Azure CLI

通过使用 Azure CLI 中的以下命令（您可以使用"试用"按钮启动 Azure 云外壳的实例，您可以在其中运行命令），从而禁用并重新启用整个订阅的串行控制台：

要禁用订阅的串行控制台，请使用以下命令：
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action disableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

要为订阅启用串行控制台，请使用以下命令：
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource invoke-action --action enableConsole --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --api-version="2018-05-01"
```

要获取订阅串行控制台的当前启用/禁用状态，请使用以下命令：
```azurecli-interactive
subscriptionId=$(az account show --output=json | jq -r .id)

az resource show --ids "/subscriptions/$subscriptionId/providers/Microsoft.SerialConsole/consoleServices/default" --output=json --api-version="2018-05-01" | jq .properties
```

### <a name="powershell"></a>PowerShell

也可以使用 PowerShell 启用和禁用串行控制台。

要禁用订阅的串行控制台，请使用以下命令：
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action disableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

要为订阅启用串行控制台，请使用以下命令：
```azurepowershell-interactive
$subscription=(Get-AzContext).Subscription.Id

Invoke-AzResourceAction -Action enableConsole -ResourceId /subscriptions/$subscription/providers/Microsoft.SerialConsole/consoleServices/default -ApiVersion 2018-05-01
```

## <a name="next-steps"></a>后续步骤
* 了解有关 Linux VM 的[Azure 串行控制台](./serial-console-linux.md)的信息
* 了解有关 Windows VM 的[Azure 串行控制台](./serial-console-windows.md)的信息
* 了解[Azure 串行控制台中的电源管理选项](./serial-console-power-options.md)