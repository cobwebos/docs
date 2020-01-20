---
title: 将 Azure 自动缩放与 Linux 规模集模板中的来宾指标配合使用
description: 了解如何使用 Linux 虚拟机规模集模板中的来宾指标执行自动缩放
author: mayanknayar
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 88f839b281e4d345b012a7e6acff3770dc536045
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271950"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>使用 Linux 规模集模板中的来宾指标执行自动缩放

Azure 中有两种从 Vm 和规模集收集的各种种类的指标：主机指标和来宾指标。 在高级别上，如果想要使用标准的 CPU、磁盘和网络指标，则主机指标非常合适。 但是，如果需要更大的指标选择，则应查看来宾指标。

主机指标不需要其他设置，因为它们是由主机 VM 收集的，而来宾度量要求您在来宾 VM 中安装[Windows Azure 诊断扩展](../virtual-machines/windows/extensions-diagnostics-template.md)或[Linux Azure 诊断扩展](../virtual-machines/linux/diagnostic-extension.md)。 使用来宾指标而不是主机指标的一个常见原因是，与主机指标相比，来宾指标提供更大的指标选择范围。 内存消耗指标就是这样一个例子，它们只会通过来宾指标提供。 [此处](../azure-monitor/platform/metrics-supported.md)列出了支持的主机指标，[此处](../azure-monitor/platform/autoscale-common-metrics.md)列出了常用的来宾指标。 本文介绍如何修改[基本可行规模集模板](virtual-machine-scale-sets-mvss-start.md)，以根据适用于 Linux 规模集的来宾指标使用自动缩放规则。

## <a name="change-the-template-definition"></a>更改模板定义

在[以前的文章](virtual-machine-scale-sets-mvss-start.md)中，我们已创建了一个基本规模集模板。 现在，我们将使用该模板，并对其进行修改，以创建一个模板，该模板使用基于来宾指标的自动缩放功能来部署 Linux 规模集。

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

最后，添加 `autoscaleSettings` 资源以基于这些指标配置自动缩放。 此资源包含一个引用规模集的 `dependsOn` 子句，确保在尝试自动缩放该规模集之前它已存在。 如果选择不同的指标作为自动缩放的依据，可使用诊断扩展配置中的 `counterSpecifier` 作为自动缩放配置中的 `metricName`。 有关自动缩放配置的详细信息，请参阅[自动缩放最佳做法](../azure-monitor/platform/autoscale-best-practices.md)和 [Azure Monitor REST API 参考文档](/rest/api/monitor/autoscalesettings)。

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
