---
title: 通过 Kubernetes 仪表板监视 Azure Stack Edge Pro 设备 |Microsoft Docs
description: 描述如何访问和使用 Kubernetes 仪表板来监视 Azure Stack Edge Pro 设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 137cff47d49be1405f60bc47cd16f7f027ab63a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320823"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>使用 Kubernetes 仪表板监视 Azure Stack Edge Pro GPU 设备

本文介绍如何访问和使用 Kubernetes 仪表板来监视 Azure Stack Edge Pro GPU 设备。 若要监视你的设备，你可以在 Azure 门户中使用图表，查看 Kubernetes 仪表板，或者 `kubectl` 通过设备的 PowerShell 界面运行命令。 

本文仅重点介绍可在 Kubernetes 仪表板上执行的监视任务。

在本文中，学习如何：

> [!div class="checklist"]
>
> * 访问设备上的 Kubernetes 仪表板
> * 查看设备上部署的模块
> * 获取设备上部署的应用程序的 IP 地址
> * 查看设备上部署的模块的容器日志


## <a name="about-kubernetes-dashboard"></a>关于 Kubernetes 仪表板

Kubernetes 仪表板是一种基于 web 的用户界面，可用于对容器化应用程序进行故障排除。 Kubernetes 仪表板是 Kubernetes 命令行的基于 UI 的替代项 `kubectl` 。 有关详细信息，请参阅 [Kubernetes 仪表板](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)。 

在 Azure Stack Edge Pro 设备上，你可以在 *只读* 模式下使用 Kubernetes 仪表板来大致了解在 Azure Stack Edge Pro 设备上运行的应用程序，查看 Kubernetes 群集资源的状态，并查看设备上发生的任何错误。

## <a name="access-dashboard"></a>访问面板

Kubernetes 仪表板为 *只读* ，并在端口31000上的 Kubernetes 主节点上运行。 请按照以下步骤访问仪表板： 

1. 在设备的本地 UI 中，中转到 " **设备** "，并中转到 " **设备终结点**"。 
1. 选择 " **下载配置** " 以下载 `kubeconfig` 允许访问仪表板的。 将该 `config.json` 文件保存到你的本地系统。
1. 选择 Kubernetes 仪表板 URL，在浏览器中打开仪表板。

    ![本地 UI 中设备页面中的 Kubernetes 仪表板 URL](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. 在 **Kubernetes 仪表板登录** 页上：
    
    1. 选择 **kubeconfig**。 
        ![选择 kubeconfig 选项](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png) 
    1. 选择省略号 **...**。浏览并指向 `kubeconfig` 你之前在本地系统上下载的。 选择“登录”。
        ![浏览到 kubeconfig 文件](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. 你现在可以在只读模式下查看 Azure Stack Edge Pro 设备的 Kubernetes 仪表板。

    ![Kubernetes 仪表板主页](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>查看模块状态

计算模块是已实现业务逻辑的容器。 你可以使用仪表板来验证是否已在 Azure Stack Edge Pro 设备上成功部署了计算模块。

若要查看模块状态，请在仪表板上执行以下步骤：

1. 在仪表板的左窗格中，找到 " **命名空间**"。 按 IoT Edge 模块显示的命名空间进行筛选，在本例中为 **iotedge**。
1. 在左窗格中，中转到 " **工作负载" > 部署**。
1. 在右侧窗格中，你将看到在设备上部署的所有模块。 在这种情况下，GettingStartedWithGPU 模块部署在 Azure Stack Edge Pro 上。 你可以看到该模块已部署。

    ![查看模块部署](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>获取服务或模块的 IP 地址

你可以使用仪表板获取要在 Kubernetes 群集外部公开的服务或模块的 IP 地址。 

通过 " **计算网络设置** " 页中设备的本地 web UI，为这些外部服务分配 IP 范围。 部署 IoT Edge 模块后，你可能需要获取分配给特定模块或服务的 IP 地址。 

若要获取 IP 地址，请在仪表板上执行以下步骤：

1. 在仪表板的左窗格中，找到 " **命名空间**"。 按部署外部服务的命名空间（在本例中为 **iotedge**）进行筛选。
1. 在左窗格中，找到 " **发现和负载平衡 > 服务**"。
1. 在右侧窗格中，你将看到在 `iotedge` Azure Stack Edge Pro 设备上的命名空间中运行的所有服务。

    ![获取外部服务的 IP](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>查看容器日志

在某些情况下，你需要查看容器日志。 你可以使用仪表板获取已在 Kubernetes 群集上部署的特定容器的日志。

若要查看容器日志，请在仪表板上执行以下步骤：

1. 在仪表板的左窗格中，找到 " **命名空间**"。 按部署 IoT Edge 模块的命名空间进行筛选，在本例中为 **iotedge**。
1. 在左侧窗格中，请参阅 **工作负荷 >** pod。
1. 在右侧窗格中，你将看到设备上运行的所有 pod。 标识运行要查看其日志的模块的 pod。 选择所标识的 pod 的垂直省略号，并从上下文菜单中选择 " **日志**"。

    ![查看容器日志1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. 日志显示在仪表板内置的日志查看器中。 你还可以下载日志。

    ![查看容器日志2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>查看 CPU，内存使用情况

Azure Stack Edge Pro 设备的 Kubernetes 仪表板还具有一个 [指标服务器加载项](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/) ，用于聚合 Kubernetes 资源中的 CPU 和内存使用情况。
 
例如，你可以查看所有命名空间中的部署所消耗的 CPU 和内存。 

![查看所有部署中的 CPU 和内存使用情况](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

您还可以按特定的命名空间进行筛选。 在下面的示例中，你可以仅查看 Azure Arc 部署的 CPU 和内存消耗。  

![查看 Azure Arc 部署的 CPU 和内存使用情况](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

Kubernetes 指标服务器提供自动缩放管道，如 [水平 Pod 自动缩放程序](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)。


## <a name="next-steps"></a>后续步骤

了解如何 [排查设备问题](azure-stack-edge-gpu-troubleshoot.md)。
