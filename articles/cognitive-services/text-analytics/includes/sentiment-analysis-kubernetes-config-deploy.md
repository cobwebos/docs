---
title: 情绪分析库伯内斯配置和部署步骤
titleSuffix: Azure Cognitive Services
description: 情绪分析库伯内斯配置和部署步骤
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b43299974034f55b57b86191b3556c3d5c2ee83b
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877797"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>将情绪分析容器部署到 AKS 群集

1. 打开 Azure CLI，然后登录到 Azure。

    ```azurecli
    az login
    ```

1. 登录到 AKS 群集。 使用`your-cluster-name`适当的`your-resource-group`值替换和替换。

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    运行此命令后，它会报告类似于以下内容的消息：

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > 如果在 Azure 帐户上有多个可用订阅，而 `az aks get-credentials` 命令返回错误，则表明你使用了错误的订阅，这是一个常见问题。 将 Azure CLI 会话的上下文设置为使用与创建资源相同的订阅，然后重试。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 打开您选择的文本编辑器。 此示例使用可视化工作室代码。

    ```console
    code .
    ```

1. 在文本编辑器中，创建名为 *"情绪.yaml"* 的新文件，并将下面的 YAML 粘贴到其中。 请务必替换`billing/value`并`apikey/value`使用您自己的信息。

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

1. 保存文件，然后关闭文本编辑器。
1. 以*情绪.yaml* `apply`文件为目标运行 Kubernetes 命令：

    ```console
    kubectl apply -f sentiment.yaml
    ```

    命令成功应用部署配置后，将显示一条消息，类似于以下输出：

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. 验证是否部署了该窗格：

    ```console
    kubectl get pods
    ```

    窗格运行状态的输出：

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 验证服务是否可用，并获取 IP 地址。

    ```console
    kubectl get services
    ```

    窗格中*情绪*服务运行状态的输出：

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
