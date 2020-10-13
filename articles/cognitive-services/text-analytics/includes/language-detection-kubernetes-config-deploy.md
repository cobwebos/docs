---
title: 语言检测 - Kubernetes 配置和部署步骤
titleSuffix: Azure Cognitive Services
description: 语言检测 - Kubernetes 配置和部署步骤
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1edca9cf8449ff386d0a9920e7d80d69692536fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779529"
---
### <a name="deploy-the-language-detection-container-to-an-aks-cluster"></a>将语言检测容器部署到 AKS 群集

1. 打开 Azure CLI，登录到 Azure。

    ```azurecli
    az login
    ```

1. 登录到 AKS 群集。 将 `your-cluster-name` 和 `your-resource-group` 替换为相应的值。

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    此命令在运行后会报告类似以下内容的消息：

    ```output
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

1. 在文本编辑器中创建名为“language.yaml”的新文件，然后将以下 YAML 粘贴到其中。 请确保将 `billing/value` 和 `apikey/value` 替换为自己的信息。

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
1. 运行 Kubernetes `apply` 命令，并将 language.yaml 文件作为其目标：

    ```console
    kubectl apply -f language.yaml
    ```

    当命令成功应用部署配置以后，会出现类似于以下输出的消息：

    ```output
    deployment.apps "language" created
    service "language" created
    ```
1. 验证 Pod 是否已部署：

    ```console
    kubectl get pods
    ```

    Pod 的运行状态输出：

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    language-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 验证服务是否可用，然后获取 IP 地址。

    ```console
    kubectl get services
    ```

    Pod 中语言服务的运行状态输出：

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    language     LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
