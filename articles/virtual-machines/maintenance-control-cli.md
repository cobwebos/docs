---
title: 维护控制
description: 了解如何使用维护控制控制何时将维护应用于 Azure VM。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 58c0964d170f49066802b955f09dab01eaf998a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250174"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>预览：使用维护控制和 Azure CLI 进行控制更新

使用维护控制管理不需要重新启动的平台更新。 Azure 经常更新其基础结构以提高可靠性、性能、安全性或启动新功能。 大多数更新对用户都是透明的。 某些敏感工作负载（如游戏、媒体流和金融交易）甚至无法容忍几秒钟的 VM 冻结或断开连接以进行维护。 维护控制允许您选择等待平台更新并在 35 天滚动窗口中应用它们。 

通过维护控制，您可以决定何时将更新应用于隔离的 VM 和 Azure 专用主机。

通过维护控制，您可以：
- 批量更新到一个更新包中。
- 最多等待 35 天才能应用更新。 
- 使用 Azure 函数自动为维护窗口执行平台更新。
- 维护配置跨订阅和资源组工作。 

> [!IMPORTANT]
> 维护控制当前处于公共预览版中。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
>

## <a name="limitations"></a>限制

- VM 必须位于[专用主机](./linux/dedicated-hosts.md)上，或者使用[隔离的 VM 大小](./linux/isolation.md)创建。
- 35 天后，将自动应用更新。
- 用户必须具有**资源参与者**访问权限。


## <a name="install-the-maintenance-extension"></a>安装维护扩展

如果选择在本地安装[Azure CLI，](https://docs.microsoft.com/cli/azure/install-azure-cli)则需要版本 2.0.76 或更高版本。

在`maintenance`本地或云壳中安装预览 CLI 扩展。 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>创建维护配置

用于`az maintenance configuration create`创建维护配置。 本示例创建名为*myConfig*的维护配置，该配置范围为主机。 

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

从输出复制配置 ID 以以后使用。

使用`--maintenanceScope host`可确保维护配置用于控制对主机的更新。

如果尝试创建具有相同名称的配置，但在不同的位置，将出现错误。 配置名称必须对订阅是唯一的。

您可以使用`az maintenance configuration list`查询可用的维护配置。

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>分配配置

用于`az maintenance assignment create`将配置分配给隔离的 VM 或 Azure 专用主机。

### <a name="isolated-vm"></a>隔离的 VM

使用配置的 ID 将配置应用于 VM。 指定`--resource-type virtualMachines``--resource-name`的 VM 的名称和 中的 VM 以及`--resource-group``--location`中的 VM 的位置。 

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

要将配置应用于专用主机，您需要包含`--resource-type hosts``--resource-parent-name`具有主机组的名称 和`--resource-parent-type hostGroups`。 

参数`--resource-id`是主机的 ID。 您可以使用[az vm 主机获取实例视图](/cli/azure/vm/host#az-vm-host-get-instance-view)来获取专用主机的 ID。

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

您可以验证配置是否正确应用，或者检查当前使用`az maintenance assignment list`应用的配置。

### <a name="isolated-vm"></a>隔离的 VM

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

用于`az maintenance update list`查看是否有挂起的更新。 更新 -- 订阅是包含 VM 的订阅的 ID。

如果没有更新，该命令将返回一条错误消息，其中包含文本： `Resource not found...StatusCode: 404`。

如果有更新，则仅返回一个更新，即使有多个更新挂起。 此更新的数据将在对象中返回：

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

### <a name="isolated-vm"></a>隔离的 VM

检查隔离 VM 的挂起更新。 在此示例中，输出被格式化为可读性表。

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>专用主机

检查专用主机的挂起更新。 在此示例中，输出被格式化为可读性表。 将资源的值替换为您自己的值。

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

用于`az maintenance apply update`应用挂起的更新。 成功后，此命令将返回 JSON，其中包含更新的详细信息。

### <a name="isolated-vm"></a>隔离的 VM

创建将更新应用于隔离 VM 的请求。

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>专用主机

将更新应用于专用主机。

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

您可以使用 检查更新的进度`az maintenance applyupdate get`。 

可以使用`default`作为更新名称查看上次更新的结果，或替换为`myUpdateName`运行`az maintenance applyupdate create`时返回的更新的名称。

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
上次更新时间将是更新完成的时间，由您启动，或者由平台启动，以防未使用自我维护窗口。 如果从未通过维护控制应用更新，则将显示默认值。

### <a name="isolated-vm"></a>隔离的 VM

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

用于`az maintenance configuration delete`删除维护配置。 删除配置会从关联的资源中删除维护控制。

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>后续步骤
要了解更多信息，请参阅[维护和更新](maintenance-and-updates.md)。
