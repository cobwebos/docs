---
title: Azure 流分析群集概述（预览版）
description: 了解流分析群集的单租户专用产品/服务。
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 613cf7d9b68fe42c26f2c01cb1fb5dd1da1e1fb5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943415"
---
# <a name="overview-of-azure-stream-analytics-cluster-preview"></a>Azure 流分析群集概述（预览版）

Azure 流分析群集为复杂且要求苛刻的流式处理场景提供单租户部署。 完整规模的流分析群集的实时处理速度超过 200 MB/秒。 在专用群集上运行的流分析作业可以利用标准产品/服务中的所有功能，并支持与输入和输出的私有链接连接。

流分析群集按流单元 (SU) 计费，流单元表示分配给群集的 CPU 和内存资源的数量。 在标准和专用产品/服务中，流单元是相同的。 可为每个群集购买 36、72、108、144、180 或 216 个 SU。 流分析群集可以充当组织的流式处理平台，并可由处理各种用例的不同团队共享。

## <a name="what-are-stream-analytics-clusters"></a>什么是流分析群集

各流分析群集由同一引擎提供支持，该引擎为在多租户环境中运行的流分析作业提供支持。 单租户专用群集具有以下功能：

* 单个租户托管，免除来自其他租户的干扰。 资源被真正地“隔离”，在流量激增时性能更佳。

* 随着时间的推移，流式处理使用量增加，群集可以在 36 到 216 个 SU 之间进行缩放。

* VNet 支持，可让流分析作业使用专用终结点安全地连接到其他资源。

* 能够在任何区域中创作 C# 用户定义函数和自定义反序列化程序。

* 零维护成本，使你能够专注于生成实时分析解决方案。

## <a name="how-to-get-started"></a>如何入门

可以通过 [Azure 门户](https://aka.ms/asaclustercreateportal)[创建流分析群集](create-cluster.md)。 如果你对于加入有任何疑问或需要帮助，可以联系[流分析团队](mailto:askasa@microsoft.com)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-do-i-choose-between-a-stream-analytics-cluster-and-a-stream-analytics-job"></a>如何在流分析群集和流分析作业之间进行选择？

最简单的入门方法是创建和开发流分析作业，熟悉该服务并了解它如何满足你的分析要求。

流分析作业本身不支持 VNet。 如果输入或输出受防火墙或 Azure 虚拟网络的保护，则可以使用以下两个选项：

* 如果本地计算机可以访问由 VNet 保护的输入和输出资源（例如 Azure 事件中心或 Azure SQL 数据库），可以在本地计算机上[安装适用于 Visual Studio 的 Azure 流分析工具](stream-analytics-tools-for-visual-studio-install.md)。 你可以在设备上本地开发和[测试流分析作业](stream-analytics-live-data-local-testing.md)，而不会产生任何费用。 准备好在体系结构中使用流分析后，便可以创建流分析群集、配置专用终结点和大规模运行作业。

* 可创建流分析群集、使用管道所需的专用终结点配置群集并在群集上运行流分析作业。

### <a name="what-performance-can-i-expect"></a>我能期待哪些性能？

在标准和专用产品/服务中，SU 是相同的。 若单个作业利用一个具有 36 个 SU 的完整群集，则它可以在延迟处于毫秒级的情况下实现大约每秒 36 MB 的吞吐量。 确切的数字取决于事件格式和分析类型。 由于它是专用的，因此流分析群集提供更可靠的性能保证。 群集上运行的所有作业仅属于你。

### <a name="can-i-scale-my-cluster"></a>我是否可缩放自己的群集？

是的。 你可以轻松地配置群集的容量，允许你根据需要[纵向扩展或纵向缩减](scale-cluster.md)以满足不断变化的需求。

### <a name="can-i-run-my-existing-jobs-on-these-new-clusters-ive-created"></a>是否可在我创建的这些新群集上运行现有作业？

是的。 可以将现有作业链接到新创建的流分析群集，然后照常运行它。 无需从头开始重新创建现有的流分析作业。

### <a name="how-much-will-these-clusters-cost-me"></a>这些群集将花费多少费用？

流分析群集根据所选 SU 容量收费。 群集按小时计费，在这些群集中运行的每个作业不收取额外费用。 请参阅[专用链接服务定价页](https://azure.microsoft.com/pricing/details/private-link/)，了解专用终结点计费更新。

### <a name="which-inputs-and-outputs-can-i-privately-connect-to-from-my-stream-analytics-cluster"></a>我可以私下从流分析群集连接到哪些输入和输出？

流分析支持多种输入和输出类型。 任何支持 Azure 专用链接的服务都可以私下连接到你的作业。 你可以在群集中[创建专用终结点](private-endpoints.md)，以允许作业访问输入和输出资源。

## <a name="next-steps"></a>后续步骤

你现在已对 Azure 流分析群集有了一个大致的了解。 接下来，你可以创建群集并运行流分析作业： 

* [创建流分析群集](create-cluster.md)
* [在 Azure 流分析群集中管理专用终结点](private-endpoints.md)
* [在流分析群集中管理流分析作业](manage-jobs-cluster.md)
* [创建流分析作业](stream-analytics-quick-create-portal.md)
