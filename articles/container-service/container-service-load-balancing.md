---
title: "Azure 容器服务群集中的负载平衡容器 | Microsoft Docs"
description: "在 Azure 容器服务群集中跨多容器进行负载平衡。"
services: container-service
documentationcenter: 
author: rgardler
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "容器, 微服务, DC/OS, Azure"
ms.assetid: f0ab5645-2636-42de-b23b-4c3a7e3aa8bb
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2016
ms.author: rogardle
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cf255856302ad5bdb1f6022d231833610acbcac5


---
# <a name="load-balance-containers-in-an-azure-container-service-cluster"></a>Azure 容器服务群集中的负载平衡容器
在本文中，将探讨如何使用 Marathon-LB 在 DC/OS 托管 Azure 容器服务中创建内部负载均衡器。 这使你能够水平扩展应用程序。 还能通过将负载均衡器和应用程序容器分别放在公用群集和专用群集上来利用公用和专用代理群集。

## <a name="prerequisites"></a>先决条件
使用 orchestrator 类型 DC/OS [部署 Azure 容器服务实例](container-service-deployment.md)，并[确保客户端可以连接到群集](container-service-connect.md)。 

## <a name="load-balancing"></a>负载平衡
将构建的容器服务群集中存在两个负载平衡层： 

1. Azure Load Balancer 提供了公用入口点，最终用户将命中这些入口点。 它们由 Azure 容器服务自动提供，被默认配置为公开端口 80、443 和 8080。
2. Marathon 负载均衡器 (marathon-lb) 将入站请求路由到容纳这些请求的容器实例。 当缩放提供 Web 服务的容器时，marathon-lb 会进行动态调整。 默认情况下，容器服务中不提供此负载均衡器，但它非常容易安装。

## <a name="marathon-load-balancer"></a>Marathon 负载均衡器
Marathon 负载均衡器基于已部署容器对自身进行动态配置。 还可在丢失容器或代理情况下进行复原，如果发生此情况，Apache Mesos 将在其他位置重启容器，marathon-lb 随之调整。

若要安装 Marathon 负载均衡器，可使用 DC/OS Web UI 或命令行。

### <a name="install-marathonlb-using-dcos-web-ui"></a>使用 DC/OS Web UI 安装 Marathon-LB
1. 单击“通用”
2. 搜索“Marathon-LB”
3. 单击“安装”

![通过 DC/OS Web 界面安装 marathon-lb](./media/dcos/marathon-lb-install.png)

### <a name="install-marathonlb-using-the-dcos-cli"></a>使用 DC/OS CLI 安装 Marathon-LB
安装 DC/OS CLI 并确保可连接到群集后，从客户端计算机上运行以下命令：

```bash
dcos package install marathon-lb
```

此命令将自动在公用代理群集上安装负载均衡器。

## <a name="deploy-a-load-balanced-web-application"></a>部署负载平衡的 Web 应用程序
有了 marathon-lb 包后，就可部署想使其负载平衡的应用程序容器。 此示例中，将通过使用以下配置部署简单 Web 服务器：

```json
{
  "id": "web",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "yeasy/simple-web",
      "network": "BRIDGE",
      "portMappings": [
        { "hostPort": 0, "containerPort": 80, "servicePort": 10000 }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "labels":{
    "HAPROXY_GROUP":"external",
    "HAPROXY_0_VHOST":"YOUR FQDN",
    "HAPROXY_0_MODE":"http"
  }
}

```

* 将 `HAProxy_0_VHOST` 的值设置为代理的负载均衡器的 FQDN。 其形式为 `<acsName>agents.<region>.cloudapp.azure.com`。 例如，如果在区域 `West US` 中创建名为 `myacs` 的容器服务群集，则 FQDN 将为 `myacsagents.westus.cloudapp.azure.com`。 另一种方法是在 [Azure 门户](https://portal.azure.com)中浏览为容器服务创建的资源组中资源时，查找名称中带有“agent”的负载均衡器。
* 将 servicePort 设置为 >= 10,000 的端口。 这标识该服务正在此容器中运行，marathon-lb 使用它来标识应使其负载平衡的服务。
* 将 `HAPROXY_GROUP` 标签设置为“外部”。
* 将 `hostPort` 设置为 0。 这意味着 Marathon 会任意分配可用端口。
* 将 `instances` 设置为想要创建的实例数。 可在之后增加或减少这一数量。

要知道默认情况下 Marathon 将部署到专用群集，这意味着以上部署仅能通过负载均衡器进行访问，这通常是希望看到的行为。

### <a name="deploy-using-the-dcos-web-ui"></a>使用 DC/OS Web UI 进行部署
1. 设置 [SSH 隧道](container-service-connect.md)后在 http://localhost/marathon 访问 Marathon 页面，并单击 `Create Appliction`
2. 在 `New Application` 对话框中，单击右上角的 `JSON Mode`
3. 将上述 JSON 粘贴到编辑器
4. 单击 `Create Appliction`

### <a name="deploy-using-the-dcos-cli"></a>使用 DC/OS CLI 进行部署
若要使用 DC/OS CLI 部署此应用程序，只需将上述 JSON 复制到名为 `hello-web.json`的文件中并运行：

```bash
dcos marathon app add hello-web.json
```

## <a name="azure-load-balancer"></a>Azure 负载平衡器
默认情况下，Azure Load Balancer 会公开端口 80、8080 和 443。 如果使用的端口是这三个中的一个（如上述示例中所做），则无需再进行任何操作。 可命中代理负载均衡器的 FQDN；每次刷新时，都会以轮询方式命中三个 Web 服务器中的一个。 但如果使用其他端口，则需为所用端口向负载均衡器添加轮循规则和探测器。 可从 [Azure CLI](../xplat-cli-azure-resource-manager.md) 使用 `azure network lb rule create` 和 `azure network lb probe create` 命令执行此操作。 还可以使用 Azure 门户来实现此目的。

## <a name="additional-scenarios"></a>其他方案
你可以使用不同域来公开不同服务。 例如：

mydomain1.com -> Azure LB:80 -> marathon-lb:10001 -> mycontainer1:33292  
mydomain2.com -> Azure LB:80 -> marathon-lb:10002 -> mycontainer2:22321

若要实现此目标，请签出 [虚拟主机](https://mesosphere.com/blog/2015/12/04/dcos-marathon-lb/)，它提供将域关联到特定 marathon-lb 路径的方法。

或者公开不同的端口，并将它们重映射到 marathon-lb 后正确的服务。例如：

Azure lb:80 -> marathon-lb:10001 -> mycontainer:233423  
Azure lb:8080 -> marathon-lb:1002 -> mycontainer2:33432

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 [marathon-lb](https://dcos.io/docs/1.7/usage/service-discovery/marathon-lb/)上的 DC/OS 文档。




<!--HONumber=Nov16_HO2-->


