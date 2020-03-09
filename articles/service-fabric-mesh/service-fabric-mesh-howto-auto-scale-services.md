---
title: 自动缩放 Azure Service Fabric 网格中运行的应用
description: 了解如何为 Service Fabric 网格应用程序的服务配置自动缩放策略。
author: dkkapur
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: fb72806dd7ba838ba7170bda409715bc074e1d99
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461970"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>创建 Service Fabric 网格应用程序的自动缩放策略
将应用程序部署到 Service Fabric 网格的一个主要优点是可以轻松地放大或缩小服务。这应该用于处理服务上的不同负载量，或提高可用性。 可以手动向内缩放或向外缩放服务，或者设置自动缩放策略。

[自动缩放](service-fabric-mesh-scalability.md#autoscaling-service-instances)允许动态缩放服务实例的数量（水平缩放）。 自动缩放提供了很大的弹性并可以预配或删除基于 CPU 或内存使用率的服务实例。

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>用于创建自动缩放策略、触发器和机制的选项
为要缩放的每个服务定义自动缩放策略。 策略在 YAML 服务资源文件或 JSON 部署模板中定义。 每个缩放策略由两部分组成：一个触发器和一个缩放机制。

触发器定义何时调用自动缩放策略。  指定触发器类型（平均负载）和要监视的指标（CPU 或内存）。  以百分比表示负载上限和下限阈值。 刻度间隔定义了在所有当前部署的服务实例中检查（以秒为单位）指定利用率（例如平均 CPU 负载）的频率。  如果受监视的指标低于阈值下限或超出阈值上限则触发机制。  

缩放机制定义当策略触发时如何执行缩放操作。  指定机制的类型（添加/删除副本）、最小和最大副本计数（整数）。  服务副本的数量永远不会低于最小计数或高于最大计数。  此外，将缩放增量指定为整数，即在缩放操作中添加或删除的副本数。  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>在 JSON 模板中定义自动缩放策略

下面的示例显示 JSON 部署模板中的自动缩放策略。  在要缩放的服务的属性中声明自动缩放策略。  在此示例中，已定义 CPU 平均负载触发器。  如果所有已部署的实例的平均 CPU 负载下降到 0.2 (20%) 以下或上升到 0.8 (80%) 以上，将触发机制。  每 60 秒检查一次 CPU 负载。  缩放机制定义为在触发策略时添加或删除实例。  将以 1 为增量添加或删除服务实例。  还定义了最小实例计数为 1，最大实例计数为 40。

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>在 Service.yaml 资源文件中定义自动缩放策略
下面的示例显示服务资源 (YAML) 文件中的自动缩放策略。  自动缩放策略被声明为要缩放的服务的属性。  在此示例中，已定义 CPU 平均负载触发器。  如果所有已部署的实例的平均 CPU 负载下降到 0.2 (20%) 以下或上升到 0.8 (80%) 以上，将触发机制。  每 60 秒检查一次 CPU 负载。  缩放机制定义为在触发策略时添加或删除实例。  将以 1 为增量添加或删除服务实例。  还定义了最小实例计数为 1，最大实例计数为 40。

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>后续步骤
了解如何[手动缩放服务](service-fabric-mesh-tutorial-template-scale-services.md)
