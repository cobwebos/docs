---
title: "对 Azure 中的 Kubernetes 容器进行负载均衡 | Microsoft 文档"
description: "外部连接 Azure 容器服务中 Kubernetes 群集内的多个容器并对其进行负载均衡。"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "容器, 微服务, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: danlep
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: e2ac0d65eea9cea25a2a9cb1f99a35c689eed976
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---
# <a name="load-balance-containers-in-a-kubernetes-cluster-in-azure-container-service"></a>对 Azure 容器服务中 Kubernetes 内的容器进行负载均衡 
本文介绍 Azure 容器服务中 Kubernetes 群集内的负载均衡。 负载均衡为服务提供可从外部访问的 IP 地址，可在代理 VM 中运行的 pod 之间分配网络流量。

可将 Kubernetes 服务设置为使用 [Azure 负载均衡器](../../load-balancer/load-balancer-overview.md)来管理外部网络 (TCP) 流量。 经过更多的配置后，便可以针对 HTTP 或 HTTPS 流量进行负载均衡和路由，或实现更高级的方案。

## <a name="prerequisites"></a>先决条件
* 在 Azure 容器服务中[部署 Kubernetes 群集](container-service-kubernetes-walkthrough.md)
* [将客户端连接](../container-service-connect.md)到群集

## <a name="azure-load-balancer"></a>Azure 负载均衡器

默认情况下，在 Azure 容器服务中部署的 Kubernetes 群集包含一个用于代理 VM 的面向 Internet 的 Azure 负载均衡器。 （将为主 VM 单独配置负载均衡器资源。）Azure 负载均衡器是第 4 层负载均衡器。 目前，负载均衡器仅支持 Kubernetes 中的 TCP 流量。

创建 Kubernetes 服务时，可将 Azure 负载均衡器自动配置为允许访问服务。 要配置负载均衡器，请将服务 `type` 设置为 `LoadBalancer`。 负载均衡器会创建一个规则，用于将传入服务流量的公共 IP 地址和端口号映射到代理 VM 中 pod 的专用 IP 地址和端口号（对于响应流量，则进行反向映射）。 

 下面两个示例演示如何将 Kubernetes 服务`type` 设置为 `LoadBalancer`。 （尝试运行示例后，如果不再需要部署，请将其删除。）

### <a name="example-use-the-kubectl-expose-command"></a>示例：使用 `kubectl expose` 命令 
[Kubernetes 演练](container-service-kubernetes-walkthrough.md)包含有关如何使用 `kubectl expose` 命令及其 `--type=LoadBalancer` 标志公开服务的示例。 步骤如下：

1. 启动新的容器部署。 例如，以下命令启动名为 `mynginx` 的新部署。 该部署由三个容器组成，这些容器基于 Nginx Web 服务器的 Docker 映像。

    ```console
    kubectl run mynginx --replicas=3 --image nginx
    ```
2. 验证容器是否正在运行。 例如，如果使用 `kubectl get pods` 查询容器，将看到如下所示的输出：

    ![获取 Nginx 容器](./media/container-service-kubernetes-load-balancing/nginx-get-pods.png)

3. 要将负载均衡器配置为接受发往部署的外部流量，请结合 `--type=LoadBalancer` 运行 `kubectl expose`。 以下命令公开端口 80 上的 Nginx 服务器：

    ```console
    kubectl expose deployments mynginx --port=80 --type=LoadBalancer
    ```

4. 键入 `kubectl get svc` 查看群集中服务的状态。 当负载均衡器配置规则时，服务的 `EXTERNAL-IP` 显示为 `<pending>`。 几分钟后，将配置外部 IP 地址： 

    ![配置 Azure 负载均衡器](./media/container-service-kubernetes-load-balancing/nginx-external-ip.png)

5. 验证是否可以通过外部 IP 地址访问该服务。 例如，打开 Web 浏览器并访问所示的 IP 地址。 浏览器显示 Nginx Web 服务器正在某个容器中运行。 或者，运行 `curl` 或 `wget` 命令。 例如：

    ```
    curl 13.82.93.130
    ```

    应看到如下输出：

    ![使用 curl 访问 Nginx](./media/container-service-kubernetes-load-balancing/curl-output.png)

6. 若要查看 Azure 负载均衡器的配置，请转到 [Azure 门户](https://portal.azure.com)。

7. 浏览容器服务群集的资源组，选择代理 VM 的负载均衡器。 该负载均衡器的名称应为容器服务相同。 （请不要选择主节点的负载均衡器，即名称中包含 **master-lb** 的负载均衡器。） 

    ![资源组中的负载均衡器](./media/container-service-kubernetes-load-balancing/container-resource-group-portal.png)

8. 若要查看负载均衡器配置的详细信息，请单击“负载均衡规则”以及已配置的规则的名称。

    ![负载均衡器规则](./media/container-service-kubernetes-load-balancing/load-balancing-rules.png) 

### <a name="example-specify-type-loadbalancer-in-the-service-configuration-file"></a>示例：在服务配置文件中指定 `type: LoadBalancer`

要从 YAML 或 JSON [服务配置文件](https://kubernetes.io/docs/user-guide/services/operations/#service-configuration-file)部署 Kubernetes 容器应用，请通过将以下行添加到服务规范来指定外部负载均衡器：

```YAML
 "type": "LoadBalancer"
``` 



以下步骤使用 Kubernetes [留言簿示例](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook)。 此示例是基于 Redis 和 PHP Docker 映像的多层 Web 应用。 可在服务配置文件中指定前端 PHP 服务器应使用 Azure 负载均衡器。

1. 从 [GitHub](https://github.com/kubernetes/kubernetes/tree/master/examples/guestbook/all-in-one) 下载文件 `guestbook-all-in-one.yaml`。 
2. 浏览 `frontend` 服务的 `spec`。
3. 取消注释 `type: LoadBalancer` 行。

    ![服务配置中的负载均衡器](./media/container-service-kubernetes-load-balancing/guestbook-frontend-loadbalance.png)

4. 保存文件，并运行以下命令部署应用。

    ```
    kubectl create -f guestbook-all-in-one.yaml
    ```

5. 键入 `kubectl get svc` 查看群集中服务的状态。 当负载均衡器配置规则时，`frontend` 服务的 `EXTERNAL-IP` 显示为 `<pending>`。 几分钟后，将配置外部 IP 地址： 

    ![配置 Azure 负载均衡器](./media/container-service-kubernetes-load-balancing/guestbook-external-ip.png)

6. 验证是否可以通过外部 IP 地址访问该服务。 例如，可以打开 Web 浏览器访问服务的外部 IP 地址。

    ![从外部访问留言簿](./media/container-service-kubernetes-load-balancing/guestbook-web.png)

    可以添加留言簿条目。

7. 若要查看 Azure Load Balancer 的配置，请在 [Azure 门户](https://portal.azure.com)中浏览群集的负载均衡器资源。 参阅前一示例中的步骤。

### <a name="considerations"></a>注意事项

* 负载均衡器规则是以异步方式创建的，有关预配的均衡器的信息发布在服务的 `status.loadBalancer` 字段中。
* 系统会自动在负载均衡器中为每个服务分配其自身的虚拟 IP 地址。
* 如果想要按 DNS 名称访问负载均衡器，请配合域服务提供商为规则的 IP 地址创建 DNS 名称。

## <a name="http-or-https-traffic"></a>HTTP 或 HTTPS 流量

若要对发往容器 Web 应用的 HTTP 或 HTTPS 流量进行负载均衡并管理传输层安全性 (TLS) 的证书，可以使用 Kubernetes [入口](https://kubernetes.io/docs/user-guide/ingress/)资源。 入口是允许与群集服务建立入站连接的规则集合。 要使入口资源正常工作，Kubernetes 群集上必须运行[入口控制器](https://kubernetes.io/docs/user-guide/ingress/#ingress-controllers)。

Azure 容器服务不会自动实现 Kubernetes 入口控制器。 有多个控制器实现可用。 目前，建议使用 [Nginx 入口控制器](https://github.com/kubernetes/ingress/tree/master/examples/deployment/nginx)来配置入站规则以及对 HTTP 和 HTTPS 流量进行负载均衡。 

有关详细信息，请参阅 [Nginx 入口控制器文档](https://github.com/kubernetes/ingress/tree/master/controllers/nginx/README.md)。

> [!IMPORTANT]
> 在 Azure 容器服务中使用 Nginx 入口控制器时，必须使用 `type: LoadBalancer` 将控制器部署公开为服务。 这会将 Azure 负载均衡器配置为向控制器路由流量。 有关详细信息，请参阅上一部分。


## <a name="next-steps"></a>后续步骤

* 参阅 [Kubernetes LoadBalancer 文档](https://kubernetes.io/docs/user-guide/load-balancer/)
* 详细了解 [Kubernetes 入口和入口控制器](https://kubernetes.io/docs/user-guide/ingress/)
* 参阅 [Kubernetes 示例](https://github.com/kubernetes/kubernetes/tree/master/examples)


