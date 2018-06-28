---
title: include 文件
description: include 文件
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: cab04a7eafbc21e0d26cd5a287f3dbee8d3d22b7
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "31805092"
---
## <a name="scenario"></a>场景
本文档指导完成在特定方案中使用 VM 中的多个 NIC 的部署。 在此方案中，在 Azure 中托管两层 IaaS 工作负荷。 每个层部署在虚拟网络 (VNet) 中其自己的子网中。 前端层由几个 Web 服务器组成，这些服务器在负载均衡器集中组合在一起以实现高可用性。 后端层由几个数据库服务器组成。 这些数据库服务器部署为每个具有两个 NIC，一个用于数据库访问，另一个用于管理。 此方案还包括网络安全组 (NSG) 以控制哪些流量允许到达部署中的每个子网和 NIC。 下图显示了此方案的基本体系结构：

![MultiNIC 方案](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

