---
title: 根据 Azure 应用程序网关指标自动缩放 AKS Pod
description: 本文说明如何使用应用程序网关指标和 Azure Kubernetes 指标适配器缩放 AKS 后端 Pod
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5e0533a44db269229b2f26fa8d2f2b4f84f4d0b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85125457"
---
# <a name="autoscale-your-aks-pods-using-application-gateway-metrics-beta"></a>根据应用程序网关指标 (Beta) 自动缩放 AKS Pod

当传入流量增加时，必须根据需求纵向扩展应用程序。

在以下教程中，我们说明了如何使用应用程序网关的 `AvgRequestCountPerHealthyHost` 指标来纵向扩展应用程序。 `AvgRequestCountPerHealthyHost` 度量发送到特定后端池和后端 HTTP 设置组合的平均请求数。

我们将使用以下两个组件：

* [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter) - 我们将使用指标适配器通过指标服务器公开应用程序网关指标。 Azure Kubernetes 指标适配器是 Azure 下的一个开源项目，类似于应用程序网关入口控制器。 
* [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) - 我们将通过 HPA 来使用应用程序网关指标，并以用于缩放的部署为目标。

## <a name="setting-up-azure-kubernetes-metric-adapter"></a>设置 Azure Kubernetes 指标适配器

1. 我们将首先创建一个 Azure AAD 服务主体，并通过应用程序网关的资源组为它分配 `Monitoring Reader` 访问权限。 

    ```azurecli
        applicationGatewayGroupName="<application-gateway-group-id>"
        applicationGatewayGroupId=$(az group show -g $applicationGatewayGroupName -o tsv --query "id")
        az ad sp create-for-rbac -n "azure-k8s-metric-adapter-sp" --role "Monitoring Reader" --scopes applicationGatewayGroupId
    ```

1. 现在，我们将使用上面创建的 AAD 服务主体来部署 [`Azure Kubernetes Metric Adapter`](https://github.com/Azure/azure-k8s-metrics-adapter)。

    ```bash
    kubectl create namespace custom-metrics
    # use values from service principal created above to create secret
    kubectl create secret generic azure-k8s-metrics-adapter -n custom-metrics \
        --from-literal=azure-tenant-id=<tenantid> \
        --from-literal=azure-client-id=<clientid> \
        --from-literal=azure-client-secret=<secret>
    kubectl apply -f kubectl apply -f https://raw.githubusercontent.com/Azure/azure-k8s-metrics-adapter/master/deploy/adapter.yaml -n custom-metrics
    ```

1. 我们将创建名为 `appgw-request-count-metric` 的 `ExternalMetric` 资源。 此资源会指示指标适配器在 `myResourceGroup` 资源组中公开 `myApplicationGateway` 资源的 `AvgRequestCountPerHealthyHost` 指标。 可以使用 `filter` 字段，以应用程序网关中的特定后端池和后端 HTTP 设置为目标。

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

现在可以向指标服务器发出请求，看我们的新指标是否已公开：
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

## <a name="using-the-new-metric-to-scale-up-the-deployment"></a>使用新指标纵向扩展部署

能够通过指标服务器公开 `appgw-request-count-metric` 以后，即可使用 [`Horizontal Pod Autoscaler`](https://docs.microsoft.com/azure/aks/concepts-scale#horizontal-pod-autoscaler) 纵向扩展目标部署。

在以下示例中，我们将以示例部署 `aspnet` 为目标。 当每个 Pod 的 `appgw-request-count-metric` > 200 时，我们会纵向扩展 Pod，Pod 数上限为 `10`。

请替换目标部署名称，并应用以下自动缩放配置：
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

使用负载测试工具（例如 apache bench）对设置进行测试：
```bash
ab -n10000 http://<applicaiton-gateway-ip-address>/
```

## <a name="next-steps"></a>后续步骤
- [**排查入口控制器问题**](ingress-controller-troubleshoot.md)：排查入口控制器的任何问题。