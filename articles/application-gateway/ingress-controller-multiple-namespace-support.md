---
title: 为应用程序网关入口控制器启用多个命名空间支持
description: 本文提供了有关如何使用应用程序网关入口控制器在 Kubernetes 群集中启用多个命名空间支持的信息。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373467"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>使用应用程序网关入口控制器在 AKS 群集中启用多个命名空间支持

## <a name="motivation"></a>动机
使用 Kubernetes[命名空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)，可以将 Kubernetes 群集分区，并将其分配给更大团队的子组。 然后，这些子团队可以通过更精细地控制资源、安全性、配置等来部署和管理基础结构。Kubernetes 允许在每个命名空间内独立定义一个或多个入口资源。

从0.7 版开始[Azure 应用程序网关 Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) （AGIC）可从引入事件，并观察多个命名空间。 如果 AKS 管理员决定使用[应用网关](https://azure.microsoft.com/services/application-gateway/)作为入口，则所有命名空间都将使用同一应用程序网关实例。 入口控制器的单个安装将监视可访问的命名空间，并将配置与其关联的应用程序网关。

AGIC 版本0.7 将继续以独占方式观察 `default` 命名空间，除非在 Helm 配置中显式更改为一个或多个不同的命名空间（请参见下面的部分）。

## <a name="enable-multiple-namespace-support"></a>启用多命名空间支持
启用多个命名空间支持：
1. 通过以下方式之一修改[helm-yaml](#sample-helm-config-file)文件：
   - 完全通过 helm 删除 `watchNamespace` 项[。 yaml](#sample-helm-config-file) -AGIC 将观察所有命名空间
   - 将 `watchNamespace` 设置为空字符串-AGIC 将观察所有命名空间
   - 添加多个由逗号（`watchNamespace: default,secondNamespace`）分隔的命名空间-AGIC 将专门观察这些命名空间
2. 将 Helm 模板更改应用于： `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

部署后，可以观察多个命名空间，AGIC 将：
  - 列出所有可访问的命名空间中的资源
  - 筛选到 `kubernetes.io/ingress.class: azure/application-gateway` 批注的入口资源
  - 组合[应用程序网关配置](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - 通过[ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)将配置应用到关联的应用程序网关

## <a name="conflicting-configurations"></a>冲突配置
多个带命名空间[入口资源](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource)可以指示 AGIC 为单一应用程序网关创建冲突配置。 （两个恒温器为实例声明相同的域。）

在层次**结构的顶部（IP**地址、端口和主机）和**路由规则**（绑定侦听器、后端池和 HTTP 设置），可以通过多个命名空间/恒温器来创建和共享。

在其他手路径上，后端池、HTTP 设置和 TLS 证书只能由一个命名空间创建，重复项将被删除。

例如，请考虑以下重复的入口资源命名空间 `staging` 和 `production` 用于 `www.contoso.com`：
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

尽管两个入口资源会要求流量 `www.contoso.com` 路由到相应的 Kubernetes 命名空间，但只有一个后端可以为流量提供服务。 AGIC 会在一个资源的 "首先提供" 基础上创建配置。 如果同时创建了两个恒温器资源，则字母表中前面的资源将优先。 在上述示例中，我们将只能为 `production` 入口创建设置。 将为应用程序网关配置以下资源：

  - 侦听器： `fl- www.contoso.com-80`
  - 路由规则： `rr- www.contoso.com-80`
  - 后端池： `pool-production-contoso-web-service-80-bp-80`
  - HTTP 设置： `bp-production-contoso-web-service-80-80-websocket-ingress`
  - 运行状况探测： `pb-production-contoso-web-service-80-websocket-ingress`

请注意，除*侦听器*和*路由规则*外，创建的应用程序网关资源包含为其创建的命名空间的名称（`production`）。

如果两个入口资源在不同时间点引入到 AKS 群集中，则 AGIC 可能最终出现在它重新配置应用程序网关，并将流量从 `namespace-B` 重新路由到 `namespace-A`的情况。

例如，如果您首先添加了 `staging`，则 AGIC 会将应用程序网关配置为将流量路由到临时后端池。 稍后，引入 `production` 引入会导致 AGIC 重新编程应用程序网关，后者会开始将流量路由到 `production` 后端池。

## <a name="restrict-access-to-namespaces"></a>限制对命名空间的访问
默认情况下，AGIC 将基于任何命名空间中的带批注入口配置应用程序网关。 如果希望限制此行为，您可以选择以下选项：
  - 限制命名空间，方法是显式定义命名空间，AGIC 应通过[helm](#sample-helm-config-file)中的 `watchNamespace` YAML 键观察
  - 使用[Role/RoleBinding](https://docs.microsoft.com/azure/aks/azure-ad-rbac)将 AGIC 限制为特定的命名空间

## <a name="sample-helm-config-file"></a>Helm 配置文件示例
```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

