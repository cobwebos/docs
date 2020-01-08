---
title: 通过 Visual Studio App Center 和 Azure 服务在云中存储、管理和保存应用程序数据
description: 了解 Visual Studio App Center 的服务，这些服务使你能够在云中存储、管理和保存移动应用程序数据。
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 04de01e1fd3dba88145c7f3748ca30b31ab1740a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454453"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>从云中存储、同步和查询移动应用程序数据
无论构建何种类型的应用程序，都可能生成和处理数据。 您的应用程序的用户的期望很高。 它们希望应用程序在所有情况下都能快速、无缝地工作。 大多数应用程序也可跨多个设备工作。 可从桌面或移动设备访问应用程序。 多个用户可以同时使用该应用程序，并与即时实时访问数据的期望共享数据。

应用程序用户不会始终具有 internet 连接。 应用程序设计为使用或不使用 internet 连接。 开发人员必须选择适当的解决方案来存储数据并将其同步到云，以便为应用程序提供卓越的客户体验。

Microsoft 提供了各种服务，无需启动服务器、选择数据库或担心规模或安全性，从而尽可能提供丰富的体验。 这些服务提供了很好的开发人员体验，可让你使用 SQL 或 NoSQL Api 在云中存储应用程序数据。 你还可以同步所有设备上的数据，并使应用程序可以使用或不使用网络连接来帮助构建可缩放且可靠的应用程序。

使用以下服务在云中管理和存储移动应用程序数据。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 数据](/appcenter/data/)是数据管理服务。 它使应用程序能够在联机和脱机方案中跨不同设备和平台管理、保留和同步云中的应用程序数据。 App Center 数据在 Azure Cosmos DB 的基础上构建，可随着用户群和应用程序的增长而扩展。 它可确保所有数据的低延迟、高可用性和高可伸缩性。

**主要功能**
- 从 Visual Studio App Center 门户轻松预配到新的 Azure Cosmos DB 数据库或现有的 Azure Cosmos DB 数据库。
- NoSQL 数据库支持轻松存储、同步和查询应用程序数据。
- 此服务是在 Azure Cosmos DB 的基础上构建的，它继承了 Azure Cosmos DB 提供的大多数关键功能，并可在全球范围内进行缩放以满足你的业务需求。
- 联机和脱机同步功能跨设备同步数据。
- 可用于轻松管理私有应用程序数据的移动客户端 Sdk。
- 适用于 iOS、Android、Xamarin 和响应本机的平台支持。

**参考**
- [注册 Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center 数据入门](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 是一种全球分布式多模型数据库服务。 你可以使用它来构建全球规模的应用程序。 利用 Azure Cosmos DB，你可以跨全球任意数量的 Azure 区域弹性、独立地缩放吞吐量和存储。 你可以通过使用你最喜欢的 API 图面来利用快速的、一位数的毫秒数据访问。 这些图面包括 SQL、MongoDB、Cassandra、Tables 或 Gremlin。 Azure Cosmos DB 为吞吐量、延迟、可用性和一致性提供全面的服务级别协议（Sla）。

**主要功能**
- 支持范围广泛的 Api，其中包括 SQL （核心） API、Cassandra API、MongoDB API、Gremlin API 和表 API。
- 如果你的用户在任何位置，则全包式全局分发将复制数据。 你的用户可以与最近的数据副本交互。
- 无架构或索引管理，因为数据库引擎的架构完全不可知。
- 无处不在世界各地，因为 Azure Cosmos DB 在所有 Azure 区域提供，在公有云中包括54个以上的区域。
- 精确定义的多个一致性选项，因为 Azure Cosmos DB 的多主复制协议旨在提供五个明确定义的一致性选择。 这五个选项包括：强、有限过期、会话、一致前缀和最终。
- 对于读取和写入，99.999% 的可用性。
- 以编程方式（或通过 Azure 门户）调用 Azure Cosmos DB 帐户的区域故障转移，以确保应用程序可经受地区性灾难。
- 在全球99% 的百分位保证低延迟。

**参考**
- [Azure 门户](https://portal.azure.com) 
- [Azure Cosmos DB 文档](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL Database
 [AZURE SQL 数据库](https://azure.microsoft.com/services/sql-database/)是一种通用的关系数据库托管服务。 可以使用它为 Azure 云中的应用程序和解决方案创建高度可用且高性能的数据存储层。

**主要功能**
- **弹性数据库模型和工具：** 使用弹性数据库，开发人员可在一组数据库中对资源进行缓冲以进行缩放。 若要管理这些资源，请将脚本作为作业提交。 然后，SQL 数据库会跨数据库执行脚本。
- **高性能：** 高吞吐量应用程序可利用最新版本。 它提供了25% 的高级数据库能力。
- **备份、复制和高可用性：** 数据库级别的内置复制和支持 Microsoft 的 SLA 提供应用程序的连续性，并防范灾难性事件。 活动异地复制可让你配置故障转移和自助服务还原，以便完全控制 "糟糕的恢复"。 数据还原可从最长为35天的数据备份。
- **近零维护：** 自动软件是服务的一部分。 内置的系统副本帮助提供固有的数据保护、数据库运行时间和系统稳定性。 系统副本会自动转移到新计算机。 由于旧版本失败，它们会动态预配。
- **安全性：** Azure SQL 数据库提供一系列安全功能，以满足组织或行业要求的符合性策略：
    - 审核使开发人员能够执行符合性相关的任务并获取有关活动的知识。
    - 开发人员和 IT 人员可以在数据库级别实施策略，以使用 Azure SQL 数据库的行级安全性、动态数据掩码和透明数据加密来帮助限制对敏感数据的访问。
    - Azure SQL 数据库是由关键云审核机构验证的，它是 Azure 符合性认证和批准（如 HIPAA BAA、ISO/IEC 27001:2005、FedRAMP 和欧盟型号条款）的一部分。

**参考**
- [Azure 门户](https://portal.azure.com) 
- [Azure SQL 数据库文档](/azure/sql-database/) 
