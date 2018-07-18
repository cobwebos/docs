---
title: 如何为机器学习缩放 Azure 容器服务群集 | Microsoft Docs
description: 缩放 ACS 群集 - 自动缩放和静态缩放；缩放群集中的节点数
services: machine-learning
author: aashishb
ms.author: aashishb
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/04/2017
ms.openlocfilehash: 1f8dbe5ccae34ab185cbe5105ac793a0d401b48e
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2018
ms.locfileid: "34831660"
---
# <a name="scaling-the-cluster-to-manage-web-service-throughput"></a>缩放群集以管理 Web 服务吞吐量

## <a name="why-scale-the-cluster"></a>为何缩放群集？

缩放 Azure 容器服务 (ACS) 群集可有效优化服务吞吐量，同时将群集大小保持在最小以降低成本。 

为了更好地理解自动缩放，请考虑以下示例 - 运行 3 个 Web 服务的群集：

![示例：运行 3 个 Web 服务的群集](media/how-to-scale-clusters/three-services.png)

这些服务的峰值需求各不相同，达到峰值时：服务 1（蓝线）需要 40 个 pod；服务 2（橙线）需要 38 个 pod；服务 3（灰线）需要 50 个 pod。 如果单独为每个服务保留所需的峰值容量，此群集至少共需要 40 + 38 + 50 = 128 个 pod。

但还要考虑到任意时刻的实际 pod 使用情况（在图中用黑色虚线表示）。 本例中，任意时刻所用的最大 pod 数为 64，这发生在 20:00，此时服务 3 达到峰值。 此时，服务 3 使用 50 个 pod，但服务 2 只使用 9 个 pod 而服务 1 仅使用 5 个。 注意，这是此群集的使用率峰值。 这意味着，任何时刻该群集使用的 pod 都不会超过 64 个，与单独缩放 3 个服务计算出的使用率峰值需求 128 个相比，这足足少了一半。

通过重新分配群集中的 pod（即通过重新缩放），以满足每个服务的当前需求，而不只是简单地为所有服务的峰值需求提供足够资源，这样就可以减小群集大小。 在此简单示例中，自动缩放将所需的 pod 数量从 128 减少到 64 个，所需的群集大小减小了一半。

缩放 pod 数量这一操作相对较快，所需时间不超过 1 分钟，因此不会严重影响服务的响应。

> [!NOTE]
> 缩放群集对请求延迟问题并无帮助。 出于操作目的，纵向扩展应增加成功次数且减少服务不可用错误。 

## <a name="how-to-scale-web-services-on-your-acs-cluster"></a>如何缩放 ACS 群集上的 Web 服务

默认情况下，模型管理 CLI 的群集设置选项可在环境中配置 2 个代理和 1 个 pod。 还可安装 Kubernetes CLI。

可以通过调整以下项来缩放部署到 ACS 的 Web 服务：

* 群集中的代理节点数量
* 在代理节点上运行的 Kubernetes pod 副本数量

### <a name="scaling-the-number-of-nodes-in-the-cluster"></a>缩放群集中的节点数量

以下命令设置群集中的代理节点计数：

```
az acs scale -g <resource group> -n <cluster name> --new-agent-count <new scale>
```

完成此设置可能需要几分钟时间。 要深入了解如何缩放群集中的节点数量，请参阅[在容器服务群集中缩放代理节点](https://docs.microsoft.com/azure/container-service/container-service-scale)。

### <a name="scaling-the-number-of-kubernetes-pod-replicas-in-a-cluster"></a>缩放群集中的 Kubernetes pod 副本数量
 
可以使用 Azure 机器学习 CLI 或 [Kubernetes 仪表板] (https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 对分配到群集的 Pod 副本数量进行缩放。

有关 Kubernetes 副本 pod 的详细信息，请参阅 [Kubernetes Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) 文档。

#### <a name="scaling-a-cluster-with-the-azure-machine-learning-cli"></a>使用 Azure 机器学习 CLI 缩放群集

使用 CLI 缩放群集的方法有 2 种：

- 自动缩放
- 静态缩放

创建服务时默认启用自动缩放，在大多数情况下，自动缩放是首选缩放方法。

##### <a name="autoscale"></a>自动缩放

以下命令启用自动缩放并设置服务的最小和最大副本数量。

```
az ml service update realtime -i <service id> --autoscale-enabled true --autoscale-min-replicas <positive number> --autoscale-max-replicas <positive number>
```

例如，将 `autoscale-min-replicas` 设置为 5 将创建 5 个副本。 为了达到最佳 Web 服务数，请将数量设置为值（如 10），然后监视 503 错误消息数。 然后对数量作出相应的调整。


| 参数名称 | Type | 说明 |
|--------------------|--------------------|--------------------|
| `autoscale-enabled` | 布尔值 | 指定是否启用自动缩放。 默认值：true |
| `autoscale-min-replicas` | integer | 指定最小 pod 数。 必须大于或等于 0。 默认值：1 |
| `autoscale-max-replicas` | integer | 指定最大 pod 数。 必须大于或等于 1。 如果自动缩放最大副本数小于自动缩放最小副本数，则将忽略自动缩放最大副本数。 默认值：10 |
| `autoscale-refresh-period-seconds` | integer | 指定自动缩放刷新之间的持续时间（以秒为单位）。 默认值：1 |
| `autoscale-target-utilization` | integer | 指定自动缩放针对的利用率百分比（介于 1 和 100 之间）。 默认值：70 |

使用自动缩放可确保满足以下 2 个条件：

1. 满足目标利用率
2. 缩放绝不超过最小值和最大值设置

群集中的服务会争夺群集资源。 随着每秒请求数 (RPS) 增加，自动缩放服务的群集资源使用率将提高，并且随着 RPS 减少，该服务会缓慢释放资源。 只要群集资源存在且可供服务获取，就可按需获取此类资源。

要深入了解如何使用自动缩放参数，请参阅[模型管理命令行接口参考](model-management-cli-reference.md)文档。

##### <a name="static-scale"></a>静态缩放

一般情况下，应避免使用静态缩放，因为它无法像自动缩放一样减小群集大小。 即便如此，在某些情况下也会建议使用静态缩放。 例如，如果群集专用于单个服务，此时自动缩放就没有任何优势；所有群集资源都应分配给该服务。

要静态缩放群集，则必须关闭自动缩放。 使用以下命令禁用自动缩放：

```
az ml service update realtime -i <service id> --autoscale-enabled false
```

关闭自动缩放后，以下命令会直接对服务的副本数量进行缩放。

```
az ml service update realtime -i <service id> -z <replica count>
```
 
要深入了解如何缩放群集中的节点数量，请参阅在容器服务群集中缩放代理节点。

#### <a name="scaling-number-of-replicas-using-the-kubernetes-dashboard"></a>使用 Kubernetes 仪表板缩放副本数量

在命令行处，输入：

```
kubectl proxy
```

在 Windows 上，Kubernetes 的安装位置不会自动 添加到该路径。 请先导航至安装文件夹：

```
c:\users\<user name>\bin
```

运行命令后，应会看到以下信息性消息：

```
Starting to serve on 127.0.0.1:8001
```

如果端口已被占用，将看到类似于以下示例的消息：

```
F0612 21:49:22.459111   59621 proxy.go:137] listen tcp 127.0.0.1:8001: bind: address already in use
```

可使用 --port 参数指定另一个端口号。

```
kubectl proxy --port=8010
Starting to serve on 127.0.0.1:8010
```

启动仪表板服务器后，请打开浏览器，然后输入以下 URL：

```
127.0.0.1:<port number>/ui
```

在仪表板主屏幕的左侧导航栏上，单击“部署”。 如果导航窗格未显示，请在左上方选择此图标 ![由 3 条短横线组成的菜单](media/how-to-scale-clusters/icon-hamburger.png)。

找到要修改的部署，在右侧单击此图标 ![由 3 个纵向点组成的菜单图标](media/how-to-scale-clusters/icon-kebab.png)，然后单击“查看/编辑 YAML”。

在“编辑部署”屏幕上，找到 spec 节点，修改 replicas 值，然后单击“更新”。
