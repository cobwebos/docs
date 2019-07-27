---
title: 运行 Azure Kubernetes 服务-文本分析
titleSuffix: Azure Cognitive Services
description: 使用情绪分析映像将文本分析容器部署到 Azure Kubernetes 服务, 并在 web 浏览器中对其进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 44ee5fab5b4e8900b823453e5674fc9bdb5fe9ac
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68552278"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-service"></a>将情绪分析容器部署到 Azure Kubernetes 服务

了解如何通过情绪分析映像将 Azure 认知[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)服务部署到 Azure Kubernetes 服务 (AKS)。 此过程说明如何创建文本分析资源、如何创建关联的情绪分析映像, 以及如何从浏览器中执行这两种操作的业务流程。 使用容器可以从管理基础结构中转移, 而不是关注应用程序开发。

## <a name="prerequisites"></a>系统必备

此过程要求必须在本地安装和运行多个工具。 不要使用 Azure Cloud Shell。 需要满足以下条件：

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 文本编辑器, 例如[Visual Studio Code](https://code.visualstudio.com/download)。
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)安装。
* [KUBERNETES CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)已安装。
* 具有适当定价层的 Azure 资源。 并非所有定价层都适用于此容器：
    * 仅具有 F0 或标准定价层的**Azure 文本分析**资源。
    * 具有 S0 定价层的**Azure 认知服务**资源。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-a-text-analytics-container-to-an-aks-cluster"></a>将文本分析容器部署到 AKS 群集

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

1. 在文本编辑器中, 创建一个名为_情绪. yaml_的新文件, 并将以下 yaml 粘贴到其中。 请确保将和`billing/value` `apikey/value`替换为自己的信息。

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
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # < Your endpoint >
            - name: apikey
              value: # < Your API Key >
     
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
1. 运行 Kubernetes `apply`命令, 并将_情绪_作为其目标:

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    在该命令成功应用部署配置后, 会显示一条类似于以下输出的消息:

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. 验证是否已部署 pod:

    ```console
    kubectl get pods
    ```

    Pod 的运行状态的输出:

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 验证服务是否可用, 并获取 IP 地址。

    ```console
    kubectl get services
    ```

    Pod 中_情绪_服务的运行状态的输出:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the sentiment analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>后续步骤

* 使用更多[认知服务容器](../../cognitive-services-container-support.md)
* 使用[文本分析连接服务](../vs-text-connected-service.md)
