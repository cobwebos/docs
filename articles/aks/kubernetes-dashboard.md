---
title: 使用 Web UI 管理 Azure Kubernetes 群集
description: 了解如何使用 Azure Kubernetes 服务 (AKS) 中的内置 Kubernetes Web UI 仪表板
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/09/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 65525114f46002c5b9300f6bbabcee06cc27ef3a
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091132"
---
# <a name="access-the-kubernetes-dashboard-with-azure-kubernetes-service-aks"></a>访问 Azure Kubernetes 服务 (AKS) 中的 Kubernetes 仪表板

Kubernetes 包含一个可用于基本管理操作的 Web 仪表板。 本文介绍如何使用 Azure CLI 访问 Kubernetes 仪表板，然后引导你完成一些基本的仪表板操作。 有关 Kubernetes 仪表板的详细信息，请参阅 [Kubernetes Web UI 仪表板][kubernetes-dashboard]。

## <a name="before-you-begin"></a>开始之前

本文档详述的步骤假设你已创建 AKS 群集并已通过该群集建立 `kubectl` 连接。 如果需要创建 AKS 群集，请参阅 [AKS 快速入门][aks-quickstart]。

还需安装并配置 Azure CLI 2.0.27 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="start-kubernetes-dashboard"></a>启动 Kubernetes 仪表板

若要启动 Kubernetes 仪表板，请使用 [az aks browse][az-aks-browse] 命令。 以下示例将为 *myResourceGroup* 资源组中的 *myAKSCluster* 群集打开仪表板：

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

此命令在开发系统与 Kubernetes API 之间创建一个代理，并在 Web 浏览器中打开 Kubernetes 仪表板。

### <a name="for-rbac-enabled-clusters"></a>对于启用了 RBAC 的群集

如果 AKS 群集使用 RBAC，则必须先创建 *ClusterRoleBinding*，然后才能正确访问仪表板。 若要创建绑定，请使用 [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] 命令，如以下示例所示。 

> [!WARNING]
> 此示例绑定不应用任何其他身份验证组件，因此可能会导致不安全的使用。 Kubernetes 仪表板将对有权访问该 URL 的任何人开放。 请勿公开 Kubernetes 仪表板。
>
> 可以使用持有者令牌或用户名/密码等机制来控制可以访问仪表板的人员以及他们拥有的权限。 这样可以更安全地使用仪表板。 有关使用不同身份验证方法的详细信息，请参阅有关[访问控制][dashboard-authentication]的 Kubernetes 仪表板 wiki。

```console
kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
```

现在可以访问启用了 RBAC 的群集中的 Kubernetes 仪表板。 若要启动 Kubernetes 仪表板，请使用 [az aks browse][az-aks-browse] 命令，如上一步所述。

## <a name="run-an-application"></a>运行应用程序

在 Kubernetes 仪表板中，单击右上方窗口中的“创建”按钮。 将部署命名为 `nginx`，输入 `nginx:latest` 作为容器映像名称。 在“服务”下，选择“外部”，对于端口和目标端口，都输入 `80`。

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
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-browse]: /cli/azure/aks#az-aks-browse
