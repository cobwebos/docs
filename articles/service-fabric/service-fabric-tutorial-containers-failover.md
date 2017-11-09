---
title: "故障转移和缩放 Azure Service Fabric 容器应用 | Microsoft Docs"
description: "了解如何在 Azure Service Fabric 容器应用程序中处理故障转移。  还了解如何缩放群集中运行的容器和服务。"
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker, 容器, 微服务, Service Fabric, Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 21dd9dfbc90c26236c43e2c334305ca97f63d361
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="demonstrate-fail-over-and-scaling-of-container-services-with-service-fabric"></a>使用 Service Fabric 演示容器服务的故障转移和缩放

本教程是一个系列中的第三部分。 在本教程中，了解如何在 Service Fabric 容器应用程序中处理故障转移。 此外，了解如何缩放容器。 在本教程中：

> [!div class="checklist"]
> * 了解 Service Fabric 群集中的容器故障转移  
> * 缩放应用程序中的 Web 前端容器

## <a name="prerequisites"></a>先决条件
[第 2 部分](service-fabric-tutorial-package-containers.md)中的应用程序在活动的 Service Fabric 群集中运行。

## <a name="fail-over-a-container-in-a-cluster"></a>故障转移群集中的容器
Service Fabric 可确保在发生故障时，将容器实例自动转移到群集中的其他节点。 也可以手动清空容器的节点，然后将其正常转移到群集中的其他节点。 可通过多种方式缩放服务，在本示例中，我们将使用 Service Fabric Explorer。

若要故障转移前端容器，请执行以下步骤：

1. 在群集中打开 Service Fabric Explorer（例如，`http://lin4hjim3l4.westus.cloudapp.azure.com:19080`）。
2. 在树视图中单击“fabric:/TestContainer/azurevotefront”节点，并展开分区节点（由 GUID 表示）。 注意树视图中的节点名称，它显示了当前正在运行容器的节点，例如 `_nodetype_1`
3. 在树视图中展开“节点”节点。 单击正在运行容器的节点旁边的省略号（三个点）。
1. 选择“重启”以重启该节点，并确认重启操作。 重启会导致容器故障转移到群集中的另一个节点。

![noderestart][noderestart]

请注意指示前端容器运行位置的节点名称现在如何更改为群集中的另一个节点。 几分钟后，应该能够重新浏览到应用程序并查看现在运行于其他节点之上的应用程序。

## <a name="scale-containers-and-services-in-a-cluster"></a>缩放群集中的容器和服务
可以跨群集缩放 Service Fabric 容器，以适应服务上的负载。 可以通过更改群集中运行的实例数量来缩放容器。

若要缩放 Web 前端，请执行以下步骤：

1. 在群集中打开 Service Fabric Explorer - 例如 `http://lin4hjim3l4.westus.cloudapp.azure.com:19080`。
2. 在树视图中单击“fabric:/TestContainer/azurevotefront”节点旁边的省略号（三个点），并选择“缩放服务”。

![sfxscale][sfxscale]

现在可以选择缩放 Web 前端的实例数量。

3. 将数字更改为 2，再单击“缩放服务”。
4. 在树视图中单击“fabric:/TestContainer/azurevotefront”节点，并展开分区节点（由 GUID 表示）。

![sfxscaledone][sfxscaledone]

现在可以看到该服务有两个实例。 在树视图中，会看到运行实例的节点。

通过这一简单的管理任务，我们让前端服务用来处理用户负载的资源数量翻了一番。 有必要了解的是，服务无需有多个实例，便能可靠运行。 如果服务出现故障，Service Fabric 可确保在群集中运行新的服务实例。

## <a name="next-steps"></a>后续步骤

在本教程中，已演示容器故障转移以及缩放应用程序。 已完成以下步骤：

> [!div class="checklist"]
> * 了解 Service Fabric 群集中的容器故障转移  
> * 缩放应用程序中的 Web 前端容器

此教程系列介绍了如何： 
> [!div class="checklist"]
> * 创建容器映像
> * 向 Azure 容器注册表推送容器映像
> * 使用 Yeoman 的 Service Fabric 程序包容器
> * 使用容器生成和部署 Service Fabric 应用程序
> * 如何在 Service Fabric 中处理故障转移和缩放

[noderestart]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialnoderestart.png
[sfxscale]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscale.png
[sfxscaledone]: ./media/service-fabric-tutorial-containers-failover/containersfailovertutorialscaledone.png
