---
title: 使用 Azure HPC 缓存进行区域冗余和故障转移恢复
description: 使用 Azure HPC 缓存为灾难恢复提供故障转移功能的技术
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 21074ae6bc4959da031bc7065cd7d0639ec2a14f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537264"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>使用多个缓存进行区域故障转移恢复

每个 Azure HPC 缓存实例都在特定订阅中运行，并在一个区域中运行。 这意味着，如果区域完全中断，您的缓存工作流可能会中断。

本文介绍了一种通过使用第二个区域进行缓存故障转移来降低工作中断风险的策略。

关键是使用可从多个区域访问的后端存储。 此存储可以是具有适当 DNS 支持的本地 NAS 系统，也可以是驻留在与缓存不同的区域中的 Azure Blob 存储。

当工作流在主区域中进行时，数据将保存在区域外部的长期存储中。 如果缓存区域不可用，则可以在辅助区域中创建重复的 Azure HPC 缓存实例，连接到同一存储，并从新缓存中恢复工作。

## <a name="planning-for-regional-failover"></a>区域故障转移的规划

要设置为可能的故障转移准备的缓存，请按照以下步骤操作：

1. 确保后端存储在第二个区域中可访问。
1. 在计划创建主缓存实例时，还应准备在第二个区域中复制此设置过程。 包括以下项目：

   1. 虚拟网络和子网结构
   1. 缓存容量
   1. 存储目标详细信息、名称和命名空间路径
   1. 有关客户端计算机的详细信息（如果它们与缓存位于同一区域）
   1. 用于缓存客户端的装载命令

   > [!NOTE]
   > Azure HPC 缓存可以通过[Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)或直接访问其 API 以编程方式创建。 有关详细信息，请与 Azure HPC 缓存团队联系。

## <a name="failover-example"></a>故障转移示例

例如，假设您要在 Azure 的东美国区域中定位 Azure HPC 缓存。 它将访问存储在本地数据中心的数据。

您可以将美国西部 2 区域中的缓存用作故障转移备份。

在美国东部创建缓存时，准备第二个缓存以在西部美国 2 中部署。 您可以使用脚本或模板来自动执行此准备工作。

如果美国东部发生区域范围故障，请创建您在美国西部 2 区域准备的缓存。

创建缓存后，添加指向同一本地数据存储的存储目标，并使用与旧缓存的存储目标相同的聚合命名空间路径。

如果原始客户端受到影响，请在美国西部 2 区域创建新客户端以与新缓存一起使用。

所有客户端都需要装载新缓存，即使客户端不受区域中断的影响。 新缓存具有与旧缓存不同的装载地址。

## <a name="learn-more"></a>了解详细信息

Azure 应用程序体系结构指南包含有关如何[从区域范围的服务中断中恢复](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>)的详细信息。
