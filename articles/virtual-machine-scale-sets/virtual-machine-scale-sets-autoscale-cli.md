---
title: "使用 Azure CLI 自动缩放虚拟机规模集 | Microsoft Docs"
description: "如何使用 Azure CLI 2.0 为虚拟机规模集创建自动缩放规则"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 8552f6b2723fef2c61d49a34d2d60c2a6c209a32
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>使用 Azure CLI 2.0 自动缩放虚拟机规模集
创建规模集时，可定义想运行的 VM 实例数。 若应用程序需要更改，可自动增加或减少 VM 实例数。 通过自动缩放功能，可随客户需求的改变而进行调整，或在应用的整个生命周期内响应应用程序性能更改。

这篇文章演示了如何使用 Azure CLI 2.0 （其在规模集中监视 VM 实例性能）创建自动缩放规则。 这些缩放规则根据性能指标增加或减少 VM 实例数。 也可以使用 [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) 或在 [Azure 门户](virtual-machine-scale-sets-autoscale-portal.md)中完成这些步骤。


## <a name="prerequisites"></a>先决条件
需要现有虚拟机规模集，才能创建自动缩放规则。 可以使用 [Azure 门户](virtual-machine-scale-sets-create-portal.md)、[Azure CLI 2.0](virtual-machine-scale-sets-create-cli.md) 或 [Azure PowerShell](virtual-machine-scale-sets-create-powershell.md) 创建规模集。

若要更轻松地创建自动缩放规则，请为规模集定义几个变量。 以下示例为 myResourceGroup 资源组和 eastus 区域内名为的 myScaleSet 的规模集定义变量。 使用 [az account show](/cli/azure/account#az_account_show) 获取订阅 ID。 如果帐户关联了多个订阅，则仅返回第一个订阅。 按照如下所示，调整名称和订阅 ID：

```azurecli
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

## <a name="define-an-autoscale-profile"></a>定义自动缩放配置文件
使用 Azure CLI 2.0 将自动缩放规则部署为 JSON（JavaScript 对象表示法）。 本文章稍后会介绍定义和部署自动缩放规则的完整的 JSON。 

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


## <a name="create-a-rule-to-automatically-scale-out"></a>创建规则，以便自动横向扩展
如果应用程序需求提高，规模集中 VM 实例上的负载将会增大。 如果这种负载增大持续稳定，而不只是短暂的需求，那么可以配置自动缩放规则来增加规模集中的 VM 实例数。 创建这些 VM 实例及部署应用程序后，规模集会开始通过负载均衡器将流量分配到这些实例和应用程序。 可以控制要监视的指标（例如 CPU 或磁盘）、应用程序负载必须处于给定阈值内的时间，以及要添加到规模集的 VM 实例数。

当平均 CPU 负载在十分钟内均超过 70% 时，请创建规则以增加规模集中的 VM 实例数。 触发规则时，VM 实例数增加 20%。 在 VM 实例数少的规模集中，可以将 `type` 设置为 ChangeCount，并为 `value` 增加一个或两个实例。 在有大量 VM 实例的规模集中，增加 10% 或 20% 的 VM 实例可能更合适。

此规则使用以下参数：

| 参数         | 说明                                                                                                         | 值           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | 监视和应用规模集操作的性能指标。                                                   | CPU 百分比  |
| *timeGrain*       | 为进行而收集指标分析的频率。                                                                   | 1 分钟        |
| *timeAggregation* | 定义如何聚合已收集的指标以便分析。                                                | 平均值         |
| *timeWindow*      | 比较指标与阈值之前监视的时长。                                   | 10 分钟      |
| *operator*        | 用于比较指标数据和阈值的运算符。                                                     | 大于    |
| *threshold*       | 使自动缩放规则触发操作的值。                                                      | 70%             |
| *direction*       | 定义应用规则时，规模集应扩展还是缩减。                                             | 增加        |
| *类型*            | 表明 VM 实例数应该增加一定的百分比。                                 | 百分比更改  |
| *值*           | 应用规则时应增加或减少多少 VM 实例。                                            | 20              |
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
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-in"></a>创建规则，以便自动横向缩减
在夜间或周末，应用程序需求可能会降低。 如果这种负载降低在一段时间内持续稳定，可以配置自动缩放规则来减少规模集中的 VM 实例数。 这种横向缩减操作可以减少运行规模集所需的成本，因为只运行满足当前需求所需的实例数。

平均 CPU 负载小于 30% 持续了 10 多分钟时，请创建另一个规则减少规模集中的 VM 实例数。 以下示例定义增加 VM 实例数的规则。 metricResourceUri 使用以前为订阅 ID、资源组名称和规模集名称定义的变量：

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>将自动缩放规则应用于规模集
最后一步是将自动缩放配置文件和规则应用于规模集。 随后，规模集便能根据应用程序需求自动进行横向扩展或缩减。 按照如下所示，使用 [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) 应用自动缩放配置文件。 完整的 JSON 使用的是先前提到的配置文件和规则。

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
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
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "PercentChangeCount",
                "value": "20",
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
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "PercentChangeCount",
                "value": "20",
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


## <a name="monitor-number-of-instances-in-a-scale-set"></a>监视规模集中的实例数
若要查看 VM 实例的数量与状态，使用 [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances) 查看规模集中的实例列表。 状态指示是随规模集自动横向扩展而预配 VM 实例，还是随规模集自动横向缩减而取消预配 VM 实例。 以下示例查看 myResourceGroup 资源组中 myScaleSet 规模集的 VM 实例状态：

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```


## <a name="autoscale-based-on-a-schedule"></a>按计划自动缩放
前述示例通过 CPU 使用情况等基本主机指标自动横向扩展或缩减规模集。 还可以根据计划创建自动缩放规则。 通过这些基于计划的规则，可在应用程序需求按预期增加（如核心工作时间）之前自动增加 VM 实例数，在预期需求减少时（例如周末）自动减少实例数。

若要使用基于计划的自动缩放规则，请创建 JSON 配置文件，该文件定义 VM 实例数，以运行固定启动和结束时间窗口。 以下示例定义在每个工作日（星期一到星期五）上午 9 点将实例数增加到 10 的规则。

```json
{
  "name": "Scale out during each work day",
  "capacity": {
      "minimum": "10",
      "maximum": "10",
      "default": "10"
  },
  "rules": [],
  "recurrence": {
      "frequency": "Week",
      "schedule": {
          "timeZone": "Pacific Standard Time",
          "days": [
              "Monday",
              "Tuesday",
              "Wednesday",
              "Thursday",
              "Friday"
          ],
          "hours": [
              9
          ],
          "minutes": [
              0
          ]
      }
  }
}
```

若要在晚上缩小实例数，请另创建规则，指定较低的实例数和适当的启动时间。

以下完整示例定义一些规则，它们先横向扩展再横向缩减，然后通过 [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) 应用自动缩放配置文件。 此示例将重写前述示例中创建的基于指标的自动缩放规则。 metricResourceUri 使用以前为订阅 ID、资源组名称和规模集名称定义的变量：

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "Scale out during each work day",
          "capacity": {
            "minimum": "10",
            "maximum": "10",
            "default": "10"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                9
              ],
              "minutes": [
                0
              ]
            }
          }
        },
        {
          "name": "Scale in during the evening",
          "capacity": {
            "minimum": "3",
            "maximum": "3",
            "default": "3"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                18
              ],
              "minutes": [
                0
              ]
            }
          }
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="next-steps"></a>后续步骤
本文详细介绍了如何使用自动缩放规则来进行横向缩放，即增加或减少规模集中的 VM 实例数。 还可进行纵向缩放，即增大或减小 VM 实例的大小。 有关详细信息，请参阅[虚拟机规模集的纵向自动缩放](virtual-machine-scale-sets-vertical-scale-reprovision.md)。

有关如何管理 VM 实例的信息，请参阅[使用 Azure PowerShell 管理虚拟机规模集](virtual-machine-scale-sets-windows-manage.md)。

若要了解如何在触发自动缩放规则时生成警报，请参阅[在 Azure Monitor 中使用自动缩放操作发送电子邮件和 Webhook 警报通知](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)。 还可以[在 Azure Monitor 中使用审核日志发送电子邮件和 Webhook 警报通知](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)。
