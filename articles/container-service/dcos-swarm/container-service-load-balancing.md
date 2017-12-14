---
title: "Azure DC/OS 群集中的负载均衡容器"
description: "在 Azure 容器服务 DC/OS 群集中跨多容器进行负载均衡。"
services: container-service
author: rgardler
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 06/02/2017
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 6f5467d0fbcc577a548f1100ed6e4d380fe38759
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2017
---
# <a name="load-balance-containers-in-an-azure-container-service-dcos-cluster"></a>Azure 容器服务 DC/OS 群集中的负载均衡容器

本文将探讨如何使用 Marathon-LB 在 DC/OS 托管 Azure 容器服务中创建内部负载均衡器。 利用此配置可水平扩展应用程序。 还能通过将负载均衡器和应用程序容器分别放在公共群集和专用群集上来利用公共和专用代理群集。 在本教程中：

> [!div class="checklist"]
> * 配置 Marathon 负载均衡器
> * 使用负载均衡器部署应用程序
> * 配置 Azure 负载均衡器

需要 ACS DC/OS 群集来完成本教程中的步骤。 必要时，[此脚本示例](./../kubernetes/scripts/container-service-cli-deploy-dcos.md)可为你创建一个。

本教程需要 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="load-balancing-overview"></a>负载均衡概述

Azure 容器服务 DC/OS 群集中存在两个负载均衡层： 

Azure 负载均衡器提供公用入口点，最终用户将访问这些入口点。 Azure LB 由 Azure 容器服务自动提供，默认配置为公开端口 80、443 和 8080。

Marathon 负载均衡器 (marathon-lb) 将入站请求路由到容纳这些请求的容器实例。 当缩放提供 Web 服务的容器时，marathon-lb 会进行动态调整。 默认情况下，容器服务中不提供此负载均衡器，但它安装简单。

## <a name="configure-marathon-load-balancer"></a>配置 Marathon 负载均衡器

Marathon 负载均衡器基于已部署容器对自身进行动态配置。 还可在丢失容器或代理的情况下进行复原，如果发生此情况，Apache Mesos 将在其他位置重启容器，marathon-lb 随之调整。

运行以下命令，在公用代理群集上安装 marathon 负载均衡器。

```azurecli-interactive
dcos package install marathon-lb
```

## <a name="deploy-load-balanced-application"></a>部署负载均衡器应用程序

有了 marathon-lb 包后，就可部署想使其负载均衡的应用程序容器。 

首先，获取公开代理的 FQDN。

```azurecli-interactive
az acs list --resource-group myResourceGroup --query "[0].agentPoolProfiles[0].fqdn" --output tsv
```

然后，创建命名为“hello-web.json”的文件并将以下内容复制到其中。 `HAPROXY_0_VHOST` 标签需要更新为 DC/OS 代理的 FQDN。 

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

使用 DC/OS CLI 运行应用程序。 默认情况下，Marathon 会将应用程序部署到专用群集。 这意味着，上面的部署仅能通过负载均衡器进行访问，这也是一种所需的行为。

```azurecli-interactive
dcos marathon app add hello-web.json
```

部署应用程序后，可浏览到代理群集的 FQDN 查看负载均衡器应用程序。

![负载均衡器应用程序的映像](./media/container-service-load-balancing/lb-app.png)

## <a name="configure-azure-load-balancer"></a>配置 Azure 负载均衡器

默认情况下，Azure 负载均衡器会公开端口 80、8080 和 443。 如果使用的端口是这三个中的一个（如上述示例中所做），则无需再进行任何操作。 应可命中代理负载均衡器的 FQDN；每次刷新时，都会以轮循方式命中三个 Web 服务器中的一个。 

如果使用其他端口，则需为所用端口向负载均衡器添加轮循规则和探测器。 可从 [Azure CLI](../../azure-resource-manager/xplat-cli-azure-resource-manager.md) 使用 `azure network lb rule create` 和 `azure network lb probe create` 命令执行此操作。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用 Marathon 和 Azure 负载均衡器在 ACS 中进行负载均衡，包括以下操作：

> [!div class="checklist"]
> * 配置 Marathon 负载均衡器
> * 使用负载均衡器部署应用程序
> * 配置 Azure 负载均衡器

请转到下一教程，了解如何将 Azure 存储与 Azure 中的 DC/OS 集成。

> [!div class="nextstepaction"]
> [在 DC/OS 群集中装载 Azure 文件共享](container-service-dcos-fileshare.md)