---
title: 结合 Linux 规模集模板中的来宾指标使用 Azure 自动缩放 | Microsoft Docs
description: 了解如何使用 Linux 虚拟机规模集模板中的来宾指标执行自动缩放
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: negat
ms.openlocfilehash: 8e822d83dd3bafabfea60ad50224c87df226bdc6
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781422"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>使用 Linux 规模集模板中的来宾指标执行自动缩放

在 Azure 中，会从 VM 和规模集收集两种类型的指标：有些指标来自主机 VM，还有一些指标来自来宾 VM。 粗略地讲，如果使用的是标准 CPU、磁盘和网络指标，主机指标可能就非常适合。 不过，如果需要更多指标，来宾指标可能更为适合。 这两者的区别如下：

主机指标更简单、更可靠。 它们不需要进行额外设置，因为它们是由主机 VM 收集；而来宾指标则需要在来宾 VM 中安装 [Windows Azure 诊断扩展](../virtual-machines/windows/extensions-diagnostics-template.md)或 [Linux Azure 诊断扩展](../virtual-machines/linux/diagnostic-extension.md)。 使用来宾指标而不是主机指标的一个常见原因是，与主机指标相比，来宾指标提供更大的指标选择范围。 内存消耗指标就是这样一个例子，它们只会通过来宾指标提供。 [此处](../monitoring-and-diagnostics/monitoring-supported-metrics.md)列出了支持的主机指标，[此处](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md)列出了常用的来宾指标。 本文介绍如何修改[最小可行规模集模板](./virtual-machine-scale-sets-mvss-start.md)，以根据 Linux 规模集的来宾指标使用自动缩放规则。

## <a name="change-the-template-definition"></a>更改模板定义

可在[此处](https://raw.githubusercontent.com/gatneil/mvss/minimum-viable-scale-set/azuredeploy.json)查看最小可行规模集模板，可在[此处](https://raw.githubusercontent.com/gatneil/mvss/guest-based-autoscale-linux/azuredeploy.json)查看用于通过基于来宾的自动缩放部署 Linux 规模集的模板。 让我们逐一查看创建此模板 (`git diff minimum-viable-scale-set existing-vnet`) 时使用的差异内容：

首先，添加 `storageAccountName` 和 `storageAccountSasToken` 的参数。 诊断代理将指标数据存储在此存储帐户中的某个[表](../cosmos-db/table-storage-how-to-use-dotnet.md)内。 从 Linux 诊断代理版本 3.0 开始，不再支持使用存储访问密钥。 改为使用 [SAS 令牌](../storage/common/storage-dotnet-shared-access-signature-part-1.md)。

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

接下来，修改规模集 `extensionProfile` 以包含诊断扩展。 在此配置中，指定要从中收集指标的规模集的资源 ID，以及用来存储指标的存储帐户和 SAS 令牌。 指定指标的聚合频率（在本例中为每隔一分钟）以及要跟踪的指标（在本例中为已用内存百分比）。 有关此配置以及除已用内存百分比之外的其他指标的详细信息，请参阅[此文档](../virtual-machines/linux/diagnostic-extension.md)。

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

最后，添加 `autoscaleSettings` 资源以基于这些指标配置自动缩放。 此资源包含一个引用规模集的 `dependsOn` 子句，确保在尝试自动缩放该规模集之前它已存在。 如果选择不同的指标作为自动缩放的依据，可使用诊断扩展配置中的 `counterSpecifier` 作为自动缩放配置中的 `metricName`。 有关自动缩放配置的详细信息，请参阅[自动缩放最佳做法](..//monitoring-and-diagnostics/insights-autoscale-best-practices.md)和 [Azure Monitor REST API 参考文档](https://msdn.microsoft.com/library/azure/dn931928.aspx)。

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>后续步骤

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
