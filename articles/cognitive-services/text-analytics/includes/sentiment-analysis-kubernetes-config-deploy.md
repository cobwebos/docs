---
title: 情绪分析 - Kubernetes 配置和部署步骤
titleSuffix: Azure Cognitive Services
description: 情绪分析 - Kubernetes 配置和部署步骤
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: ca8d4d725ff25687d1005ddab1964316a147c730
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779437"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>将情绪分析容器部署到 AKS 群集

1. 打开 Azure CLI，登录到 Azure。

    ```azurecli
    az login
    ```

1. 登录到 AKS 群集。 将 `your-cluster-name` 和 `your-resource-group` 替换为相应的值。

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    此命令在运行后会报告类似以下内容的消息：

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > 如果在 Azure 帐户上有多个可用订阅，而 `az aks get-credentials` 命令返回错误，则表明你使用了错误的订阅，这是一个常见问题。 将 Azure CLI 会话的上下文设置为使用你在创建这些资源时使用的订阅，然后重试。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 打开所选的文本编辑器。 此示例使用 Visual Studio Code。

    ```console
    code .
    ```

1. 在文本编辑器中创建名为“sentiment.yaml”的新文件，然后将以下 YAML 粘贴到其中。 请确保将 `billing/value` 和 `apikey/value` 替换为自己的信息。

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
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
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. 保存该文件并关闭文本编辑器。
1. 运行 Kubernetes 的 `apply` 命令，将“sentiment.yaml”文件作为其目标：

    ```console
    kubectl apply -f sentiment.yaml
    ```

    当命令成功应用部署配置以后，会出现类似于以下输出的消息：

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. 验证 Pod 是否已部署：

    ```console
    kubectl get pods
    ```

    Pod 的运行状态输出：

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 验证服务是否可用，然后获取 IP 地址。

    ```console
    kubectl get services
    ```

    Pod 中情绪服务的运行状态输出：

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
