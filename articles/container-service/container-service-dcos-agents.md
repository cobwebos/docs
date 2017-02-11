---
title: "公共和专用 DC/OS 代理池 ACS |Microsoft Docs"
description: "公共和专用代理池如何与 Azure 容器服务群集一起使用。"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, 容器, 微服务, Mesos, Azure"
ms.assetid: 36d657c9-8845-4bf7-bed2-088323b67406
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a6d9ab6d95de936e4f1d28d47d4e1d74c080bdae


---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Azure 容器服务的 DC/OS 代理池
DC/OS Azure 容器服务将代理划分为公共池或专用池。 可对每个池进行部署，从而影响容器服务中计算机之间的可访问性。 计算机可以向 Internet 公开（公共）或保持在内部（专用）。 本文提供存在公共池和专用池的原因概述。

### <a name="private-agents"></a>专用代理
专用代理节点通过不可路由网络运行。 只有从管理区域或通过公共区域边缘路由器才可访问此网络。 默认情况下，DC/OS 在专用代理节点启动应用。 有关网络安全性的详细信息，请参阅 [DC/OS 文档](https://dcos.io/docs/1.7/administration/securing-your-cluster/)。

### <a name="public-agents"></a>公共代理
公共代理节点通过可公共访问的网络运行 DC/OS 应用和服务。 有关网络安全性的详细信息，请参阅 [DC/OS 文档](https://dcos.io/docs/1.7/administration/securing-your-cluster/)。

## <a name="using-agent-pools"></a>使用代理池
默认情况下，**Marathon** 将所有新的应用程序部署到“专用”代理节点。 必须在应用程序创建过程中，将应用程序显式部署到“公共”节点。 选择“可选”选项卡，然后输入 **slave_public**作为“已接受的资源角色”值。 此过程记录在[此处](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)和 [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) 文档中。

## <a name="next-steps"></a>后续步骤
阅读有关[管理 DC/OS 容器](container-service-mesos-marathon-ui.md)的详细信息。

了解如何[打开由 Azure 提供的防火墙](container-service-enable-public-access.md)，允许对 DC/OS 容器进行公共访问。




<!--HONumber=Nov16_HO3-->


