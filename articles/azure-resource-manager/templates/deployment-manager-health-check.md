---
title: 运行状况集成部署-Azure 部署管理器
description: 介绍如何使用 Azure 部署管理器在多个区域部署服务。 其中介绍了安全部署实践，可在部署到所有区域前验证部署的稳定性。
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273795"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>介绍 Azure 部署管理器的运行状况集成推出（公共预览版）

[Azure 部署管理器](./deployment-manager-overview.md)允许你执行 azure 资源管理器资源的分阶段部署。 资源按区域按顺序部署。 Azure 部署管理器的集成运行状况检查可以监视部署情况，并自动停止出现问题的展示，使你能够进行故障排除并降低影响的规模。 此功能可以减少更新中的回归导致的服务不可用。

## <a name="health-monitoring-providers"></a>运行状况监视提供程序

为了尽量简化运行状况集成，Microsoft 一直与某些顶级服务运行状况监视公司合作，以便为客户提供简单的复制/粘贴解决方案，用于将运行状况检查与部署相集成。 如果尚未使用运行状况监视器，以下是开始使用的最佳解决方案：

| ![azure 部署管理器运行状况监视器提供程序 datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![azure 部署管理器运行状况监视器提供程序 site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![azure 部署管理器运行状况监视器提供程序 wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog 是新式云环境的领先监视和分析平台。 了解[Datadog 如何与 Azure 部署管理器集成](https://www.datadoghq.com/azure-deployment-manager/)。|Site24x7，它是一种私有和公有云服务监视解决方案。 了解[Site24x7 如何与 Azure 部署管理器集成](https://www.site24x7.com/azure/adm.html)。| Wavefront，适用于多云应用程序环境的监视和分析平台。 了解[Wavefront 如何与 Azure 部署管理器集成](https://go.wavefront.com/wavefront-adm/)。|

## <a name="how-service-health-is-determined"></a>如何确定服务运行状况

[运行状况监视提供程序](#health-monitoring-providers)为监视服务提供了多种机制，并向您发出任何服务运行状况问题的警报。 [Azure Monitor](../../azure-monitor/overview.md)是一个此类产品的示例。 当超过特定阈值时，可以使用 Azure Monitor 来创建警报。 例如，将新更新部署到服务时，内存和 CPU 使用率会超出预期级别。 收到通知后，你可以采取纠正措施。

这些健康提供程序通常提供 REST Api，以便能够以编程方式检查服务监视器的状态。 REST Api 可以返回到简单的正常/不正常的信号（由 HTTP 响应代码确定）和/或包含有关其接收信号的详细信息。

Azure 部署管理器中的新*healthCheck*步骤使你可以声明指示正常服务的 HTTP 代码，或者，对于更复杂的 REST 结果，你甚至可以指定正则表达式（如果匹配）指示正常响应。

用 Azure 部署管理器运行状况检查进行设置的流：

1. 通过所选的运行状况服务提供程序创建运行状况监视器。
1. 在 Azure 部署管理器推出过程中，创建一个或多个 healthCheck 步骤。 填写 healthCheck 步骤，其中包含以下信息：

    1. 运行状况监视器的 REST API 的 URI （由运行状况服务提供程序定义）。
    1. 身份验证信息。 目前仅支持 API 键样式身份验证。
    1. 定义正常响应的[HTTP 状态代码](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes)或正则表达式。 请注意，您可以提供正则表达式，这两者都必须匹配才能视为正常的响应，或者您可能会提供表达式，使响应被视为正常。 支持这两种方法。

    下面的 Json 是一个示例：

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

1. 在 Azure 部署管理器部署中的适当时间调用 healthCheck 步骤。 在下面的示例中，在**stepGroup2**的**postDeploymentSteps**中调用了运行状况检查步骤。

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

若要完成示例，请参阅[教程：使用 Azure 部署管理器中的运行状况检查](./deployment-manager-health-check.md)。

## <a name="phases-of-a-health-check"></a>运行状况检查阶段

此时，Azure 部署管理器知道如何查询服务的运行状况，以及在部署中执行此操作的阶段。 但是，Azure 部署管理器还允许对这些检查的时间进行深入配置。 在3个连续阶段中执行 healthCheck 步骤，所有这些阶段都有可配置的持续时间： 

1. 等待

    1. 部署操作完成后，Vm 可能会重新启动、基于新数据重新配置，甚至首次启动。 运行状况监视提供程序将运行状况信号聚合为有用的内容时，服务还需要花费时间。 在此 tumultuous 过程中，检查服务运行状况可能并不合理，因为更新尚未达到稳定状态。 事实上，当资源结算时，服务在正常和不正常状态之间可能会变得不稳定。 
    1. 在等待阶段，不会监视服务运行状况。 这用于在开始运行状况检查过程之前，允许已部署的资源制作时间。 
1. Elastic

    1. 由于在所有情况下都不可能知道资源在变得稳定之前需要多长时间才能制作，因此，弹性阶段允许在资源可能不稳定以及需要维持正常稳定状态.
    1. 当弹性阶段开始时，Azure 部署管理器会定期轮询为服务运行状况提供的 REST 终结点。 轮询间隔是可配置的。 
    1. 如果运行状况监视器返回信号，指示该服务不正常，则会忽略这些信号，并继续弹性阶段，并继续轮询。 
    1. 当运行状况监视器返回指示服务处于正常状态的信号后，弹性阶段结束，HealthyState 阶段开始。 
    1. 因此，为弹性阶段指定的持续时间是在正常响应被视为必需之前，可用于轮询服务运行状况的最长时间。 
1. HealthyState

    1. 在 HealthyState 阶段，服务运行状况会按照弹性阶段的相同时间间隔持续轮询。 
    1. 此服务应在整个指定的持续时间内维护运行状况监视提供程序的正常信号。 
    1. 如果在任何时候检测到不正常的响应，Azure 部署管理器将停止整个推出并返回带有不正常服务信号的 REST 响应。
    1. HealthyState 持续时间结束后，healthCheck 完成，部署将继续进行下一步。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure 部署管理器中集成运行状况监视。 请转到下一篇文章，了解如何使用部署管理器进行部署。

> [!div class="nextstepaction"]
> [教程：集成 Azure 部署管理器中的运行状况检查](./deployment-manager-tutorial-health-check.md)