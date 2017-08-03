---
title: "启用对 Azure DC/OS 容器应用的访问 | Microsoft Docs"
description: "如何启用对 Azure 容器服务中 DC/OS 容器的公共访问。"
services: container-service
documentationcenter: 
author: sauryadas
manager: madhana
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Mesos, Azure"
ms.assetid: 5dea3c4d-a687-4024-93ea-f7a9a7243ab4
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: saudas
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 25eb3b5d379f58022e9e516c51f67c52becf9087
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---
# <a name="enable-public-access-to-an-azure-container-service-application"></a>启用对 Azure 容器服务应用程序的公共访问
ACS [公共代理池](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)中的任何 DC/OS 容器会自动公开到 Internet。 默认情况下，端口 **80**、**443**、**8080** 处于打开状态，且可访问任何在这些端口上侦听的（公用）容器。 本文介绍如何在 Azure 容器服务中打开更多端口供应用程序使用。

## <a name="open-a-port-portal"></a>打开端口（门户）
首先，需要打开所需端口。

1. 登录门户。
2. 查找向其部署 Azure 容器服务的资源组。
3. 选择代理负载均衡器（名称类似于 **XXXX-agent-lb-XXXX**）。
   
    ![Azure 容器服务负载均衡器](./media/container-service-enable-public-access/agent-load-balancer.png)
4. 单击“探测”，并单击“添加”。
   
    ![Azure 容器服务负载均衡器探测](./media/container-service-enable-public-access/add-probe.png)
5. 填写探测窗体，并单击“确定”。
   
   | 字段 | 说明 |
   | --- | --- |
   | Name |探测的描述性名称。 |
   | 端口 |要测试的容器端口。 |
   | 路径 |（处于 HTTP 模式时）探测的相对网站路径。 不支持 HTTPS。 |
   | 时间间隔 |探测尝试之间的时间量（秒）。 |
   | 不正常阈值 |将容器视为运行不正常前，连续探测尝试的次数。 |
6. 返回到代理负载均衡器的属性，单击“负载均衡规则”，并单击“添加”。
   
    ![Azure 容器服务负载均衡器规则](./media/container-service-enable-public-access/add-balancer-rule.png)
7. 填写负载均衡器窗体，并单击“确定”。
   
   | 字段 | 说明 |
   | --- | --- |
   | Name |负载均衡器的描述性名称。 |
   | 端口 |公共传入端口。 |
   | 后端端口 |要将流量路由到其中的容器的内部公共端口。 |
   | 后端池 |此池中的容器将面向此负载均衡器。 |
   | 探测 |用于确定**后端池**中的目标运行是否正常的探测。 |
   | 会话暂留 |确定会话过程中应如何处理来自客户端的流量。<br><br>**无**：来自同一客户端的连续请求可由任何容器处理。<br>**客户端 IP**：来自同一客户端 IP 的连续请求由同一容器处理。<br>**客户端 IP 和协议**：来自同一客户端 IP 和协议组合的连续请求由同一容器处理。 |
   | 空闲超时 |（仅 TCP）在不依赖 *keep-alive* 消息的情况下，让 TCP/HTTP 客户端保持打开状态的时间（分钟）。 |

## <a name="add-a-security-rule-portal"></a>添加安全规则（门户）
接下来，需要添加一个安全规则，将流量从打开的端口路由通过防火墙。

1. 登录门户。
2. 查找向其部署 Azure 容器服务的资源组。
3. 选择**公共**代理网络安全组（名称类似于 **XXXX-agent-public-nsg-XXXX**）。
   
    ![Azure 容器服务网络安全组](./media/container-service-enable-public-access/agent-nsg.png)
4. 选择“入站安全规则”，并选择“添加”。
   
    ![Azure 容器服务网络安全组规则](./media/container-service-enable-public-access/add-firewall-rule.png)
5. 填写防火墙规则以允许公共端口，并单击“确定”。
   
   | 字段 | 说明 |
   | --- | --- |
   | Name |防火墙规则的描述性名称。 |
   | Priority |规则的优先级等级。 编号越低，优先级越高。 |
   | 源 |限制此规则允许或拒绝的传入 IP 地址范围。 使用“任何”，不指定限制。 |
   | 服务 |选择一组此安全规则适用的预定义服务。 否则，可使用“自定义”，创建自己的限制。 |
   | 协议 |基于 **TCP** 或 **UDP** 限制流量。 使用“任何”，不指定限制。 |
   | 端口范围 |“服务”为“自定义”时，指定此规则影响的端口范围。 可以使用单个端口（例如 **80**）或类似 **1024-1500** 的范围。 |
   | 操作 |允许或拒绝符合条件的流量。 |

## <a name="next-steps"></a>后续步骤
了解有关[公共和专用 DC/OS 代理](container-service-dcos-agents.md)之间的差异。

阅读有关[管理 DC/OS 容器](container-service-mesos-marathon-ui.md)的详细信息。


