---
title: Azure 容器服务教程 - 部署应用程序
description: Azure 容器服务教程 - 部署应用程序
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: f8f626143e74d65fa9d4e37e1e2bfda37501f102
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32162766"
---
# <a name="run-applications-in-kubernetes"></a>在 Kubernetes 中运行应用程序

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

在本教程中（第 4 部分，共 7 部分），示例应用程序会部署到 Kubernetes 群集中。 已完成的步骤包括：

> [!div class="checklist"]
> * 更新 Kubernetes 清单文件
> * 在 Kubernetes 中运行应用程序
> * 测试应用程序

在后续教程中，会扩大并更新此应用程序，并将 Log Analytics 配置为监视 Kubernetes 群集。

本教程假定你基本了解 Kubernetes 概念。有关 Kubernetes 的详细信息，请参阅 [Kubernetes 文档](https://kubernetes.io/docs/home/)。

## <a name="before-you-begin"></a>开始之前

在前面的教程中，我们已将应用程度打包到容器映像中，将此映像上传到 Azure 容器注册表，并创建了 Kubernetes 群集。 

必须先预创建 `azure-vote-all-in-one-redis.yml` Kubernetes 清单文件，然后才能完成本教程。 此文件是在上一教程中与应用程序源代码一同下载。 验证是否已克隆存储库，并且是否已将目录更改为克隆的存储库。

如果尚未完成这些步骤，并且想要逐一完成，请返回到[教程 1 – 创建容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。 

## <a name="update-manifest-file"></a>更新清单文件

在本教程中，Azure 容器注册表 (ACR) 用于存储容器映像。 运行应用程序前，需要先在 Kubernetes 清单文件中更新 ACR 登录服务器名称。

使用 [az acr list](/cli/azure/acr#az_acr_list) 命令获取 ACR 登录服务器名称。

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

此清单文件已预创建，其中包含 `microsoft` 的登录服务器名称。 使用任意文本编辑器打开此文件。 在此示例中，清单文件是使用 `vi` 打开的。

```bash
vi azure-vote-all-in-one-redis.yml
```

将 `microsoft` 替换为 ACR 登录服务器名称。 此值位于清单文件的第 47 行。

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

保存并关闭该文件。

## <a name="deploy-application"></a>部署应用程序

使用 [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) 命令运行该应用程序。 此命令分析清单文件并创建定义的 Kubernetes 对象。

```azurecli-interactive
kubectl create -f azure-vote-all-in-one-redis.yml
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

若要监视进度，请将 [kubectl get service](https://review.docs.microsoft.com/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) 命令与 `--watch` 参数配合使用。

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

`azure-vote-front` 服务的 EXTERNAL-IP 一开始显示为 `pending`。 当 EXTERNAL-IP 地址从 `pending` 变成 `IP address` 后，立即运行 `CTRL-C` 停止 kubectl 监视进程。

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