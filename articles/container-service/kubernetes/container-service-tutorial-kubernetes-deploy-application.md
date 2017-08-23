---
title: "Azure 容器服务教程 - 部署应用程序 | Microsoft Docs"
description: "Azure 容器服务教程 - 部署应用程序"
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Kubernetes, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 81a6a3d5364642b2da75faf875d64d2f4a1939d4
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---

# <a name="run-applications-in-kubernetes"></a>在 Kubernetes 中运行应用程序

在本教程中（第 4 部分，共 7 部分），示例应用程序会部署到 Kubernetes 群集中。 已完成的步骤包括：

> [!div class="checklist"]
> * 下载 Kubernetes 清单文件
> * 在 Kubernetes 中运行应用程序
> * 测试应用程序

在后续教程中，会扩大并更新此应用程序，并将 Operations Management Suite 配置为监视 Kubernetes 群集。

本教程假定你基本了解 Kubernetes 概念。有关 Kubernetes 的详细信息，请参阅 [Kubernetes 文档](https://kubernetes.io/docs/home/)。

## <a name="before-you-begin"></a>开始之前

在前面的教程中，我们已将应用程度打包到容器映像中，将此映像上传到 Azure 容器注册表，并创建了 Kubernetes 群集。 如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 – 创建容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。 

本教程至少需要 Kubernetes 群集。

## <a name="get-manifest-file"></a>获取清单文件

对于本教程，使用 Kubernetes 清单部署 [Kubernetes 对象](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)。 Kubernetes 清单是 YAML 或 JSON 格式的文件，其中包含 Kubernetes 对象部署和配置说明。

本教程的应用程序清单文件可在 Azure 投票应用程序存储库（在前面的教程中进行了克隆）中获取。 如果尚未这样做，请使用以下命令克隆该存储库： 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

清单文件位于克隆存储库的以下目录中。

```bash
/azure-voting-app-redis/kubernetes-manifests/azure-vote-all-in-one-redis.yaml
```

## <a name="update-manifest-file"></a>更新清单文件

如果使用 Azure 容器注册表存储容器映像，则需使用 ACR loginServer 名称更新清单。

使用 [az acr list](/cli/azure/acr#list) 命令获取 ACR 登录服务器名称。

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

已使用存储库名称 microsoft 预先创建了示例清单。 使用任意文本编辑器打开文件，然后将 microsoft 值替换为 ACR 实例的登录服务器名称。

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

## <a name="deploy-application"></a>部署应用程序

使用 [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) 命令运行该应用程序。 此命令分析清单文件并创建定义的 Kubernetes 对象。

```azurecli-interactive
kubectl create -f ./azure-voting-app-redis/kubernetes-manifests/azure-vote-all-in-one-redis.yaml
```

输出：

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>测试应用程序

创建向 Internet 公开应用程序的 [Kubernetes 服务](https://kubernetes.io/docs/concepts/services-networking/service/)。 此过程可能需要几分钟。 

若要监视进度，请将 [kubectl get service](https://review.docs.microsoft.com/en-us/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) 命令与 `--watch` 参数配合使用。

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

起初，azure-vote-front 服务的 EXTERNAL-IP 显示为“挂起”。 EXTERNAL-IP 地址从“挂起”变为 IP 地址以后，请使用 `CTRL-C` 停止 kubectl 监视进程。

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

若要查看应用程序，请浏览到外部 IP 地址。

![Azure 上的 Kubernetes 群集映像](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>后续步骤

在本教程中，Azure 投票应用程序部署到 Azure 容器服务 Kubernetes 群集。 已完成的任务包括：  

> [!div class="checklist"]
> * 下载 Kubernetes 清单文件
> * 在 Kubernetes 中运行应用程序
> * 已测试应用程序

前进到下一个教程，以了解如何缩放 Kubernetes 应用程序和底层 Kubernetes 基础结构。 

> [!div class="nextstepaction"]
> [缩放 Kubernetes 应用程序和基础结构](./container-service-tutorial-kubernetes-scale.md)
