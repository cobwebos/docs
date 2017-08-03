---
title: "监视 Azure DC/OS 群集 - Datadog | Microsoft Docs"
description: "通过 Datadog 监视 Azure 容器服务群集。 使用 DC/OS Web UI 将 Datadog 代理部署到群集。"
services: container-service
documentationcenter: 
author: sauryadas
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "容器, DC/OS, Docker Swarm, Azure"
ms.assetid: ed00635d-4569-4f87-ab63-e307b2690b42
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 07/28/2016
ms.author: saudas
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 42e1743b67a46c9eac6cce44852ea3a48fd38e5a
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-datadog"></a>通过 Datadog 监视 Azure 容器服务 DC/OS 群集
在本文中，我们将 Datadog 代理部署到 Azure 容器服务群集中的所有代理节点。 此配置需要具有 Datadog 帐户。 

## <a name="prerequisites"></a>先决条件
[部署](container-service-deployment.md)和[连接](../container-service-connect.md)由 Azure 容器服务配置的群集。 探究 [Marathon UI](container-service-mesos-marathon-ui.md)。 转到 [http://datadoghq.com](http://datadoghq.com)，设置 Datadog 帐户。 

## <a name="datadog"></a>Datadog
Datadog 是一种从 Azure 容器服务群集中的容器收集监视数据的监视服务。 Datadog 具有 Docker 集成仪表板，可通过它查看容器中的特定指标。 从容器中收集的指标由 CPU、内存、网络和 I/O 组织。 Datadog 将指标拆分为容器和映像。 以下是 CPU 使用情况的 UI 外观示例。

![Datadog UI](./media/container-service-monitoring/datadog4.png)

## <a name="configure-a-datadog-deployment-with-marathon"></a>通过 Marathon 配置 Datadog 部署
这些步骤将演示如何通过 Marathon 将 Datadog 应用程序配置和部署到群集中。 

通过 [http://localhost:80/](http://localhost:80/) 访问 DC/OS UI。 进入 DC/OS UI 中后，导航到左下角的“Universe”，并搜索“Datadog”，单击“安装”。

![DC/OS Universe 中的 Datadog 包](./media/container-service-monitoring/datadog1.png)

现在，要完成该配置，需要一个 Datadog 帐户或免费试用版帐户。 登录 Datadog 网站后，请查看左侧，并转到“集成”->[“API”](https://app.datadoghq.com/account/settings#api)。 

![Datadog API 密钥](./media/container-service-monitoring/datadog2.png)

接下来将 API 密钥输入到 DC/OS Universe 内的 Datadog 配置中。 

![DC/OS Universe 中的 Datadog 配置](./media/container-service-monitoring/datadog3.png) 

在以上配置中，将实例设置为 10000000，这样每当在群集中添加新节点时，Datadog 都会将代理部署到此节点。 这是一种过渡解决方案。 安装此包后，应导航回 Datadog 网站，找到“[仪表板](https://app.datadoghq.com/dash/list)”。 将从此处看到自定义和集成仪表板。 [Docker 仪表板](https://app.datadoghq.com/screen/integration/docker)包含监视群集所需的所有容器指标。 


