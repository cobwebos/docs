---
title: 应用程序网关入口控制器故障排除
description: 本文提供了有关如何排查应用程序网关入口控制器的常见问题和/或问题的文档。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 10/11/2019
ms.author: caya
ms.openlocfilehash: 6ee6239f9b1fbb66bac5d3920a888fb5a288a300
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513323"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>诊断入口控制器的常见问题或问题

[Azure Cloud Shell](https://shell.azure.com/)是解决 AKS 和 AGIC 安装问题的最简便方法。 从[shell.azure.com](https://shell.azure.com/)或单击链接启动 shell：

[![嵌入启动](https://shell.azure.com/images/launchcloudshell.png "启动 Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>使用简单的 Kubernetes 应用进行测试

下面的步骤假定：
  - 具有启用了高级网络的 AKS 群集
  - AGIC 已安装在 AKS 群集上
  - 已在与 AKS 群集共享的 VNET 上 hav 应用程序网关

若要验证是否正确设置了应用程序网关 + AKS + AGIC 安装，请部署最简单的应用程序：

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

将上述脚本中的所有行一次复制并粘贴到[Azure Cloud Shell](https://shell.azure.com/)中。 请确保复制整个命令-从 `cat` 开始，包括最后一个 `EOF`。

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

在将应用成功部署到 AKS 群集后，将具有新的 Pod、服务和入口。

获取[Cloud Shell](https://shell.azure.com/)： `kubectl get pods -o wide`的 pod 列表。
我们希望创建一个名为 "agic" 的 pod。 它将有一个 IP 地址。 此地址必须在应用程序网关的 VNET 中，此地址与 AKS 一起使用。

![pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

获取服务列表： `kubectl get services -o wide`。 我们应该会看到一个名为 "agic" 的服务。

![pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

获取恒温器： `kubectl get ingress`的列表。 我们希望创建一个名为 "agic" 的入口资源。 资源将具有主机名 "test.agic.contoso.com"。

![pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

其中一个 pod 将为 AGIC。 `kubectl get pods` 将显示一个 pod 列表，其中一个将以 "入口-azure" 开头。 使用 `kubectl logs <name-of-ingress-controller-pod>` 获取该 pod 的所有日志，以验证是否已成功完成部署。 成功的部署将在日志中添加以下行：
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

或者，可以从[Cloud Shell](https://shell.azure.com/)仅检索指示成功的应用程序网关 `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`配置的行，其中 `<ingress-azure....>` 应为 AGIC pod 的确切名称。

应用程序网关将应用以下配置：

- 侦听器： ![侦听器](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- 路由规则： ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- 后端池：
  - 后端地址池中将有一个 IP 地址，并且它将与我们之前观察到的 backend_pool 的 IP 地址相匹配，`kubectl get pods -o wide`
![](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


最后，我们可以使用[Cloud Shell](https://shell.azure.com/)中的 `cURL` 命令与新部署的应用建立 HTTP 连接：

1. 使用 `kubectl get ingress` 获取应用程序网关的公共 IP 地址
2. 使用 `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

`HTTP/1.1 200 OK` 的结果表示应用程序网关 + AKS + AGIC 系统按预期方式工作。


## <a name="inspect-kubernetes-installation"></a>检查 Kubernetes 安装

### <a name="pods-services-ingress"></a>箱，服务，入口
应用程序网关入口控制器（AGIC）持续监视以下 Kubernetes 资源：[部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment)或[Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod)、[服务](https://kubernetes.io/docs/concepts/services-networking/service/)、[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)


若要使 AGIC 正常工作，必须满足以下要求：
  1. AKS 必须有一个或多个正常**盒**。
     使用 `kubectl get pods -o wide --show-labels` [Cloud Shell](https://shell.azure.com/)验证此设置。如果有一个带 `apsnetapp`的 Pod，则输出可能如下所示：
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. 一个或多个**服务**，通过匹配 `selector` 标签来引用上面的 pod。
     通过 `kubectl get services -o wide` [Cloud Shell](https://shell.azure.com/)验证此情况
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. 引用上述服务的**入口**（用 `kubernetes.io/ingress.class: azure/application-gateway`进行了注释）从[Cloud Shell](https://shell.azure.com/)中验证这一点 `kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. 查看上述入口的批注： `kubectl get ingress aspnetapp -o yaml` （将 `aspnetapp` 替换为入口的名称）
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

     入口资源必须用 `kubernetes.io/ingress.class: azure/application-gateway`进行批注。
 

### <a name="verify-observed-namespace"></a>验证观察到的命名空间

* 获取 Kubernetes 群集中的现有命名空间。 应用在哪个命名空间中运行？ 是否 AGIC 监视该命名空间？ 请参阅[多命名空间支持](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support)文档，了解如何正确配置观察到的命名空间。

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* AGIC pod 应位于 `default` 命名空间中（请参阅列 `NAMESPACE`）。 正常的 pod 在 `STATUS` 列中 `Running`。 应至少有一个 AGIC pod。

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* 如果 AGIC pod 不正常（不 `Running`上的命令`STATUS` 列）：
  - 获取日志以了解原因： `kubectl logs <pod-name>`
  - 对于 pod 的上一个实例： `kubectl logs <pod-name> --previous`
  - 描述 pod 以获取更多上下文： `kubectl describe pod <pod-name>`


* 是否有 Kubernetes[服务](https://kubernetes.io/docs/concepts/services-networking/service/)和[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)资源？
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* 你的[入口](https://kubernetes.io/docs/concepts/services-networking/ingress/)是否已用以下内容批注： `kubernetes.io/ingress.class: azure/application-gateway`？ AGIC 将仅监视具有此批注的 Kubernetes 入口资源。
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC 发出针对某些严重错误的 Kubernetes 事件。 可以查看以下内容：
  - 在终端中通过 `kubectl get events --sort-by=.metadata.creationTimestamp`
  - 在浏览器中使用[Kubernetes WEB UI （仪表板）](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>日志记录级别

AGIC 有3个日志记录级别。 第1级是默认值，它显示日志行的最小数目。
另一方面，Level 5 将显示所有日志，包括应用于 ARM 的配置的净化内容。

Kubernetes 社区已建立了[kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging)工具的9个日志记录级别。 在此存储库中，我们使用了其中的三种，具有类似的语义：


| 详细程度 | 说明 |
|-----------|-------------|
|  1        | 默认日志级别;显示启动详细信息、警告和错误 |
|  3        | 有关事件和更改的扩展信息;已创建对象的列表 |
|  5        | 记录封送处理的对象;显示应用于 ARM 的净化的 JSON 配置 |


详细级别可通过[helm yaml](#sample-helm-config-file)文件中的 `verbosityLevel` 变量进行调整。 提高详细级别 `5` 以将 JSON 配置调度到[ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)：
  - 在[helm yaml](#sample-helm-config-file)中单独添加 `verbosityLevel: 5`，并重新安装
  - 获取 `kubectl logs <pod-name>` 的日志

### <a name="sample-helm-config-file"></a>Helm 配置文件示例
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

