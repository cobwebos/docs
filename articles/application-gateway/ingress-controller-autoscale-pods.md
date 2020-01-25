---
title: 具有 Azure 应用程序网关指标的自动缩放 AKS pod
description: 本文提供了有关如何使用应用程序网关指标和 Azure Kubernetes 指标适配器缩放 AKS 后端盒的说明
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: b98ab8d3c4d03115ea689b4dfd3d8dee753f019d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715074"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>使用应用程序网关度量值自动缩放 AKS pod （Beta）

随着传入流量的增加，根据需求扩展应用程序将变得至关重要。

在以下教程中，我们将介绍如何使用应用程序网关的 `AvgRequestCountPerHealthyHost` 度量值来扩展应用程序。 `AvgRequestCountPerHealthyHost` 度量发送到特定后端池和后端 HTTP 设置组合的平均请求数。

我们将使用以下两个组件：

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) -我们将使用指标适配器通过指标服务器公开应用程序网关指标。 Azure Kubernetes 指标适配器在 Azure 下是一个开源项目，类似于应用程序网关入口控制器。 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) -我们将使用 HPA 来使用应用程序网关指标，并以部署为目标进行缩放。

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>设置 Azure Kubernetes 指标适配器

1. 首先，我们将创建一个 Azure AAD 服务主体，并将其分配 `Monitoring Reader` 对应用程序网关的资源组的访问权限。 

    ```bash
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. 现在，我们将使用上面创建的 AAD 服务主体部署[`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) 。

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principle created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. 我们将使用名称 `appgw-request-count-metric`创建一个 `ExternalMetric` 资源。 此资源将指示指标适配器在 `myResourceGroup` 资源组中公开 `myApplicationGateway` 资源 `AvgRequestCountPerHealthyHost` 指标。 可以使用 "`filter`" 字段作为应用程序网关上特定的后端池和后端 HTTP 设置。

    ```yaml
    apiVersion: azure.com/v1alpha2
    kind: ExternalMetric
    metadata:
    name: appgw-request-count-metric
    spec:
        type: azuremonitor
        azure:
            resourceGroup: myResourceGroup # replace with your application gateway's resource group name
            resourceName: myApplicationGateway # replace with your application gateway's name
            resourceProviderNamespace: Microsoft.Network
            resourceType: applicationGateways
        metric:
            metricName: AvgRequestCountPerHealthyHost
            aggregation: Average
            filter: BackendSettingsPool eq '<backend-pool-name>~<backend-http-setting-name>' # optional
    ```

你现在可以向指标服务器发出请求，以查看新指标是否公开：
```bash
kubectl get --raw "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric"
# Sample Output
# {
#   "kind": "ExternalMetricValueList",
#   "apiVersion": "external.metrics.k8s.io/v1beta1",
#   "metadata":
#     {
#       "selfLink": "/apis/external.metrics.k8s.io/v1beta1/namespaces/default/appgw-request-count-metric",
#     },
#   "items":
#     [
#       {
#         "metricName": "appgw-request-count-metric",
#         "metricLabels": null,
#         "timestamp": "2019-11-05T00:18:51Z",
#         "value": "30",
#       },
#     ],
# }
```

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>使用新度量值扩展部署

一旦我们能通过指标服务器公开 `appgw-request-count-metric`，就可以使用[`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler)来扩展目标部署。

在下面的示例中，我们将以 `aspnet`部署为目标。 将每个 Pod `appgw-request-count-metric` > 200 到最大 `10` 盒时，将扩展箱。

替换目标部署名称并应用以下自动缩放配置：
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: deployment-scaler
spec:
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: aspnet # replace with your deployment's name
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metricName: appgw-request-count-metric
      targetAverageValue: 200
```

使用 apache 工作台等负载测试工具测试安装：
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>다음 단계
- [**诊断入口控制器问题**](ingress-controller-troubleshoot.md)：排查入口控制器的任何问题。