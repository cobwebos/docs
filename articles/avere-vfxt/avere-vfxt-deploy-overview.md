---
title: 部署概述 - Avere vFXT for Azure
description: Avere vFXT for Azure 部署概述
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 88305e5d877f5bc3cc7ebb116f69b0f49f162b79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409906"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure - 部署概述

本文概述启动并运行 Avere vFXT for Azure 群集所要执行的步骤。

在从 Azure 市场创建 vFXT 群集之前和之后，需要执行多个任务。 对自始至终的整个过程有个明确的认识将有助于规划所要完成的工作。 

## <a name="deployment-steps"></a>部署步骤

[规划系统](avere-vfxt-deploy-plan.md)后，可以开始创建 Avere vFXT 群集。 

Azure 市场中的 Azure 资源管理器模板会收集必需的信息并自动部署整个群集。 

vFXT 群集启动并运行后，需要知道如何将客户端连接到该群集，如有必要，还需要知道如何将数据移到新的 Blob 存储容器。  

下面是所有步骤的概述。

1. 配置先决条件 

   在创建 VM 之前，必须创建 Avere vFXT 项目的新订阅、配置订阅所有权、检查配额并根据需要请求提高配额，并接受 Avere vFXT 软件的使用条款。 有关详细说明，请参阅[准备创建 Avere vFXT](avere-vfxt-prereqs.md)。

1. 创建 Avere vFXT 群集 

   使用 Azure 市场创建 Avere vFXT for Azure 群集。 模板将收集所需的信息并执行脚本来创建最终产品。

   群集创建涉及以下步骤，所有这些步骤都是通过市场模板执行的： 

   * 创建新的网络基础结构和资源组（如果需要）
   * 创建*群集控制器*  

     群集控制器是驻留在 Avere vFXT 群集所在的同一虚拟网络中的简单 VM，它具有创建和管理群集时所需的自定义软件。 该控制器可创建 vFXT 节点并组建群集，此外，还提供一个命令行接口，用于在群集的整个生存期内对其进行管理。

     如果在部署期间创建新的 vnet，请在控制器将具有一个公共 IP 地址。 这意味着该控制器可用作跳转主机连接到 vnet 的外部从群集。

   * 创建群集节点 VM

   * 配置群集节点 Vm 来形成群集

   * （可选）创建新的 Blob 容器并将其配置为群集的后端存储

1. 配置群集 

   连接到 Avere vFXT 配置接口（Avere 控制面板）以自定义群集的设置。 选择启用监视支持；如果使用本地数据中心，请添加存储系统。

   * [访问 vFXT 群集](avere-vfxt-cluster-gui.md)
   * [启用支持](avere-vfxt-enable-support.md)
   * [配置存储](avere-vfxt-add-storage.md)（如果需要）

1. 装载客户端

   遵循[装载 Avere vFXT 群集](avere-vfxt-mount-clients.md)中的指导原则来了解负载均衡，以及客户端计算机应如何装载群集。

1. 添加数据（如果需要）

   由于 Avere vFXT 是可缩放的多客户端缓存，将数据移到新后端存储容器的最佳方式是使用多客户端多线程文件复制策略。 有关详细信息，请参阅[将数据移到 vFXT 群集](avere-vfxt-data-ingest.md)。

## <a name="next-steps"></a>后续步骤

继续阅读[准备创建 Avere vFXT](avere-vfxt-prereqs.md)，以完成部署 Avere vFXT for Azure 所需的预备任务。 