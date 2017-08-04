---
title: "在 Azure Kubernetes 中使用 Helm 部署容器 | Microsoft Docs"
description: "使用 Helm 打包工具在 Azure 容器服务中的 Kubernetes 群集上部署容器"
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/10/2017
ms.author: saudas
ms.custom: 
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 703b516c8fd443d2bb751530a8f62496b020cb5d
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---
# <a name="use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>使用 Helm 在 Kubernetes 群集上部署容器 

[Helm](https://github.com/kubernetes/helm/) 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。 与诸如 pt-get 和 Yum 的 Linux 包管理器类似，Helm 用于管理 Kubernetes 图表，这些图表是预配置的 Kubernetes 资源包。 本文演示了如何在 Azure 容器服务中部署的 Kubernetes 群集上使用 Helm。

Helm 具有两个组件： 
* **Helm CLI** 是在计算机上在本地或在云中运行的客户端  

* **Tiller** 是在 Kubernetes 群集上运行并管理 Kubernetes 应用程序生命周期的服务器 
 
## <a name="prerequisites"></a>先决条件

* 在 Azure 容器服务中[创建 Kubernetes 群集](container-service-kubernetes-walkthrough.md)

* 在本地计算机上[安装和配置 `kubectl`](../container-service-connect.md)

* 在本地计算机上[安装 Helm](https://github.com/kubernetes/helm/blob/master/docs/install.md)

## <a name="helm-basics"></a>Helm 基础知识 

若要查看有关安装 Tiller 并向其部署应用程序的 Kubernetes 群集的信息，请键入以下命令：

```bash
kubectl cluster-info 
```
![kubectl cluster-info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
安装好 Helm 后，通过键入以下命令在 Kubernetes 群集上安装 Tiller：

```bash
helm init --upgrade
```
安装成功完成后，将看到如下输出：

![Tiller 安装](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
若要查看在存储库中可用的所有 Helm 图表，请键入以下命令：

```bash 
helm search 
```

将看到如下输出：

![Helm 搜索](./media/container-service-kubernetes-helm/helm-search.png)
 
若要更新图表以获取最新版本，请键入：

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>部署 Nginx 入口控制器图表 
 
若要部署 Nginx 入口控制器图表，请键入单个命令：

```bash
helm install stable/nginx-ingress 
```
![部署入口控制器](./media/container-service-kubernetes-helm/nginx-ingress.png)

如果键入 `kubectl get svc` 以查看在群集上运行的所有服务，则会看到 IP 地址已分配给入口控制器。 （如果正在进行分配，请查看 `<pending>`。 该过程需要几分钟时间完成。） 

分配好 IP 地址后，导航到外部 IP 地址的值，查看 Nginx 是否在后端运行。 
 
![入口 IP 地址](./media/container-service-kubernetes-helm/ingress-ip-address.png)


若要查看在群集上安装的图表列表，请键入：

```bash
helm list 
```

可以将命令缩写为 `helm ls`。
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>部署 MariaDB 图表和客户端

现在部署 MariaDB 图表和 MariaDB 客户端以连接到数据库。

若要部署 MariaDB 图表，请键入以下命令：

```bash
helm install --name v1 stable/mariadb
```

其中，`--name` 是用于发布的标记。

> [!TIP]
> 如果部署失败，运行 `helm repo update`，并重试。
>
 
 
若要查看在群集上部署的所有图表，请键入：

```bash 
helm list
```
 
若要查看在群集上运行的所有部署，请键入：

```bash
kubectl get deployments 
``` 
 
 
最后，若要运行 Pod 以访问客户端，请键入：

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
要连接到客户端，请键入以下命令，将 `v1-mariadb` 替换为部署名称：

```bash
sudo mysql –h v1-mariadb
```
 
 
现在，可以使用标准 SQL 命令创建数据库、表等。例如：`Create DATABASE testdb1;` 将创建一个空数据库。 
 
 
 
## <a name="next-steps"></a>后续步骤

* 有关管理 Kubernetes 图表的详细信息，请参阅 [Helm 文档](https://github.com/kubernetes/helm/blob/master/docs/index.md)。 


