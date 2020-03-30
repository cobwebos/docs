---
title: 运行状况集成推出 - Azure 部署管理器
description: 介绍如何使用 Azure 部署管理器在多个区域部署服务。 其中介绍了安全部署实践，可在部署到所有区域前验证部署的稳定性。
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273795"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>向 Azure 部署管理器引入运行状况集成推出（公共预览版）

[Azure 部署管理器](./deployment-manager-overview.md)允许您执行 Azure 资源管理器资源的分阶段推出。 资源按区域有序部署。 Azure 部署管理器 的集成运行状况检查可以监视推出，并自动停止有问题的推出，以便您可以排除故障并减少影响的规模。 此功能可以减少更新中回归导致的服务不可用。

## <a name="health-monitoring-providers"></a>健康监测提供者

为了尽量简化运行状况集成，Microsoft 一直与某些顶级服务运行状况监视公司合作，以便为客户提供简单的复制/粘贴解决方案，用于将运行状况检查与部署相集成。 如果您尚未使用运行状况监视器，则以下是从以下方面开始的绝佳解决方案：

| ![azure 部署管理器运行状况监视器提供程序数据狗](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![azure 部署管理器运行状况监视器提供程序站点 24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure 部署管理器运行状况监视器提供程序波前](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog，现代云环境的领先监控和分析平台。 了解[Datadog 如何与 Azure 部署管理器集成](https://www.datadoghq.com/azure-deployment-manager/)。|Site24x7，一体式私有和公共云服务监控解决方案。 了解[Site24x7 如何与 Azure 部署管理器集成](https://www.site24x7.com/azure/adm.html)。| 波前，多云应用环境的监控和分析平台。 了解[Wavefront 如何与 Azure 部署管理器集成](https://go.wavefront.com/wavefront-adm/)。|

## <a name="how-service-health-is-determined"></a>如何确定服务运行状况

[运行状况监控提供程序](#health-monitoring-providers)提供了多种机制来监控服务和提醒您任何服务运行状况问题。 [Azure 监视器](../../azure-monitor/overview.md)是此类产品/服务的示例。 Azure 监视器可用于在超过某些阈值时创建警报。 例如，在将新的更新部署到服务时，内存和 CPU 利用率的峰值超出预期级别。 通知后，您可以采取纠正措施。

这些运行状况提供程序通常提供 REST API，以便可以以编程方式检查服务监视器的状态。 REST API 可以返回一个简单的健康/不正常信号（由 HTTP 响应代码确定）和/或有关其接收信号的详细信息。

Azure 部署管理器中的新*运行状况检查*步骤允许您声明指示正常服务的 HTTP 代码，或者，对于更复杂的 REST 结果，甚至可以指定正则表达式，如果它们匹配，则指示正常响应。

使用 Azure 部署管理器运行状况检查获取安装程序的流：

1. 通过您选择的健康服务提供商创建您的健康监视器。
1. 创建一个或多个运行状况检查步骤，作为 Azure 部署管理器部署部署部署部署的一部分。 填写运行状况检查步骤，并提供以下信息：

    1. 用于运行状况监视器的 REST API 的 URI（由运行状况服务提供商定义）。
    1. 身份验证信息。 目前仅支持 API 密钥样式身份验证。
    1. 定义正常响应的[HTTP 状态代码](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes)或正则表达式。 请注意，您可以提供正则表达式，ALL 必须匹配该表达式才能认为响应为健康，或者您可以提供 ANY 必须匹配的表达式，以便响应被视为正常。 支持这两种方法。

    以下 Json 就是一个示例：

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. 在 Azure 部署管理器 推出中的适当时间调用运行状况检查步骤。 在下面的示例中，在**步骤Group2**的部署**后步骤**中调用了运行状况检查步骤。

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

要演练一个示例，请参阅[教程：在 Azure 部署管理器 中使用运行状况检查](./deployment-manager-health-check.md)。

## <a name="phases-of-a-health-check"></a>运行状况检查阶段

此时，Azure 部署管理器知道如何查询服务的运行状况，以及部署中的哪些阶段可以执行此操作。 但是，Azure 部署管理器还允许对这些检查的时间进行深度配置。 运行状况检查步骤在 3 个顺序阶段执行，所有这些阶段都有可配置的持续时间： 

1. 等待

    1. 部署操作完成后，VM 可能会重新启动、根据新数据重新配置，甚至首次启动。 服务还需要一段时间才能开始发出运行状况信号，以便运行状况监测提供者将其聚合为有用内容。 在此动荡过程中，检查服务运行状况可能没有意义，因为更新尚未达到稳定状态。 事实上，随着资源的解决，该服务可能在健康状态和不健康状态之间摇摆不定。 
    1. 在"等待"阶段，不会监视服务运行状况。 这用于允许已部署的资源在开始运行状况检查过程之前进行烘焙。 
1. Elastic

    1. 由于不可能在所有情况下知道资源在稳定之前烘烤需要多长时间，因此弹性阶段允许在资源可能不稳定和需要资源保持健康稳定之间留出一段灵活的时间段状态。
    1. 弹性阶段开始时，Azure 部署管理器开始定期轮询提供的 REST 终结点以进行服务运行状况。 轮询间隔是可配置的。 
    1. 如果运行状况监视器返回时带有指示服务不正常的信号，则忽略这些信号，弹性阶段继续，轮询将继续。 
    1. 一旦运行状况监视器返回，并显示服务正常，弹性阶段结束，健康状态阶段开始。 
    1. 因此，为弹性阶段指定的持续时间是在考虑正常响应之前，可以用于轮询服务运行状况的最大时间量。 
1. HealthyState

    1. 在 HealthState 阶段，服务运行状况会以与弹性相相同的间隔持续轮询。 
    1. 该服务应在整个指定持续时间内保持来自运行状况监控提供程序的健康信号。 
    1. 如果在任何时候检测到不正常的响应，Azure 部署管理器将停止整个部署，并返回携带不正常服务信号的 REST 响应。
    1. 运行状况持续时间结束后，运行状况检查将完成，并且部署将继续执行下一步。

## <a name="next-steps"></a>后续步骤

在本文中，您了解了如何在 Azure 部署管理器中集成运行状况监视。 请转到下一篇文章，了解如何使用部署管理器进行部署。

> [!div class="nextstepaction"]
> [教程：在 Azure 部署管理器中集成运行状况检查](./deployment-manager-tutorial-health-check.md)