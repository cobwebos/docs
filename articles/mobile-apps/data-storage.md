---
title: 通过 Visual Studio App Center 和 Azure 服务在云中存储、管理和保存应用程序数据
description: 了解 App Center 的服务，这些服务使你能够在云中存储、管理和保存移动应用程序数据。
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b7043c7e9bd944bfb3633397edfa3c35609bd8ec
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795570"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>从云中存储、同步和查询移动应用程序数据
无论生成哪种类型的应用程序，您都可能会生成和处理数据。 用户的期望很高，而且希望应用程序能够在所有情况下快速、无缝地工作。 大多数应用程序都可跨多个设备工作，因此，在访问应用程序时，可以从桌面或移动设备进行访问。 多个用户可能同时使用该应用程序，并将数据共享到即时实时访问数据的期望。

最重要的是，应用程序用户不会始终具有 internet 连接。 应用程序设计为使用或不使用 internet 连接。 随着所有这些日益复杂的复杂性，开发人员可以选择适当的解决方案来存储数据并将其同步到云，从而为应用程序提供卓越的客户体验。

Microsoft 提供了各种服务，无需启动服务器、选择数据库或担心规模或安全性，从而尽可能提供丰富的体验。 这些服务提供了很好的开发人员体验，可让你使用 SQL 或 NoSQL Api 将应用程序数据存储在云中，同步所有设备上的数据，并使应用程序可以使用或不使用网络连接来帮助构建可缩放且可靠的应用程序.

使用以下服务在云中管理和存储移动应用程序数据。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 数据](/appcenter/data/)是一种数据管理服务，使应用程序能够在联机和脱机方案中跨不同设备和平台管理、保留和同步云中的应用程序数据。 此服务**基于 Cosmos DB 服务构建**，可随着用户群和应用程序数量的增加而扩展，此服务可确保所有数据的低延迟、高可用性和高可伸缩性。

**关键功能**
- 从 App Center 门户轻松**预配新的 Cosmos DB 数据库**或**连接到现有的 Cosmos DB 数据库**。
- **NoSQL 数据库支持**轻松存储、同步和查询应用程序数据。
- 此服务是**在 Cosmos DB 的基础上构建**的，它继承了 Azure Cosmos DB 提供的大多数关键功能，并可在**全球范围内进行缩放**以满足你的业务需求。
- **联机和脱机同步**功能跨设备同步数据。
- 使你能够轻松管理私有应用程序数据的移动**客户端 sdk** 。
- **平台支持**-IOS、Android、Xamarin、响应本机。

**参考**
- [注册 App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center 数据入门](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)是 Microsoft 的全球分布式多模型数据库服务，可帮助你构建全球规模的应用程序。 只需单击一个按钮，即可通过 Cosmos DB 跨任意数量的全球 Azure 区域弹性且独立地缩放吞吐量和存储。 可以弹性缩放吞吐量和存储空间，并使用最喜欢的 API 图面（包括 SQL、MongoDB、Cassandra、Tables 或 Gremlin）利用快速的单位数毫秒数据访问。 Cosmos DB 为吞吐量、延迟、可用性和一致性提供全面的服务级别协议（Sla）。

**关键功能**
- 支持**各种 api** ，包括 SQL （CORE） api、Cassandra API、MongoDB Api、Gremlin api 和表 API。
- 当你的用户在任何位置都**可以复制你**的数据，因此你的用户可以与最接近它们的数据副本交互。
- 数据库引擎没有完全与架构无关的**架构或索引管理**。
- 在全球所有 Azure 区域中都提供了 Cosmos DB 无处不在的**区域**，包括在公有云中的54个以上的区域。
- **准确定义的是，将多个一致性选择**作为 Cosmos DB 的多主复制协议，旨在提供5个明确定义的一致性选择-强、有限过期、会话、一致前缀和最终。
- 对于读取和写入， **99.999% 的可用性**。
- **以编程方式（或通过门户）调用 Cosmos 帐户的区域故障转移**，以确保应用程序可经受地区性灾难。
- **在全球99% 的百分位保证低延迟**。

**参考**
- [Azure 门户](https://portal.azure.com) 
- [文档](/azure/cosmos-db/introduction)   

## <a name="azure-sql-database"></a>Azure SQL Database
 [AZURE SQL 数据库](https://azure.microsoft.com/services/sql-database/)是一种通用的关系数据库托管服务，可用于为 Azure 云中的应用程序和解决方案创建高度可用且高性能的数据存储层。

**关键功能**
- **弹性数据库模型和工具**-弹性数据库使开发人员能够在一组数据库中使用资源来进行缩放，然后将脚本作为作业提交，然后 SQL 数据库执行跨数据库编写脚本。
- **高性能**-高吞吐量应用程序可利用最新版本，该版本可提供25% 的高级数据库能力。
- **备份、复制和高可用性**-在数据库级别的内置复制和支持 MICROSOFT 的 SLA 可为灾难性事件提供应用程序连续性和保护。 活动异地复制可让你配置故障转移和自助服务还原，后者提供对 "糟糕恢复" （从最多35天的可用数据备份的数据还原）的完全控制。
- **近零维护**-自动软件是服务的一部分，内置的系统副本帮助提供固有的数据保护、数据库运行时间和系统稳定性。 旧计算机发生故障时，系统副本会自动转移到系统配置的新计算机。
- **安全**-SQL 数据库提供一系列安全功能，以满足组织或行业强制符合性策略
    - 审核使开发人员能够执行符合性相关的任务并获取有关活动的知识。
    - 开发人员和 IT 人员可以在数据库级别实施策略，以使用 Azure SQL 数据库的行级安全性、动态数据掩码和透明数据加密来帮助限制对敏感数据的访问。
    - SQL 数据库是由关键云审核机构验证的，它是 Azure 符合性认证和批准的作用域（例如 HIPAA BAA、ISO/IEC 27001:2005、FedRAMP 和欧盟型号条款）的一部分。

**参考**
- [Azure 门户](https://portal.azure.com) 
- [文档](/azure/sql-database/)
   