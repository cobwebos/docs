---
title: Azure 虚拟机的维护控制
description: 了解如何使用维护控制来控制何时将维护应用到 Azure Vm。
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: e2eb77bfd000ecaa3bad5fd3c5792d1aa3a81964
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964866"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>预览：控制包含维护控制的更新和 Azure CLI

使用维护控制管理平台更新，无需重新启动。 Azure 会频繁更新其基础结构，以提高可靠性、性能、安全性或启动新功能。 大多数更新对用户是透明的。 某些敏感工作负荷（例如游戏、媒体流式处理和财务交易）不能容忍几乎几秒钟的时间冻结或断开连接以进行维护。 维护控制提供了在35天滚动窗口中等待平台更新并将其应用的选项。 

维护控制允许您决定何时将更新应用于隔离的 Vm 和 Azure 专用主机。

借助维护控制，您可以：
- 批处理更新为一个更新包。
- 请等待35天，以应用更新。 
- 使用 Azure Functions 自动执行维护时段的平台更新。
- 维护配置跨订阅和资源组工作。 

> [!IMPORTANT]
> 维护控制目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="limitations"></a>限制

- Vm 必须位于[专用主机](./linux/dedicated-hosts.md)上，或使用[独立的 VM 大小](./linux/isolation.md)创建。
- 35天后，将自动应用更新。
- 用户必须具有**资源参与者**访问权限。


## <a name="install-the-maintenance-extension"></a>安装维护扩展

如果选择在本地安装[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ，则需要2.0.76 或更高版本。

在本地或 Cloud Shell 中安装 `maintenance` 预览版 CLI 扩展。 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>创建维护配置

使用 `az maintenance configuration create` 创建维护配置。 此示例将创建一个名为*myconfig.xml*的维护配置，范围为主机。 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

复制输出中的配置 ID 以供以后使用。

使用 `--maintenanceScope host` 确保维护配置用于控制主机的更新。

如果尝试创建具有相同名称但位于不同位置的配置，则会出现错误。 配置名称在你的订阅中必须是唯一的。

您可以使用 `az maintenance configuration list`查询可用的维护配置。

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>分配配置

使用 `az maintenance assignment create` 将配置分配给独立 VM 或 Azure 专用主机。

### <a name="isolated-vm"></a>独立 VM

使用配置的 ID 将配置应用到 VM。 指定 `--resource-type virtualMachines` 并为 `--resource-name`提供 VM 的名称，并为 `--resource-group`中的 VM 提供资源组，并为 `--location`指定 vm 的位置。 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>专用主机

若要将配置应用到专用主机，需要包含 `--resource-type hosts`、`--resource-parent-name` 与主机组的名称一起使用，并 `--resource-parent-type hostGroups`。 

参数 `--resource-id` 是主机的 ID。 你可以使用[az vm host get-help](/cli/azure/vm/host#az-vm-host-get-instance-view)获取专用主机的 ID。

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>检查配置

可以验证是否正确应用了配置，或者通过 `az maintenance assignment list`查看当前应用的配置。

### <a name="isolated-vm"></a>独立 VM

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>专用主机 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>检查挂起的更新

使用 `az maintenance update list` 查看是否存在挂起的更新。 Update--订阅为包含 VM 的订阅的 ID。

如果没有更新，则该命令将返回一条错误消息，其中包含文本： `Resource not found...StatusCode: 404`。

如果有更新，则只会返回一个更新，即使存在多个挂起的更新。 此更新的数据将在对象中返回：

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>独立 VM

检查独立 VM 的挂起更新。 在此示例中，输出的格式为表格以便于阅读。

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>专用主机

检查专用主机的挂起更新（ADH）。 在此示例中，输出的格式为表格以便于阅读。 将资源的值替换为自己的值。

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>应用更新

使用 `az maintenance apply update` 来应用挂起的更新。 成功时，此命令将返回包含更新详细信息的 JSON。

### <a name="isolated-vm"></a>独立 VM

创建将更新应用到隔离 VM 的请求。

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>专用主机

将更新应用到专用主机。

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>检查应用更新的状态 

你可以使用 `az maintenance applyupdate get`查看更新进度。 

你可以使用 `default` 作为更新名称来查看上次更新的结果，或将 `myUpdateName` 替换为运行 `az maintenance applyupdate create`时返回的更新的名称。

### <a name="isolated-vm"></a>独立 VM

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>专用主机

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>删除维护配置

使用 `az maintenance configuration delete` 删除维护配置。 删除配置将从关联的资源中删除维护控制。

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>后续步骤
若要了解详细信息，请参阅[维护和更新](maintenance-and-updates.md)。
