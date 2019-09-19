---
title: 关键短语提取 Kubernetes 配置和部署步骤
titleSuffix: Azure Cognitive Services
description: 关键短语提取 Kubernetes 配置和部署步骤
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/19/2019
ms.author: dapine
ms.openlocfilehash: 5ec535fe2ce23a2ead1163e870aae97fd104ef09
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130067"
---
### <a name="deploy-the-key-phrase-extraction-container-to-an-aks-cluster"></a>将关键短语提取容器部署到 AKS 群集

1. 打开 Azure CLI, 并登录到 Azure。

    ```azurecli
    az login
    ```

1. 登录到 AKS 群集。 用`your-cluster-name`适当`your-resource-group`的值替换和。

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    运行此命令后, 它将报告类似于以下内容的消息:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > 如果在 Azure 帐户上有多个可用订阅，而 `az aks get-credentials` 命令返回错误，则表明你使用了错误的订阅，这是一个常见问题。 将 Azure CLI 会话的上下文设置为使用创建资源时使用的同一订阅, 然后重试。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 打开所选的文本编辑器。 此示例使用 Visual Studio Code。

    ```azurecli
    code .
    ```

1. 在文本编辑器中，创建一个名为*关键短语. yaml*的新文件，并将以下 yaml 粘贴到其中。 请确保将和`billing/value` `apikey/value`替换为自己的信息。

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: keyphrase
    spec:
      template:
        metadata:
          labels:
            app: keyphrase-app
        spec:
          containers:
          - name: keyphrase
            image: mcr.microsoft.com/azure-cognitive-services/keyphrase
            ports:
            - containerPort: 5000
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
      name: keyphrase
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: keyphrase-app
    ```

1. 保存该文件并关闭文本编辑器。
1. 运行 Kubernetes `apply`命令，并将*关键短语*文件作为其目标：

    ```console
    kuberctl apply -f keyphrase.yaml
    ```

    在该命令成功应用部署配置后, 会显示一条类似于以下输出的消息:

    ```console
    deployment.apps "keyphrase" created
    service "keyphrase" created
    ```
1. 验证是否已部署 pod:

    ```console
    kubectl get pods
    ```

    Pod 的运行状态的输出:

    ```console
    NAME                         READY     STATUS    RESTARTS   AGE
    keyphrase-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 验证服务是否可用, 并获取 IP 地址。

    ```console
    kubectl get services
    ```

    Pod 中*关键短语*服务的运行状态的输出：

    ```console
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    keyphrase    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
