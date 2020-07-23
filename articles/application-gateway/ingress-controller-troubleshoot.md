---
title: 应用程序网关入口控制器故障排除
description: 本文提供有关如何排查应用程序网关入口控制器常见问题的文字说明。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/18/2020
ms.author: caya
ms.openlocfilehash: 0fdfa6265b81140fa6536082fe7ad4c5fa687fc4
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86207154"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>排查入口控制器的常见问题

[Azure Cloud Shell](https://shell.azure.com/)是解决 AKS 和 AGIC 安装问题的最简便方法。 从[shell.azure.com](https://shell.azure.com/)或单击链接启动 shell：

[![嵌入启动](https://shell.azure.com/images/launchcloudshell.png "启动 Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>使用简单的 Kubernetes 应用进行测试

以下步骤假设：
  - 已有一个启用了高级网络的 AKS 群集
  - 已在 AKS 群集上安装 AGIC
  - 与 AKS 群集共享的 VNET 上已有应用程序网关

若要验证是否正确设置了应用程序网关 + AKS + AGIC 安装，请部署一个尽量简单的应用：

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

将上述脚本中的所有行一次复制并粘贴到[Azure Cloud Shell](https://shell.azure.com/)中。 请确保复制整个命令 - 从 `cat` 开始，到最后的 `EOF` 为止。

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

成功部署上述应用后，AKS 群集中将包含新的 Pod、服务和入口。

获取带有[Cloud Shell](https://shell.azure.com/)的 pod 的列表： `kubectl get pods -o wide` 。
预期已创建名为“test-agic-app-pod”的 Pod。 该 Pod 有一个 IP 地址。 此地址必须在 AKS 所用的应用程序网关的 VNET 中。

![Pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

获取服务列表：`kubectl get services -o wide`。 预期会看到名为“test-agic-app-service”的服务。

![Pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

获取入口列表：`kubectl get ingress`。 预期已创建名为“test-agic-app-ingress”的入口资源。 该资源具有主机名“test.agic.contoso.com”。

![Pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

其中的一个 Pod 将是 AGIC。 `kubectl get pods` 将显示 Pod 列表，其中的一个 Pod 以“ingress-azure”开头。 使用 `kubectl logs <name-of-ingress-controller-pod>` 获取该 Pod 的所有日志，以验证部署是否成功。 如果部署成功，日志中会添加以下行：
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

或者，可以从[Cloud Shell](https://shell.azure.com/)仅检索指示成功应用程序网关配置的行 `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'` ，其中 `<ingress-azure....>` 应该是 AGIC pod 的确切名称。

将在应用程序网关中应用以下配置：

- 侦听器：![listener](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- 路由规则：![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- 后端池：
  - 后端地址池中有一个 IP 地址，该地址与前面使用 `kubectl get pods -o wide`
![backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png) 观测到的 Pod IP 地址相匹配


最后，我们可以使用 `cURL` [Cloud Shell](https://shell.azure.com/)中的命令建立与新部署的应用的 HTTP 连接：

1. 使用 `kubectl get ingress` 获取应用程序网关的公共 IP 地址
2. 使用 `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![Pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

结果 `HTTP/1.1 200 OK` 表示应用程序网关 + AKS + AGIC 系统按预期方式工作。


## <a name="inspect-kubernetes-installation"></a>检查 Kubernetes 安装

### <a name="pods-services-ingress"></a>Pod、服务、入口
应用程序网关入口控制器 (AGIC) 持续监视以下 Kubernetes 资源：[部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment)或[Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod)、[服务](https://kubernetes.io/docs/concepts/services-networking/service/)、[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)


必须符合以下要求才能让 AGIC 按预期正常工作：
  1. AKS 必须包含一个或多个正常的 **Pod**。
     与[Cloud Shell](https://shell.azure.com/)验证此设置 `kubectl get pods -o wide --show-labels` ，如果你有一个带的 Pod `apsnetapp` ，你的输出可能如下所示：
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. 通过匹配的 `selector` 标签引用上述 Pod 的一个或多个**服务**。
     验证此[Cloud Shell](https://shell.azure.com/)`kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. 引用上述服务的**入口**（用来注释 `kubernetes.io/ingress.class: azure/application-gateway` ）验证此[Cloud Shell](https://shell.azure.com/)`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. 查看上述入口的批注：`kubectl get ingress aspnetapp -o yaml`（请将 `aspnetapp` 替换为入口的名称）
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     必须使用 `kubernetes.io/ingress.class: azure/application-gateway` 批注入口资源。
 

### <a name="verify-observed-namespace"></a>验证观测到的命名空间

* 获取 Kubernetes 群集中的现有命名空间。 应用在哪个命名空间中运行？ AGIC 是否监视该命名空间？ 有关如何正确配置观测到的命名空间，请参阅[多命名空间支持](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support)文档。

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* AGIC Pod 应位于 `default` 命名空间中（查看列 `NAMESPACE`）。 正常的 Pod 在 `STATUS` 列中会显示为 `Running`。 应至少有一个 AGIC Pod。

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* 如果 AGIC Pod 不正常（运行上述命令后 `STATUS` 列不是显示 `Running`）：
  - 获取日志来了解原因：`kubectl logs <pod-name>`
  - 对于前面的 Pod 实例：`kubectl logs <pod-name> --previous`
  - 描述 Pod 以获取更多上下文：`kubectl describe pod <pod-name>`


* 是否有 Kubernetes [服务](https://kubernetes.io/docs/concepts/services-networking/service/)和[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)资源？
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* 是否已使用 `kubernetes.io/ingress.class: azure/application-gateway` 批注[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)？ AGIC 只会监视具有此批注的 Kubernetes 入口资源。
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC 针对某些严重的错误发出 Kubernetes 事件。 可通过以下方式查看这些事件：
  - 在终端中运行 `kubectl get events --sort-by=.metadata.creationTimestamp`
  - 在浏览器中使用 [Kubernetes Web UI（仪表板）](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>日志记录级别

AGIC 提供 3 个日志记录级别。 第 1 级别是默认级别，显示的日志行数极少。
而第 5 级别会显示所有日志，包括应用到 ARM 的配置的净化内容。

Kubernetes 社区已经为 [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) 工具建立了 9 个日志记录级别。 在此存储库中，我们使用了其中 3 个语义类似的级别：


| 详细级别 | 说明 |
|-----------|-------------|
|  1        | 默认日志级别；显示启动详细信息、警告和错误 |
|  3        | 有关事件和更改的扩展信息；创建的对象列表 |
|  5        | 记录封送的对象；显示应用到 ARM 的已净化 JSON 配置 |


可通过 [helm-config.yaml](#sample-helm-config-file) 文件中的 `verbosityLevel` 变量调整详细级别。 将详细级别提高到 `5` 可以获取已分配到 [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 的 JSON 配置：
  - 在 [helm-config.yaml](#sample-helm-config-file) 中独行添加 `verbosityLevel: 5`，然后重新安装
  - 使用 `kubectl logs <pod-name>` 获取日志

### <a name="sample-helm-config-file"></a>示例 Helm 配置文件
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

