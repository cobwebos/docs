---
title: 运行 Azure Kubernetes 服务
titleSuffix: Text Analytics - Azure Cognitive Services
description: 将使用情绪分析图像的文本分析容器部署到 Azure Kubernetes 服务，并在 web 浏览器中进行测试。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: 4d5e1da01be531550915a38bed17dd8e57be907a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454946"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-kubernetes-services-aks"></a>将情绪分析容器部署到 Azure Kubernetes 服务 (AKS)

了解如何部署认知服务[文本分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)情绪分析图像 Azure Kubernetes 服务 (AKS) 的容器。 此过程其中演示了如何创建文本分析的资源、 关联的情绪分析图像和能够练习从浏览器两个此业务流程的创建。 使用容器可以移动开发人员的注意力从管理到改为将重点放在应用程序开发的基础结构。

## <a name="prerequisites"></a>必备组件

此过程要求必须在本地安装和运行多个工具。 请勿使用 Azure Cloud Shell。

* 使用 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。
* 文本编辑器，例如：[Visual Studio Code](https://code.visualstudio.com/download)。
* 安装 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。
* 安装[Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/)。
* 具有适当定价层的 Azure 资源。 并非所有定价层都适用于此容器：
    * 仅具有 F0 或标准定价层的文本分析资源  。
    * 具有 S0 定价层的认知服务资源  。

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Kubernetes Services (AKS)](../../containers/includes/create-aks-resource.md)]

## <a name="deploy-text-analytics-container-to-an-aks-cluster"></a>将文本分析容器部署到 AKS 群集

1. 打开 Azure CLI 并登录到 Azure

    ```azurecli
    az login
    ```

1. 登录到 AKS 群集 (替换`your-cluster-name`和`your-resource-group`使用适当的值)

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    此命令执行后，它会报告一条类似于以下消息：

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > 如果你有多个订阅可以在你的 Azure 帐户和`az aks get-credentials`命令返回的错误消息，一个常见的问题是使用错误的订阅。 只需设置上下文的 Azure CLI 会话用于创建具有资源的同一个订阅，然后重试。
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. 打开所选的文本编辑器 (此示例使用__Visual Studio Code__):

    ```azurecli
    code .
    ```

1. 在文本编辑器中，创建名为的新文件_sentiment.yaml_并将以下 YAML 粘贴到其中：

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
              value: "https://westus2.api.cognitive.microsoft.com/"
            - name: apikey
              value: "16c12e3419f54ba49a3222177cef781d"
     
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
1. 执行 Kubernetes`apply`命令_sentiment.yaml_作为其目标：

    ```console
    kuberctl apply -f sentiment.yaml
    ```

    该命令已成功应用的部署配置后，类似于以下的消息是输出：

    ```
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. 验证已部署在 POD:

    ```console
    kubectl get pods
    ```

    这将输出 POD 的正在运行状态：

    ```
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. 验证服务可用并获取 IP 地址：

    ```console
    kubectl get services
    ```

    这将输出的运行状态_情绪_服务 POD:

    ```
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>后续步骤

* 使用更多[认知服务容器](../../cognitive-services-container-support.md)
* 使用[文本分析连接的服务](../vs-text-connected-service.md)