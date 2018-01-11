---
title: "使用 Web UI 管理 Azure Kubernetes 群集"
description: "使用 AKS 中的 Kubernetes 仪表板"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 11/15/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: ca828dab7bdb47e41596be2717598cfe828953ca
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="kubernetes-dashboard-with-azure-container-service-aks"></a>Azure 容器服务 Service (AKS) 中的 Kubernetes 仪表板

可以使用 Azure CLI 启动 Kubernetes 仪表板。 本文档将指导你使用 Azure CLI 启动 Kubernetes 仪表板，它还指导你执行一些基本的仪表板操作。 有关 Kubernetes 仪表板的详细信息，请参阅 [Kubernetes Web UI 仪表板][kubernetes-dashboard]。

## <a name="before-you-begin"></a>开始之前

本文档详述的步骤假设你已创建 AKS 群集并已通过该群集建立 kubectl 连接。 如果需要这些项，请参阅 [AKS 快速入门][aks-quickstart]。

还需安装并配置 Azure CLI 2.0.21 或更高版本。 若要查找版本，请运行 az --version。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="start-kubernetes-dashboard"></a>启动 Kubernetes 仪表板

可使用 `az aks browse` 命令启动 Kubernetes 仪表板。 运行此命令时，请替换资源组和群集名称。

```azurecli
az aks browse --resource-group myResourceGroup --name myK8SCluster
```

此命令在开发系统与 Kubernetes API 之间创建一个代理，并在 Web 浏览器中打开 Kubernetes 仪表板。

## <a name="run-an-application"></a>运行应用程序

在 Kubernetes 仪表板中，单击右上方窗口中的“创建”按钮。 将部署命名为 `nginx`，对于映像名称，输入 `nginx:latest`。 在“服务”下，选择“外部”，对于端口和目标端口，都输入 `80`。

完成后，单击“部署”以创建部署。

![Kubernetes 服务“创建”对话框](./media/container-service-kubernetes-ui/create-deployment.png)

## <a name="view-the-application"></a>查看应用程序

可以在 Kubernetes 仪表板上看到有关应用程序的状态。 当应用程序在运行后，每个组件旁边都有一个绿色的对号。

![Kubernetes Pod](./media/container-service-kubernetes-ui/complete-deployment.png)

若要查看有关应用程序 Pod 的详细信息，请在左侧菜单中单击“Pod”，然后选择“NGINX”Pod。 在此处，可以看到特定于 Pod 的信息，例如资源消耗。

![Kubernetes 资源](./media/container-service-kubernetes-ui/running-pods.png)

若要查找应用程序的 IP 地址，请在左侧菜单中单击“服务”，然后选择“NGINX”服务。

![nginx 视图](./media/container-service-kubernetes-ui/nginx-service.png)

## <a name="edit-the-application"></a>编辑应用程序

除了创建和查看应用程序之外，Kubernetes 仪表板还可以用来编辑和更新应用程序部署。

若要编辑部署，请在左侧菜单中单击“部署”，然后选择“NGINX”部署。 最后，在右上角的导航栏中选择“编辑”。

![Kubernetes 编辑](./media/container-service-kubernetes-ui/view-deployment.png)

找到 `spec.replica` 值，该值应为 1，将该值更改为 3。 这样，NGINX 部署的副本计数从 1 增加到了 3。

在完成后，选择“更新”。

![Kubernetes 编辑](./media/container-service-kubernetes-ui/edit-deployment.png)

## <a name="next-steps"></a>后续步骤

有关 Kubernetes 仪表板的详细信息，请参阅 Kubernetes 文档。

> [!div class="nextstepaction"]
> [Kubernetes Web UI 仪表板][kubernetes-dashboard]

<!-- LINKS - external -->
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli