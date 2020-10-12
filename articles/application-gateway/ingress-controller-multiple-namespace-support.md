---
title: 为应用程序网关入口控制器启用多命名空间支持
description: 本文提供有关如何在使用应用程序网关入口控制器的 Kubernetes 群集中启用多命名空间支持的信息。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 953430421bd30aaa1df352451b549994aeaa1a70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85556165"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>在使用应用程序网关入口控制器的 AKS 群集中启用多命名空间支持

## <a name="motivation"></a>动机
Kubernetes [命名空间](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)使 Kubernetes 群集可分区并分配到较大团队的子组。 然后，这些子团队可以通过对资源、安全性、配置等的更精细控制来部署和管理基础结构。Kubernetes 允许在每个命名空间内部单独定义一个或多个入口资源。

从版本 0.7 开始，[Azure 应用程序网关 Kubernetes 入口控制器](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC) 可以从多个命名空间引入事件，并可以观察这些命名空间。 如果 AKS 管理员决定使用[应用程序网关](https://azure.microsoft.com/services/application-gateway/)作为入口，则所有命名空间将使用相同的应用程序网关实例。 入口控制器的单个安装将会监视可访问的命名空间，并配置关联的应用程序网关。

AGIC 版本 0.7 将继续专门观察 `default` 命名空间，除非在 Helm 配置中将其显式更改为一个或多个不同的命名空间（请参阅以下部分）。

## <a name="enable-multiple-namespace-support"></a>启用多命名空间支持
若要启用多命名空间支持：
1. 按以下方式之一修改 [helm-config.yaml](#sample-helm-config-file) 文件：
   - 从 [helm-config.yaml](#sample-helm-config-file) 中删除整个 `watchNamespace` 键 - AGIC 将观察所有命名空间
   - 将 `watchNamespace` 设置为空字符串 - AGIC 将观察所有命名空间
   - 添加以逗号 (`watchNamespace: default,secondNamespace`) 分隔的多个命名空间 - AGIC 将专门观察这些命名空间
2. 使用 `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure` 应用 Helm 模板更改

部署并可以观察多个命名空间后，AGIC 将会：
  - 列出所有可访问的命名空间中的资源
  - 筛选带有 `kubernetes.io/ingress.class: azure/application-gateway` 批注的入口资源
  - 编写组合的[应用程序网关配置](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - 通过 [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 将配置应用到关联的应用程序网关

## <a name="conflicting-configurations"></a>有冲突的配置
多个带有命名空间的[入口资源](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource)可能会指示 AGIC 为单个应用程序网关创建有冲突的配置。 （例如，两个入口声明同一个域。）

在层次结构的顶层 - **侦听器**（IP 地址、端口和主机）和**路由规则**（绑定侦听器、后端池和 HTTP 设置）可由多个命名空间/入口创建和共享。

另一方面 - 路径、后端池、HTTP 设置和 TLS 证书只能由一个命名空间创建，重复项将被删除。

例如，假设以下重复入口资源为 `www.contoso.com` 定义了命名空间 `staging` 和 `production`：

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

尽管这两个入口资源要求将 `www.contoso.com` 的流量路由到相应的 Kubernetes 命名空间，但只有一个后端能够为该流量提供服务。 AGIC 将会根据“先到者先接受服务”的原则，为其中的一个资源创建配置。 如果同时创建了两个入口资源，则字母顺序靠前的资源将优先接受服务。 在上述示例中，我们只能为 `production` 入口创建设置。 将在应用程序网关中配置以下资源：

  - 侦听器：`fl-www.contoso.com-80`
  - 路由规则：`rr-www.contoso.com-80`
  - 后端池：`pool-production-contoso-web-service-80-bp-80`
  - HTTP 设置：`bp-production-contoso-web-service-80-80-websocket-ingress`
  - 运行状况探测：`pb-production-contoso-web-service-80-websocket-ingress`

请注意，创建的应用程序网关资源（侦听器和路由规则除外）包含创建时所针对的命名空间 (`production`) 的名称。  

如果在不同时间点将两个入口资源引入 AKS 群集，AGIC 最终可能会出现这种情况：重新配置应用程序网关，并将流量从 `namespace-B` 重新路由到 `namespace-A`。

例如，如果先添加 `staging`，则 AGIC 会将应用程序网关配置为向临时后端池路由流量。 在后续阶段引入 `production` 入口会导致 AGIC 对应用程序网关重新编程，从而开始将流量路由到 `production` 后端池。

## <a name="restrict-access-to-namespaces"></a>限制对命名空间的访问
默认情况下，AGIC 根据任何命名空间中带批注的入口配置应用程序网关。 如果你想要限制此行为，可使用以下选项：
  - 通过 [helm-config.yaml](#sample-helm-config-file) 中的 `watchNamespace` YAML 键定义 AGIC 应观察的命名空间，以限制命名空间
  - 使用[角色/角色绑定](https://docs.microsoft.com/azure/aks/azure-ad-rbac)来限制 AGIC 只能访问特定的命名空间

## <a name="sample-helm-config-file"></a>示例 Helm 配置文件

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

