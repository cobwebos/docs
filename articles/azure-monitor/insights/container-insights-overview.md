---
title: 用于容器的 Azure Monitor（预览版）概述 | Microsoft Docs
description: 本文介绍用于容器的 Azure Monitor，它通过监视 AKS 群集和 Azure 中容器实例的运行状况监视 AKS 容器见解解决方案及其提供的值。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: magoedte
ms.openlocfilehash: 084b79d0738cdad2b95499a9004e89b15d052b42
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713847"
---
# <a name="azure-monitor-for-containers-preview-overview"></a>用于容器的 Azure Monitor（预览版）概述

用于容器的 Azure Monitor 功能旨在监视部署到 Azure Kubernetes 服务 (AKS) 上托管的托管 Kubernetes 群集的容器工作负荷的性能。 监视容器至关重要，特别是在大规模运行包含多个应用程序的生产群集时。

用于容器的 Azure Monitor 通过 Metrics API 从 Kubernetes 中提供的控制器、节点和容器收集内存和处理器指标，来提供性能可见性。 容器日志也会收集。  从 Kubernetes 群集启用监视后，将通过适用于 Linux 的 Log Analytics 代理的容器化版本自动收集这些指标和日志，并将其存储在 [Log Analytics](../../log-analytics/log-analytics-queries.md) 工作区中。 
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>用于容器的 Azure Monitor 提供了什么？

用于容器的 Azure Monitor 包含几个预定义的视图，这些视图显示驻留的容器工作负载，以及影响受监视的 Kubernetes 群集性能运行状况的因素，以便你可以：  

* 确定节点上运行的 AKS 容器及其平均处理器和内存利用率。 此信息可帮助标识资源瓶颈。
* 确定容器在控制器或 Pod 中的驻留位置。 此信息可帮助了解控制器或 Pod 的整体性能。 
* 查看在主机上运行的与支持 Pod 的标准过程无关的工作负荷的资源利用率。
* 了解群集在平均负载和最重负载下的行为。 此信息有助于了解容量需求及确定群集可承受的最大负载。 

## <a name="how-do-i-access-this-feature"></a>如何访问此功能？
可以通过两种方式访问用于容器的 Azure Monitor：从 Azure Monitor 访问或直接从所选 AKS 群集访问。 在 Azure Monitor 中可以从全局角度查看已部署的所有容器（受到监视的容器和未受监视的容器），从而可以跨订阅和资源组进行搜索和筛选，然后从所选容器向下钻取到用于容器的 Azure Monitor。  否则，可以简单地直接从 AKS 页上选定的 AKS 容器访问该功能。  

![访问用于容器的 Azure Monitor 的方法概述](./media/container-insights-overview/azmon-containers-views.png)

如果想要监视和管理 Docker 和 Windows 容器主机以查看配置、审核和资源利用率，请参阅[容器监视解决方案](../../log-analytics/log-analytics-containers.md)。

## <a name="next-steps"></a>后续步骤
若要开始监视 AKS 群集，请查看[如何载入用于容器的 Azure Monitor](container-insights-onboard.md) 以了解启用监视的要求和可用方法。  
