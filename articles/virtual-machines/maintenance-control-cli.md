---
title: 使用 CLI 对 Azure 虚拟机进行维护控制
description: 了解如何使用维护控制和 CLI 控制将维护应用于 Azure Vm 的时间。
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/20/2020
ms.author: cynthn
ms.openlocfilehash: 4843b4769e31748fd5f624005792c604db18f11e
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82137495"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>控制包含维护控制的更新和 Azure CLI

维护控制允许您决定何时将更新应用于隔离的 Vm 和 Azure 专用主机。 本主题介绍用于维护控制的 Azure CLI 选项。 有关使用维护控制、其限制和其他管理选项的好处的详细信息，请参阅[使用维护控制管理平台更新](maintenance-control.md)。

## <a name="create-a-maintenance-configuration"></a>创建维护配置

使用`az maintenance configuration create`创建维护配置。 此示例将创建一个名为*myconfig.xml*的维护配置，范围为主机。 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location eastus
```

复制输出中的配置 ID 以供以后使用。

使用`--maintenanceScope host`可确保使用维护配置控制主机的更新。

如果尝试创建具有相同名称但位于不同位置的配置，则会出现错误。 配置名称在你的订阅中必须是唯一的。

您可以使用`az maintenance configuration list`查询可用的维护配置。

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>分配配置

使用`az maintenance assignment create`将配置分配给独立 VM 或 Azure 专用主机。

### <a name="isolated-vm"></a>独立 VM

使用配置的 ID 将配置应用到 VM。 指定`--resource-type virtualMachines`并提供 vm 的名称`--resource-name`，并为中`--resource-group`的 vm 指定资源组，并为 vm 指定 vm 的`--location`位置。 

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

若要将配置应用到专用主机，你需要包含`--resource-type hosts`， `--resource-parent-name`并且具有主机组的名称和。 `--resource-parent-type hostGroups` 

参数`--resource-id`是主机的 ID。 你可以使用[az vm host get-help](/cli/azure/vm/host#az-vm-host-get-instance-view)获取专用主机的 ID。

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

可以验证是否正确应用了配置，或查看当前使用`az maintenance assignment list`的配置。

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

使用`az maintenance update list`查看是否有挂起的更新。 Update--订阅为包含 VM 的订阅的 ID。

如果没有更新，则该命令将返回一条错误消息，其中包含以下文本： `Resource not found...StatusCode: 404`。

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

检查专用主机的挂起更新。 在此示例中，输出的格式为表格以便于阅读。 将资源的值替换为自己的值。

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

使用`az maintenance apply update`应用挂起的更新。 成功时，此命令将返回包含更新详细信息的 JSON。

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

你可以使用`az maintenance applyupdate get`查看更新的进度。 

你可以使用`default`作为更新名称来查看上次更新的结果，或将替换`myUpdateName`为运行`az maintenance applyupdate create`时返回的更新的名称。

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
LastUpdateTime 将是完成更新的时间，这是由你或平台在未使用的情况下启动的。 如果从未使用过维护控制来应用更新，它将显示默认值。

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

使用`az maintenance configuration delete`删除维护配置。 删除配置将从关联的资源中删除维护控制。

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>后续步骤
若要了解详细信息，请参阅[维护和更新](maintenance-and-updates.md)。
