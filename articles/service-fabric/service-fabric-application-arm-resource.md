---
title: "使用 Azure 资源管理器部署和升级应用程序及服务 | Microsoft Docs"
description: "了解如何使用 Azure 资源管理器模板，将应用程序和服务部署到 Service Fabric 群集。"
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: dekapur
ms.openlocfilehash: d6cda201e4cf16549f296bf9873b1085effd3a45
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2017
---
# <a name="manage-applications-and-services-as-azure-resource-manager-resources"></a>将应用程序和服务作为 Azure 资源管理器资源进行管理

可以通过 Azure 资源管理器，将应用程序和服务部署到 Service Fabric 群集。 也就是说，现在可以使用 JSON 表示应用程序和服务，并在群集使用的同一个资源管理器模板中部署它们，而不用被迫等待群集就绪，然后再通过 PowerShell 或 CLI 部署和管理应用程序。 只需执行一步操作，即可完成注册、预配和部署应用程序的整个过程。

这是部署群集所需的任意安装应用程序、治理应用程序或群集管理应用程序的推荐方法。 其中包括[修补业务流程应用程序](service-fabric-patch-orchestration-application.md)、监视程序，或部署其他应用程序/服务前群集需要运行的任何应用程序。 

在适当情况下，将应用程序作为资源管理器资源进行管理可改进：
* 审核线索：资源管理器审核所有操作，并记录详细的活动日志，有助于跟踪对这些应用程序和群集做出的任何更改。
* 基于角色的访问控制 (RBAC)：可通过同一个资源管理器模板，管理对群集及其上部署的应用程序的访问。
* Azure 资源管理器（通过 Azure 门户）成为管理群集和关键应用程序部署的一站式平台。

下面的代码片段展示了可以通过模板管理的各种资源：

```json
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2017-07-01-preview",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```


## <a name="add-a-new-application-to-your-resource-manager-template"></a>将新应用程序添加到资源管理器模板

1. 准备群集的资源管理器模板，以供部署时使用。 若要详细了解如何执行此操作，请参阅[使用 Azure 资源管理器创建 Service Fabric 群集](service-fabric-cluster-creation-via-arm.md)。
2. 考虑一下，打算在群集中部署的一些应用程序。 是否有始终要运行的被其他应用程序依赖的应用程序？ 是否计划部署任何群集治理应用程序或安装应用程序？ 如上所述，此类应用程序最好是通过资源管理器模板进行管理。 
3. 确定要使用此方法部署的应用程序后，需要立即打包、压缩这些应用程序，并将它们上传到文件共享。 此共享必须可通过 REST 终结点进行访问，这样 Azure 资源管理器才能在部署期间使用它。
4. 在资源管理器模板中的群集声明下，描述每个应用程序的属性。 这些属性包括副本或实例计数，以及资源（其他应用程序或服务）之间的任何依赖链。 有关完整属性列表，请参阅 [REST API Swagger 规范](https://github.com/Azure/azure-rest-api-specs/blob/current/specification/servicefabric/resource-manager/Microsoft.ServiceFabric/2017-07-01-preview/servicefabric.json)。请注意，这不会取代应用程序或服务清单，只是在群集的资源管理器模板中描述了清单的部分内容。 下面展示了示例模板，包括在 Application1 中部署无状态服务 Service1 和有状态服务 Service2：

  ```json
  {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "clusterName": {
        "type": "string",
        "defaultValue": "Cluster",
        "metadata": {
          "description": "Name of your cluster - Between 3 and 23 characters. Letters and numbers only"
        }
      },
      "applicationTypeName": {
        "type": "string",
        "defaultValue": "ApplicationType",
        "metadata": {
          "description": "The application type name"
        }
      },
      "applicationTypeVersion": {
        "type": "string",
        "defaultValue": "1",
        "metadata": {
          "description": "The application type version"
        }
      },
      "appPackageUrl": {
        "type": "string",
        "metadata": {
          "description": "The URL to the application package sfpkg file"
        }
      },
      "applicationName": {
        "type": "string",
        "defaultValue": "Application1",
        "metadata": {
          "description": "The application name"
        }
      },
      "serviceName": {
        "type": "string",
        "defaultValue": "Service1",
        "metadata": {
          "description": "The service name"
        }
      },
      "serviceTypeName": {
        "type": "string",
        "defaultValue": "Service1Type",
        "metadata": {
          "description": "The service type name"
        }
      },
      "serviceName2": {
        "type": "string",
        "defaultValue": "Service2",
        "metadata": {
          "description": "The service name"
        }
      },
      "serviceTypeName2": {
        "type": "string",
        "defaultValue": "Service2Type",
        "metadata": {
          "description": "The service type name"
        }
      }
    },
    "variables": {
      "clusterLocation": "[resourcegroup().location]"
    },
    "resources": [
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [],
        "properties": {
          "provisioningState": "Default"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "appPackageUrl": "[parameters('appPackageUrl')]"
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "typeName": "[parameters('applicationTypeName')]",
          "typeVersion": "[parameters('applicationTypeVersion')]",
          "parameters": {},
          "upgradePolicy": {
            "upgradeReplicaSetCheckTimeout": "01:00:00.0",
            "forceRestart": "false",
            "rollingUpgradeMonitoringPolicy": {
              "healthCheckWaitDuration": "00:02:00.0",
              "healthCheckStableDuration": "00:05:00.0",
              "healthCheckRetryTimeout": "00:10:00.0",
              "upgradeTimeout": "01:00:00.0",
              "upgradeDomainTimeout": "00:20:00.0"
            },
            "applicationHealthPolicy": {
              "considerWarningAsError": "false",
              "maxPercentUnhealthyDeployedApplications": "50",
              "defaultServiceTypeHealthPolicy": {
                "maxPercentUnhealthyServices": "50",
                "maxPercentUnhealthyPartitionsPerService": "50",
                "maxPercentUnhealthyReplicasPerPartition": "50"
              }
            }
          }
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateless",
          "serviceTypeName": "[parameters('serviceTypeName')]",
          "instanceCount": "-1",
          "partitionDescription": {
            "partitionScheme": "Singleton"
          },
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": []
        }
      },
      {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters/applications/services",
        "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName2'))]",
        "location": "[variables('clusterLocation')]",
        "dependsOn": [
          "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applications/', parameters('applicationName'))]"
        ],
        "properties": {
          "provisioningState": "Default",
          "serviceKind": "Stateful",
          "serviceTypeName": "[parameters('serviceTypeName2')]",
          "targetReplicaSetSize": "3",
          "minReplicaSetSize": "2",
          "replicaRestartWaitDuration": "00:01:00.0",
          "quorumLossWaitDuration": "00:02:00.0",
          "standByReplicaKeepDuration": "00:00:30.0",
          "partitionDescription": {
            "partitionScheme": "UniformInt64Range",
            "count": "5",
            "lowKey": "1",
            "highKey": "5"
          },
          "hasPersistedState": "true",
          "correlationScheme": [],
          "serviceLoadMetrics": [],
          "servicePlacementPolicies": [],
          "defaultMoveCost": "Low"
        }
      }
    ]
  }
  ```

  > [!NOTE] 
  > 必须将 apiVersion 设置为 `"2017-07-01-preview"`。 部署的此模板也可以与群集互不影响，只要群集已部署即可。

5. 部署！ 

## <a name="manage-an-existing-application-via-resource-manager"></a>通过资源管理器管理现有应用程序

如果群集已部署，且其上已部署一些要作为资源管理器资源进行管理的应用程序，可以使用相同的 API，将这些应用程序确定为资源管理器资源，从而执行 PUT 调用，而不用删除并重新部署应用程序。 


## <a name="next-steps"></a>后续步骤

* 使用 [Service Fabric CLI](service-fabric-cli.md) 或 [PowerShell](service-fabric-deploy-remove-applications.md)，将其他应用程序部署到群集。 
* [升级 Service Fabric 群集](service-fabric-cluster-upgrade.md)

