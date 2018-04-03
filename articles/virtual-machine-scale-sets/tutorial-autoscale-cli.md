---
title: 教程 - 使用 Azure CLI 2.0 自动缩放规模集 | Microsoft Docs
description: 了解如何使用 Azure CLI 2.0 随 CPU 需求的增减自动缩放虚拟机规模集
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 10e5b1a261f28391bed8cf3f111b1124b52d7816
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>教程：使用 Azure CLI 2.0 自动缩放虚拟机规模集
创建规模集时，可定义想运行的 VM 实例数。 若应用程序需要更改，可自动增加或减少 VM 实例数。 通过自动缩放功能，可随客户需求的改变而进行调整，或在应用的整个生命周期内响应应用程序性能更改。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 对规模集使用自动缩放
> * 创建和使用自动缩放规则
> * 对 VM 实例进行压力测试并触发自动缩放规则
> * 在需求下降时自动横向缩减

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装和使用 CLI，本教程要求运行 Azure CLI 2.0.29 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 


## <a name="create-a-scale-set"></a>创建规模集
为了更好地创建自动缩放规则，可以为订阅 ID、资源组和规模集名称以及位置定义一些参数：

```azurecli-interactive
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

使用 [az group create](/cli/azure/group#create) 创建资源组，如下所示：

```azurecli-interactive
az group create --name $resourcegroup_name --location $location_name
```

现在，使用 [az vmss create](/cli/azure/vmss#create) 创建虚拟机规模集。 以下示例创建实例计数为 *2* 的规模集，并生成 SSH 密钥（如果不存在）：

```azurecli-interactive
az vmss create \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="define-an-autoscale-profile"></a>定义自动缩放配置文件
使用 Azure CLI 2.0 将自动缩放规则部署为 JSON（JavaScript 对象表示法）。 让我们看看此自动缩放配置文件的每个部分，然后创建一个完整的示例。

自动缩放配置文件开头定义了默认值、最小规模集容量和最大规模集容量。 以下示例设置了默认值，以及最小容量 2 个 VM 实例、最大容量 10 个 VM：

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-autoscale-out"></a>创建规则，以便自动横向扩展
如果应用程序需求提高，规模集中 VM 实例上的负载将会增大。 如果这种负载增大持续稳定，而不只是短暂的需求，那么可以配置自动缩放规则来增加规模集中的 VM 实例数。 创建这些 VM 实例及部署应用程序后，规模集会开始通过负载均衡器将流量分配到这些实例和应用程序。 可以控制要监视的指标（例如 CPU 或磁盘）、应用程序负载必须处于给定阈值内的时间，以及要添加到规模集的 VM 实例数。

当平均 CPU 负载在 5 分钟内均超过 70% 时，请创建规则以增加规模集中的 VM 实例数。 触发规则时，VM 实例数增加 3。

此规则使用以下参数：

| 参数         | 说明                                                                                                         | 值           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | 监视和应用规模集操作的性能指标。                                                   | CPU 百分比  |
| *timeGrain*       | 为进行而收集指标分析的频率。                                                                   | 1 分钟        |
| *timeAggregation* | 定义如何聚合已收集的指标以便分析。                                                | 平均值         |
| *timeWindow*      | 比较指标与阈值之前监视的时长。                                   | 5 分钟       |
| *operator*        | 用于比较指标数据和阈值的运算符。                                                     | 大于    |
| *threshold*       | 使自动缩放规则触发操作的值。                                                      | 70%             |
| *direction*       | 定义在应用规则的情况下，规模集应横向缩减还是扩展。                                              | 增加        |
| *类型*            | 表明 VM 实例数应该根据特定值进行更改。                                    | 更改计数    |
| *值*           | 应用规则时应减少或增加多少 VM 实例。                                             | 3               |
| *cooldown*        | 为使自动缩放操作有时间生效，再次应用规则前需要等待的时间。 | 5 分钟       |

以下示例定义增加 VM 实例数的规则。 metricResourceUri 使用以前为订阅 ID、资源组名称和规模集名称定义的变量：

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "ChangeCount",
    "value": "3",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-autoscale-in"></a>创建规则，以便自动横向缩减
在夜间或周末，应用程序需求可能会降低。 如果这种负载降低在一段时间内持续稳定，可以配置自动缩放规则来减少规模集中的 VM 实例数。 这种横向缩减操作可以减少运行规模集所需的成本，因为只运行满足当前需求所需的实例数。

平均 CPU 负载小于 30% 持续了 5 分钟时，请创建另一个规则来减少规模集中的 VM 实例数。 以下示例定义将 VM 实例数减 1 的规则。 metricResourceUri 使用以前为订阅 ID、资源组名称和规模集名称定义的变量：

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>将自动缩放规则应用于规模集
最后一步是将自动缩放配置文件和规则应用于规模集。 随后，规模集便能根据应用程序需求自动进行横向扩展或缩减。 按照如下所示，使用 [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) 应用自动缩放配置文件。 下述完整的 JSON 使用的是先前提到的配置文件和规则：

```azurecli-interactive
az monitor autoscale-settings create \
    --resource-group $resourcegroup_name \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "ChangeCount",
                "value": "3",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "ChangeCount",
                "value": "1",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="generate-cpu-load-on-scale-set"></a>在规模集上生成 CPU 负载
若要测试自动缩放规则，请在规模集的 VM 实例上生成一些 CPU 负载。 这种模拟的 CPU 负载会导致自动缩放以横向扩展的方式增加 VM 实例数。 随着模拟的 CPU 负载下降，自动缩放规则会进行横向缩减，减少 VM 实例数。

首先，请使用 [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) 列出规模集中连接 VM 实例的地址和端口：

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group $resourcegroup_name \
  --name $scaleset_name
```

以下示例输出显示了实例名称、负载均衡器的公共 IP 地址，以及可以通过网络地址转换 (NAT) 规则将流量转发到其中的端口号：

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

通过 SSH 连接到第一个 VM 实例。 使用 `-p` 参数指定你自己的公共 IP 地址和端口号，如前述命令所示：

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

登录后，安装 **stress** 实用程序。 启动 *10* 个可以生成 CPU 负载的 **stress** 辅助角色。 这些辅助角色运行 *420* 秒，此时间足以让自动缩放规则实施所需的操作。

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

当 **stress** 显示类似于 *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* 的输出时，按 *Enter* 键返回到提示符。

若要确认 **stress** 是否生成了 CPU 负载，请使用 **top** 实用程序检查活动的系统负载：

```azuecli-interactive
top
```

退出 **top**，然后关闭到 VM 实例的连接。 **stress** 继续在 VM 实例上运行。

```azurecli-interactive
Ctrl-c
exit
```

连接到第二个 VM 实例，所使用的端口号是在前面的 [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) 中列出的：

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50003
```

安装并运行 **stress**，然后在这第二个 VM 实例上启动十个辅助角色。

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

当 **stress** 再次显示类似于 *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd* 的输出时，按 *Enter* 键返回到提示符。

关闭到第二个 VM 实例的连接。 **stress** 继续在 VM 实例上运行。

```azurecli-interactive
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>监视活动的自动缩放规则
若要监视规模集中的 VM 实例数，请使用 **watch**。 自动缩放规则需要 5 分钟的时间才能启动横向扩展过程，以便响应由每个 VM 实例上的 **stress** 生成的 CPU 负载：

```azurecli-interactive
watch az vmss list-instances \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --output table
```

达到 CPU 阈值以后，自动缩放规则会增加规模集中的 VM 实例数。 以下输出显示，在规模集进行自动横向扩展时创建了 3 个 VM：

```bash
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

当 **stress** 在初始 VM 实例上停止后，平均 CPU 负载会回到正常。 另一个 5 分钟后，自动缩放规则会缩减 VM 实例数。 横向缩减操作会首先删除 ID 值最高的 VM 实例。 以下示例输出显示，在规模集进行自动横向缩减时删除了一个 VM 实例：

```bash
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

使用 `Ctrl-c` 退出 *watch*。 规模集继续每 5 分钟横向缩减一次，每次删除一个 VM 实例，直至达到最小实例计数 2。


## <a name="clean-up-resources"></a>清理资源
若要删除规模集和其他资源，请使用 [az group delete](/cli/azure/group#az_group_delete) 删除资源组及其所有资源。 `--no-wait` 参数会使光标返回提示符处，不会等待操作完成。 `--yes` 参数将确认是否希望删除资源，不会显示询问是否删除的额外提示。

```azurecli-interactive
az group delete --name $resourcegroup_name --yes --no-wait
```


## <a name="next-steps"></a>后续步骤
本教程介绍了如何使用 Azure CLI 2.0 自动进行规模集的横向缩减或扩展：

> [!div class="checklist"]
> * 对规模集使用自动缩放
> * 创建和使用自动缩放规则
> * 对 VM 实例进行压力测试并触发自动缩放规则
> * 在需求下降时自动横向缩减

如需更多的虚拟机规模集操作示例，请参阅下面的 Azure CLI 2.0 示例脚本：

> [!div class="nextstepaction"]
> [适用于 Azure CLI 2.0 的规模集脚本示例](cli-samples.md)