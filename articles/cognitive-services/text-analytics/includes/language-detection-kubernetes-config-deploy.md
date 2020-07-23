---
title: 语言检测 Kubernetes 配置和部署步骤
titleSuffix: Azure Cognitive Services
description: 语言检测 Kubernetes 配置和部署步骤
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: dc344bd6e4927d39b72ba9af3ae2eeb61d9cec95
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80877794"
---
### <a name="deploy-the-language-detection-container-to-an-aks-cluster"></a>将语言检测容器部署到 AKS 群集

1. 打开 Azure CLI，并登录到 Azure。

    ```azurecli
    az login
    ```

1. 登录到 AKS 群集。 `your-cluster-name` `your-resource-group` 用适当的值替换和。

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    运行此命令后，它将报告类似于以下内容的消息：

    ```output
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > 如果在 Azure 帐户上有多个可用订阅，而 `az aks get-credentials` 命令返回错误，则表明你使用了错误的订阅，这是一个常见问题。 将 Azure CLI 会话的上下文设置为使用创建资源时使用的同一订阅，然后重试。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 打开所选的文本编辑器。 此示例使用 Visual Studio Code。

    ```console
    code .
    ```

1. 在文本编辑器中，创建名为*yaml*的新文件，然后将以下 yaml 粘贴到其中。 请确保将和替换为 `billing/value` `apikey/value` 自己的信息。

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: language
    spec:
      template:
        metadata:
          labels:
            app: language-app
        spec:
          containers:
          - name: language
            image: mcr.microsoft.com/azure-cognitive-services/language
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: language
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: language-app
    ```

1. 保存该文件并关闭文本编辑器。
1. 运行 Kubernetes `apply` 命令，并将*yaml*文件作为其目标：

    ```console
    kubectl apply -f language.yaml
    ```

    在该命令成功应用部署配置后，会显示一条类似于以下输出的消息：

    ```output
    deployment.apps "language" created
    service "language" created
    ```
1. 验证是否已部署 pod：

    ```console
    kubectl get pods
    ```

    Pod 的运行状态的输出：

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    language-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 验证服务是否可用，并获取 IP 地址。

    ```console
    kubectl get services
    ```

    Pod 中*语言*服务的运行状态的输出：

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    language     LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
