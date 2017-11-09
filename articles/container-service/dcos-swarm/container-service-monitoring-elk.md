---
title: "监视 Azure DC/OS 群集 - ELK 堆栈 | Microsoft 文档"
description: "使用 ELK（Elasticsearch、Logstash 和 Kibana）监视 Azure 容器服务群集中的 DC/OS 群集。"
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: "容器, DC/OS, Azure, 监视, elk"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: fcfa277cdd0f3cebc0fbbb23e771fb23ffbe2ca6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-cluster-with-elk"></a>使用 ELK 监视 Azure 容器服务群集
在本文中，我们将演示如何在 Azure 容器服务中的 DC/OS 群集上部署 ELK（Elasticsearch、Logstash、Kibana）堆栈。 

## <a name="prerequisites"></a>先决条件
[部署](container-service-deployment.md)和[连接](../container-service-connect.md)由 Azure 容器服务配置的 DC/OS 群集。 在[此处](container-service-mesos-marathon-ui.md)浏览 DC/OS 仪表板和 Marathon 服务。 此外，安装 [Marathon 负载均衡器](container-service-load-balancing.md)。


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK（Elasticsearch、Logstash、Kibana）
ELK 堆栈是 Elasticsearch、Logstash 和 Kibana 的组合，提供可用于监视和分析群集中的日志的端到端堆栈。

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>在 DC/OS 群集上配置 ELK 堆栈
通过 [http://localhost:80/](http://localhost:80/) 访问 DC/OS UI。在 DC/OS UI 中后，导航到 **Universe**。 从 DC/OS Universe 以该特定顺序搜索并安装 Elasticsearch、Logstash 和 Kibana。 如果转到“高级安装”链接，可以了解有关配置的详细信息。

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

在 ELK 容器启动并正在运行后，需要使 Kibana 能够通过 Marathon-LB 进行访问。 导航到“服务” > “kibana”，并单击“编辑”，如下所示。

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


切换到“JSON 模式”，并向下滚动到标签部分。
需要在此处添加 `"HAPROXY_GROUP": "external"` 条目，如下所示。
单击“部署更改”后，容器将重新启动。

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


如果要验证 Kibana 是否已注册为 HAPROXY 仪表板中的服务，需要在 HAPROXY 在端口 9090 上运行时打开代理群集上的端口 9090。
默认情况下，我们在 DC/OS 代理群集中打开端口 80、8080 和 443。
有关打开端口和提供公共评估的说明在[此处](container-service-enable-public-access.md)提供。

若要访问 HAPROXY 仪表板，请在 `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats` 打开 Marathon-LB 管理界面。
导航到该 URL 后，应看到 HAPROXY 仪表板（如下所示），并应看到 Kibana 服务条目。

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


若要访问 Kibana 仪表板（在端口 5601 上部署），需要打开端口 5601。 请按照[此处](container-service-enable-public-access.md)的说明进行操作。 然后在 `http://localhost:5601` 打开 Kibana 仪表板。

## <a name="next-steps"></a>后续步骤

* 有关系统和应用程序日志转发和设置，请参阅[使用 ELK 在 DC/OS 中管理日志](https://docs.mesosphere.com/1.8/administration/logging/elk/)。

* 若要筛选日志，请参阅[使用 ELK 筛选日志](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/)。 

 

