---
title: "使用 Web UI 管理 Azure Kubernetes 群集 | Microsoft Docs"
description: "在 Azure 容器服务中使用 Kubernetes Web 用户 UI"
services: container-service
documentationcenter: 
author: bburns
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: bburns
translationtype: Human Translation
ms.sourcegitcommit: 0aa9b3ae14f586fc79e6ebee898e794d526c19bd
ms.openlocfilehash: 4192369f2e7758200131aa85c60d07436f7cbbdc


---

# <a name="using-the-kubernetes-web-ui-with-azure-container-service"></a>通过 Azure 容器服务使用 Kubernetes Web UI

## <a name="prerequisites"></a>先决条件
本演练假定用户已[使用 Azure 容器服务创建 Kubernetes 群集](container-service-kubernetes-walkthrough.md)，

> [!NOTE]
> Azure 容器服务中的 Kubernetes 支持当前为预览版。
>

并已安装 azure cli 和 kubectl 工具。

可以通过运行以下语句测试是否已安装 `az` 工具：

```console
$ az --version
```

如果尚未安装 `az` 工具，请参阅[此处](https://github.com/azure/azure-cli#installation)的说明。

可以通过运行以下语句测试是否已安装 `kubectl` 工具：

```console
$ kubectl version
```

如果尚未安装 `kubectl`，则可运行：

```console
$ az acs kubernetes install-cli
```

## <a name="overview"></a>概述

### <a name="connect-to-the-web-ui"></a>连接到 Web UI
可以通过运行以下语句启动 Kubernetes Web UI：

```console
$ az acs kubernetes browse
```

此时会打开一个 Web 浏览器，该浏览器已配置为与安全代理通信，将本地机连接到 Kubernetes Web UI。

### <a name="create-and-expose-a-service"></a>创建和公开服务
在 Kubernetes Web UI 中，应该会在右上窗口显示“创建”按钮。

![Kubernetes“创建”UI](media/k8s/create.png)

此时会打开一个对话框，用户可以开始在其中创建应用程序。
将其命名为 `hello-nginx`。 使用 Docker](https://hub.docker.com/_/nginx/) 中的 [`nginx` 容器，部署此 Web 服务的三个副本。

![Kubernetes Pod“创建”对话框](media/k8s/nginx.png)

继续操作，创建“外部”Kubernetes 服务，以便对三个副本的流量进行负载均衡。  选择“外部”并输入端口 80。

![Kubernetes 服务“创建”对话框](media/k8s/service.png)

最后，按“部署”按钮部署这些容器和服务。

![Kubernetes 部署](media/k8s/deploy.png)

### <a name="view-your-containers"></a>查看容器
按“部署”后，UI 会显示部署时的服务的视图：

![Kubernetes 状态](media/k8s/status.png)

可以查看 UI 左侧圆圈中每个 Kubernetes 对象的状态。 如果该圆圈不完整，则对象仍然处于部署状态。 对象在完全部署以后会显示绿色的复选标记：

![Kubernetes 已部署](media/k8s/deployed.png)

所有项都运行以后，即可单击某个 Pod，查看正在运行的 Web 服务的详细信息

![Kubernetes Pod](media/k8s/pods.png)

在特定于 Pod 的视图中，用户可以在 Pod 中查看容器以及这些容器所使用的 CPU 和内存资源的相关信息：

![Kubernetes 资源](media/k8s/resources.png)

如果看不到资源，则可能需要等待几分钟时间，以便监视数据完成传播。

也可单击“日志”链接，查看容器的日志：

![Kubernetes 日志](media/k8s/logs.png)

### <a name="viewing-your-service"></a>查看服务
除了运行容器，Kubernetes UI 还创建外部 `Service`，用于预配负载均衡器，以便将流量带到群集中的容器。

可以单击左侧导航窗格中的“服务”，以便查看所有服务（目前只应有一个服务）

![Kubernetes 服务](media/k8s/service-deployed.png)

在该视图中，应能够看到分配给服务的外部 IP 地址。
如果单击该 IP 地址，则应看到在负载均衡器后运行的 nginx 容器。

![nginx 视图](media/k8s/nginx-page.png)

### <a name="resizing-your-service"></a>调整服务大小
除了在 UI 中查看对象，还可以编辑和更新 Kubernetes API 对象。

首先，请转到服务的 `Deployment`，方法是单击左侧导航窗格中的“部署”。

进入该视图后，单击“ReplicaSet”，然后单击导航栏上部的“编辑”按钮：

![Kubernetes 编辑](media/k8s/edit.png)

将 `spec.replicas` 字段编辑为“2”，然后按“更新”。

这样会删除一个 Pod，导致副本数降到&2;。

 




<!--HONumber=Jan17_HO4-->


