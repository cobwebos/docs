---
title: "监视 Azure DC/OS 群集 - Dynatrace | Microsoft Docs"
description: "通过 Dynatrace 监视 Azure 容器服务 DC/OS 群集。 使用 DC/OS 仪表板部署 Dynatrace OneAgent。"
services: container-service
documentationcenter: 
author: MartinGoodwell
manager: 
editor: 
tags: acs, azure-container-service
keywords: "容器, DC/OS, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 6fa23728680779e33eda7bb9aa8a01b9cad9a82b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>通过 Dynatrace SaaS/托管监视 Azure 容器服务 DC/OS 群集
在本文中，我们介绍如何部署 [Dynatrace](https://www.dynatrace.com/) OneAgent 以监视 Azure 容器服务群集中的所有代理节点。 此配置需要 Dynatrace SaaS/托管帐户。 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/托管
Dynatrace 是用于高动态容器和群集环境的云-本机监视解决方案。 它支持使用实时使用数据更好地优化容器部署和内存分配。 它提供自动基线、问题关联和根本原因检测，可自动查明应用程序和基础结构问题。

下图显示 Dynatrace UI：

![Dynatrace UI](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>先决条件 
[部署](container-service-deployment.md)和[连接](./../container-service-connect.md)由 Azure 容器服务配置的群集。 探究 [Marathon UI](container-service-mesos-marathon-ui.md)。 转到 [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/)，设置 Dynatrace SaaS 帐户。  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>通过 Marathon 配置 Dynatrace 部署
这些步骤将演示如何通过 Marathon 将 Dynatrace 应用程序配置和部署到群集中。

1. 通过 [http://localhost:80/](http://localhost:80/) 访问 DC/OS UI。 在位于 DC/OS UI 中后，导航到“Universe”选项卡，并搜索“Dynatrace”。

    ![DC/OS Universe 中的 Dynatrace](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. 现在，要完成该配置，需要一个 Dynatrace SaaS 帐户或免费试用帐户。 登录 Dynatrace 仪表板后，选择“部署 Dynatrace”。

    ![Dynatrace 设置 PaaS 集成](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. 在页面上选择“设置 PaaS 集成”。 

    ![Dynatrace API 令牌](./media/container-service-monitoring-dynatrace/api-token.png) 

4. 将 API 令牌输入到 DC/OS Universe 内的 Dynatrace OneAgent 配置中。 

    ![DC/OS Universe 中的 Dynatrace OneAgent 配置](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. 将该实例设为希望运行的节点数。 设置较大的数字也可以，但 DC/OS 将继续尝试查找新实例，直到确实达到该数字。 如果愿意，还可将此值设为 1000000。 在这种情况下，无论什么时候将新节点添加到群集，Dynatrace 都会自动将代理部署到新节点，代价是 DC/OS 将不断尝试部署更多实例。

    ![DC/OS Universe 实例中的 Dynatrace 配置](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>后续步骤

安装程序包后，请导航回 Dynatrace 仪表板。 可浏览群集中容器的不同使用情况度量。 