---
title: Azure HPC 缓存的区域冗余和故障转移恢复
description: 用于通过 Azure HPC 缓存为灾难恢复提供故障转移功能的技术
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4eb203915c8fedbef6af0e5a3bc14eff1835a92b
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75982161"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>使用多个缓存进行区域故障转移恢复

每个 Azure HPC 缓存实例在特定订阅和一个区域中运行。 这意味着，如果区域出现完全中断，则可能会中断缓存工作流。

本文介绍了一种策略，通过使用第二个区域进行缓存故障转移，降低工作中断的风险。

密钥使用的是可从多个区域访问的后端存储。 此存储可以是具有适当 DNS 支持的本地 NAS 系统，也可以是驻留在与缓存不同的区域中的 Azure Blob 存储。

当你的工作流在主要区域中继续时，数据将保存在区域外的长期存储中。 如果缓存区域变得不可用，则可以在次要区域中创建重复的 Azure HPC 缓存实例，连接到相同的存储，并从新缓存恢复工作。

## <a name="planning-for-regional-failover"></a>规划区域故障转移

若要设置已准备好进行故障转移的缓存，请执行以下步骤：

1. 请确保可在第二个区域中访问后端存储。
1. 计划创建主缓存实例时，还应准备好在第二个区域复制此安装过程。 包括以下项：

   1. 虚拟网络和子网结构
   1. 缓存容量
   1. 存储目标详细信息、名称和命名空间路径
   1. 有关客户端计算机与缓存位于同一区域中的详细信息
   1. 供缓存客户端使用的装载命令

   > [!NOTE]
   > 可以通过[azure 资源管理器模板](../azure-resource-manager/templates/overview.md)以编程方式或通过直接访问其 API 来创建 Azure HPC 缓存。 有关详细信息，请联系 Azure HPC 缓存团队。

## <a name="failover-example"></a>故障转移示例

例如，假设你想要在 Azure 美国东部地区查找 Azure HPC 缓存。 它将访问存储在本地数据中心的数据。

可以在美国西部2区域中使用缓存作为故障转移备份。

在美国东部创建缓存时，请在美国西部2准备要部署的第二个缓存。 可以使用脚本或模板自动执行此准备工作。

如果在美国东部发生区域范围的故障，请在美国西部2区域创建缓存。

创建缓存后，添加指向同一本地数据存储的存储目标，并使用与旧缓存的存储目标相同的聚合命名空间路径。

如果原始客户端受到影响，请在美国西部2区域创建新的客户端，以便与新缓存一起使用。

所有客户端都需要装载新的缓存，即使客户端不受区域服务中断的影响。 新缓存有不同于旧缓存的装入地址。

## <a name="learn-more"></a>了解更多

《 Azure 应用程序体系结构指南》中提供了有关如何[在区域范围的服务中断](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)后进行恢复的详细信息。
<!-- this should be an internal link instead of a URL but I can't find the tree  -->
