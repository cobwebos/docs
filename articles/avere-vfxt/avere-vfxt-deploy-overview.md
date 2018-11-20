---
title: 部署概述 - Avere vFXT for Azure
description: Avere vFXT for Azure 部署概述
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: aa5737d67ea2c9cb8cc7c7098764ae67fc91137d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669814"
---
# <a name="avere-vfxt-for-azure---deployment-overview"></a>Avere vFXT for Azure - 部署概述

本文概述启动并运行 Avere vFXT for Azure 群集所要执行的步骤。

首次部署 Avere vFXT 系统时，你可能会注意到，部署该系统所涉及到的步骤比部署其他大多数 Azure 工具都要多。 对自始至终的整个过程有个明确的认识将有助于规划所要完成的工作。 该系统启动并运行后，它的强大功能会加速基于云的计算任务，让你的付出得到回报。

## <a name="deployment-steps"></a>部署步骤

[规划系统](avere-vfxt-deploy-plan.md)后，可以开始创建 Avere vFXT 群集。 

首先请创建用于创建 vFXT 群集的群集控制器 VM。

vFXT 群集启动并运行后，需要知道如何将客户端连接到该群集，如有必要，还需要知道如何将数据移到新的 Blob 存储容器。  

下面是所有步骤的概述。

1. 配置先决条件 

   在创建 VM 之前，必须创建 Avere vFXT 项目的新订阅、配置订阅所有权、检查配额并根据需要请求提高配额，并接受 Avere vFXT 软件的使用条款。 有关详细说明，请参阅[准备创建 Avere vFXT](avere-vfxt-prereqs.md)。

1. 创建群集控制器

   群集控制器是驻留在 Avere vFXT 群集所在的同一虚拟网络中的简单 VM。 该控制器可创建 vFXT 节点并组建群集，此外，还提供一个命令行接口，用于在群集的整个生存期内对其进行管理。

   如果使用公共 IP 地址配置了控制器，则该控制器还可以充当跳接主机，用于从 VNet 外部连接到群集。

   群集控制器中已预先安装创建 vFXT 群集和管理其节点所需的所有软件。

   有关详细信息，请参阅[创建群集控制器 VM](avere-vfxt-deploy.md#create-the-cluster-controller-vm)。

1. 创建群集节点的运行时角色 

   Azure 使用[基于角色的访问控制](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) 来授权群集节点 VM 执行特定的任务。 例如，群集节点需要能够向其他群集节点分配或重新分配 IP 地址。 在创建群集之前，必须定义一个角色，以便为这些节点授予足够的权限。

   群集控制器的预装软件包含可自定义的原型角色。 有关说明，请参阅[创建群集节点访问角色](avere-vfxt-deploy.md#create-the-cluster-node-access-role)。

1. 创建 Avere vFXT 群集 

   在控制器中编辑相应的群集创建脚本，并执行该脚本来创建群集。 [编辑部署脚本](avere-vfxt-deploy.md#edit-the-deployment-script)中提供了详细说明。 

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