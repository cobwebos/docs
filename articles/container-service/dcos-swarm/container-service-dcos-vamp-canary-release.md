---
title: （已弃用）在 Azure DC/OS 群集上使用 Vamp 进行 Canary 发布
description: 如何在 Azure 容器服务 DC/OS 群集上使用 Vamp 通过 Canary 发布服务以及应用智能流量筛选
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: dfdf6e1f8edfb4dafaf93e62090ed51878f9b2aa
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734818"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>（已弃用）在 Azure 容器服务 DC/OS 群集上使用 Vamp 通过 Canary 发布微服务

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

本演练在包含 DC/OS 群集的 Azure 容器服务上设置 Vamp。 我们将通过 Canary 发布 Vamp 演示服务“sava”，并通过应用智能流量筛选解决该服务与 Firefox 不兼容的问题。 

> [!TIP] 
> 在本演练中，Vamp 在 DC/OS 群集上运行，但也可以将 Vamp 作为协调器与 Kubernetes 一起使用。
>

## <a name="about-canary-releases-and-vamp"></a>关于 Canary 发布和 Vamp


[Canary 发布](https://martinfowler.com/bliki/CanaryRelease.html)是 Netflix、Facebook 和 Spotify 等创新组织采用的一种智能部署策略。 它是一种非常有效的方案，因为它可以减少问题，引入安全网络，并提高创新能力。 为什么它没有被所有公司使用？ 将 CI/CD 管道扩展为包含 Canary 策略会增大复杂性，并且需要丰富的 devops（开发运营）知识和经验。 在小型公司和企业尚未涉足之前，这些因素就足以让它们望而却步。 

[Vamp](https://vamp.io/) 是一个开源系统，旨在简化这种过渡，在首选的容器计划程序中添加 Canary 发布功能。 Vamp 的 Canary 功能并不局限于基于百分比的实施。 可以根据各种条件筛选和拆分流量，例如，定位特定的用户、IP 范围或设备。 Vamp 可以跟踪和分析性能指标，基于实际数据实现自动化。 可以设置为出错时自动回滚，或者根据负载或延迟缩放单个服务变体。

## <a name="set-up-azure-container-service-with-dcos"></a>设置 Azure 容器服务和 DC/OS



1. 使用一个默认大小的主节点和两个默认大小的代理[部署 DC/OS 群集](container-service-deployment.md)。 

2. [创建 SSH 隧道](../container-service-connect.md)以连接到 DC/OS 群集。 本文假设在本地端口 80 上通过隧道连接到群集。


## <a name="set-up-vamp"></a>安装 Vamp

现在，你已有一个正在运行的 DC/OS 群集，可以通过 DC/OS UI （http：\//localhost：80）安装 Vamp。 

![DC/OS UI](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

安装过程分为两个阶段：

1. **部署 Elasticsearch**。

2. 然后通过安装 Vamp DC/OS 通用包部署 Vamp****。

### <a name="deploy-elasticsearch"></a>部署 Elasticsearch

Vamp 需要使用 Elasticsearch 来收集和聚合指标。 可以使用 [magneticio Docker 映像](https://hub.docker.com/r/magneticio/elastic/)部署兼容的 Vamp Elasticsearch 堆栈。

1. 在 DC/OS UI 中转到“服务”，并单击“部署服务”。********

2. 在“部署新服务”弹出对话框中选择“JSON 模式”。********

   ![选择 JSON 模式](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. 粘贴以下 JSON。 此配置在 Elasticsearch 端口上运行具有 1 GB RAM 的容器和基本运行状况检查。
  
   ```JSON
   {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
   }
   ```
  

3. 单击“部署”****。

   DC/OS 将部署 Elasticsearch 容器。 可以在“服务”页上跟踪进度。****  

   ![部署 e?Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>部署 Vamp

Elasticsearch 报告“正在运行”后，可以添加 Vamp DC/OS 通用包。**** 

1. 转到“通用”，并搜索 **vamp**。**** 
   ![DC/OS 通用包中的 Vamp](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. 单击 vamp 包旁边的“安装”，并选择“高级安装”。********

3. 向下滚动并输入以下 elasticsearch-url：`http://elasticsearch.marathon.mesos:9200`。 

   ![输入 Elasticsearch URL](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. 单击“检查并安装”，然后单击“安装”开始部署********。  

   DC/OS 将部署全部所需的 Vamp 组件。 可以在“服务”页上跟踪进度。****
  
   ![将 Vamp 部署为通用包](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. 完成部署后，可以访问 Vamp UI：

   ![DC/OS 中的 Vamp 服务](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Vamp UI](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>部署第一个服务

Vamp 启动并运行后，请从蓝图部署一个服务。 

最简单形式的 [Vamp 蓝图](https://docs.vamp.io/how-vamp-works/vamp-and-kubernetes#vamp-deployments)描述了要部署的终结点（网关）、群集和服务。 Vamp 使用群集将相同服务的不同变体分组成逻辑组，以用于 Canary 发布或 A/B 测试。  

此方案使用名为 [**sava**](https://github.com/magneticio/sava-product)、版本为 1.0 的示例整体应用程序。 该整体应用程序打包在一个 Docker 容器中，该容器位于 Docker 中心的 magneticio/sava:1.0.0 下面。 正常情况下，该应用在端口 8080 上运行，但在本例中，你希望在端口 9050 下将它公开。 使用简单的蓝图通过 Vamp 部署该应用。

1. 转到“部署”。****

2. 单击“添加”  。

3. 粘贴以下蓝图 YAML。 此蓝图包含一个群集，该群集只包含一个要在后续步骤中更改的服务变体：

   ```YAML
   name: sava                        # deployment name
   gateways:
    9050: sava_cluster/webport      # stable endpoint
   clusters:
    sava_cluster:               # cluster to create
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
   ```

4. 单击“保存”  。 Vamp 将启动部署。

该部署将列在“部署”页上。**** 单击该部署监视其状态。

![Vamp UI - 部署 sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![Vamp UI 中的 sava 服务](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

将创建两个网关，它们将列在“网关”页上：****

* 一个访问正在运行的服务的稳定终结点（端口 9050） 
* 一个由 Vamp 管理的内部网关（稍后将详细介绍此网关）。 

![Vamp UI - sava 网关](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

现已部署 sava 服务，但无法在外部访问它，因为 Azure 负载均衡器尚不知道要向其转发流量。 若要访问该服务，请更新 Azure 网络配置。


## <a name="update-the-azure-network-configuration"></a>更新 Azure 网络配置

Vamp 在 DC/OS 代理节点上部署了 sava 服务，从而在端口 9050 上公开了一个稳定终结点。 若要从 DC/OS 群集外部访问该服务，请对群集部署中的 Azure 网络配置进行以下更改： 

1. 使用运行状况探测和用于在端口 9050 上向 sava 实例转发流量的规则，为代理**配置 Azure 负载均衡器**（名为**dcos-agent-lb-xxxx** 的资源）。 

2. 为公共代理**更新网络安全组**（名为 **XXXX-agent-public-nsg-XXXX** 的资源），允许在端口 9050 上传送流量。

有关使用 Azure 门户完成这些任务的详细步骤，请参阅[启用对 Azure 容器服务应用程序的公共访问](container-service-enable-public-access.md)。 为所有端口设置指定端口 9050。


创建所有组件后，请转到 DC/OS 代理负载均衡器（名为 **dcos-agent-lb-xxxx** 的资源）的“概述”边栏选项卡。**** 找到“公共 IP 地址”，并使用该地址访问端口 9050 上的 sava。****

![Azure 门户 - 获取公共 IP 地址](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>运行 Canary 发布

假设要通过 Canary 将此应用程序的新版本发布到生产环境。 现已将它容器化为 magneticio/sava:1.1.0 并已做好发布准备。 使用 Vamp 可以轻松将新服务添加到正在运行的部署。 这些“合并的”服务将连同群集中的现有服务一起部署，并分配有权重 0%。 在调整流量分配之前，不会将任何流量路由到新合并的服务。 使用 Vamp UI 中的权重滑块可以全面控制分配，进行增量调整（Canary 发布）或即时回滚。

### <a name="merge-a-new-service-variant"></a>合并新的服务变体

要将新 sava 1.1 服务与正在运行的部署合并，请执行以下操作：

1. 在 Vamp UI 中单击“蓝图”。****

2. 单击“添加”并粘贴以下蓝图 YAML：此蓝图描述要在现有群集 (sava_cluster) 中部署的新服务变体 (sava:1.1.0)。****

   ```YAML
   name: sava:1.1.0      # blueprint name
   clusters:
    sava_cluster:       # cluster to update
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
   ```
  
3. 单击“保存”  。 该蓝图会存储，并列在“蓝图”页上。****

4. 在 sava:1.1 蓝图中打开操作菜单，并单击“合并到”。****

   ![Vamp UI - 蓝图](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. 选择“sava”部署，并单击“合并”。********

   ![Vamp UI - 将蓝图合并到部署](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp 会在正在运行的部署的 **sava_cluster** 中，连同 sava:1.0.0 一起部署蓝图中描述的新 sava:1.1.0 服务变体。 

![Vamp UI - 更新的 sava 部署](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

**sava/sava_cluster/webport** 网关（群集终结点）也会更新，其中会添加通往新部署的 sava:1.1.0 的路由。 目前，不会将任何流量路由到此处（“权重”设置为 0%）。****

![Vamp UI - 群集网关](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Canary 发布

在同一个群集中部署两个版本的 sava 后，请移动“权重”滑块调整两个版本之间的流量分配。****

1. 单击“权重”旁边的“Vamp UI - 编辑”。![](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png)****

2. 将权重分配设置为 50%/50%，然后单击“保存”。****

   ![Vamp UI - 网关权重滑块](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. 返回到浏览器并刷新 sava 页面几次。 现在，sava 应用程序会在 sava:1.0 页与 sava:1.1 页之间切换。

   ![交替 sava1.0 和 sava1.1 服务](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > 这种页面交替最适合用于浏览器的“隐身”或“匿名”模式，因为静态资产将被缓存。
  >

### <a name="filter-traffic"></a>筛选流量

假设在部署后发现 sava:1.1.0 中的不兼容性导致 Firefox 浏览器出现显示问题。 可将 Vamp 设置为筛选传入流量，并将所有 Firefox 用户定向回到已知稳定的 sava:1.0.0。 此筛选器可立即解决 Firefox 用户的中断，而其他任何人则可继续享用改进的 sava:1.1.0 带来的好处。

Vamp 使用**条件**来筛选网关中路由之间的流量。 流量首先根据应用到每个路由的条件进行筛选和定向。 所有剩余的流量根据网关权重设置进行分配。

可以创建一个条件用于筛选所有 Firefox 用户，并将其定向到旧的 sava:1.0.0：

1. 在 sava/sava_cluster/webport“网关”页上，单击“Vamp UI - 编辑”将一个**条件**添加到路由 sava/sava_cluster/sava:1.0.0/webport。****![](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) 

2. 输入条件 **user-agent == Firefox**，并单击“Vamp UI - 保存”。![](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png)

   Vamp 将添加默认强度为 0% 的条件。 若要开始筛选流量，需要调整条件强度。

3. 单击“Vamp UI - 编辑”更改应用到条件的**强度**。![](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png)
 
4. 将“强度”设置为 100%，然后单击“Vamp UI - 保存”以保存该设置。****![](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png)

   现在，Vamp 会将与该条件的所有流量（所有 Firefox 用户）发送到 sava:1.0.0。

   ![Vamp UI - 将条件应用到网关](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. 最后，调整网关权重，以便将所有剩余流量（所有非 Firefox 用户）发送到新的 sava:1.1.0。 单击“权重”旁边的“Vamp UI - 编辑”并设置权重分配，以便将 100% 的流量定向到路由 sava/sava_cluster/sava:1.1.0/webport。![](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png)****

   现在，未根据条件筛选的所有流量将定向到新的 sava:1.1.0。

6. 要查看筛选器的工作方式，请打开两个不同的浏览器（一个 Firefox 和一个其他浏览器），并从这两个浏览器访问 sava 服务。 所有 Firefox 请求将发送到 sava:1.0.0，而其他所有浏览器将定向到 sava:1.1.0。

   ![Vamp UI - 筛选流量](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>总结

本文是有关在 DC/OS 群集上使用 Vamp 的快速简介。 其中介绍了初学者如何在 Azure 容器服务 DC/OS 群集上启动和运行 Vamp，使用 Vamp 蓝图部署服务，以及在公开的终结点（网关）上访问该服务。

此外，还大致讲解了 Vamp 的一些强大功能：将新的服务变体合并到正在运行的部署并以增量方式引入该变体，然后筛选流量以解决已知的不兼容性。


## <a name="next-steps"></a>后续步骤

* 了解如何通过 [Vamp REST API](https://docs.vamp.io/how-vamp-works/events-and-metrics#events) 管理 Vamp 操作。

* 在 Node.js 中生成 Vamp 自动化脚本并以 [Vamp 工作流](https://docs.vamp.io/how-vamp-works/concepts-and-components#workflows)的形式运行这些脚本。

* 参阅其他 [VAMP 教程](https://docs.vamp.io/tutorials/)。

