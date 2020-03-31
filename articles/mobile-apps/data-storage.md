---
title: 使用 Visual Studio 应用中心和 Azure 服务在云中存储、管理和持久化应用程序数据
description: 了解 Visual Studio 应用中心等服务，这些服务允许您在云中存储、管理和持久化移动应用程序数据。
author: codemillmatt
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 166847325fa9094136f1c2a143f1751420f05f66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240914"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>从云存储、同步和查询移动应用程序数据
无论您构建何种应用程序，您都可能会生成和处理数据。 应用程序的用户有很高的期望。 他们希望应用程序在所有情况下快速、无缝地工作。 大多数应用程序还跨多个设备工作。 您可以通过桌面或移动设备访问应用程序。 多个用户可能同时使用该应用程序并共享数据，期望获得对数据的即时和实时访问。

您的应用程序用户并不总是具有互联网连接。 应用程序被设计和期望使用或不使用互联网连接。 开发人员必须选择正确的解决方案来存储和同步其数据到云，为其应用程序提供出色的客户体验。

Microsoft 提供各种服务，无需启动服务器、选择数据库或担心规模或安全性，从而提供尽可能丰富的体验。 这些服务提供了出色的开发人员体验，允许您使用 SQL 或 NoSQL API 在云中存储应用程序数据。 您还可以在所有设备上同步数据，并使应用程序能够使用或不带网络连接，以帮助构建可扩展且可靠的应用程序。

使用以下服务在云中管理和存储移动应用程序数据。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[应用中心数据](/appcenter/data/)是一种数据管理服务。 它使应用程序能够在联机和脱机方案中跨不同的设备和平台管理、持久和同步云中的应用程序数据。 应用中心数据构建在 Azure Cosmos DB 之上，随着用户群和应用程序数量的增加而扩展。 它可确保低延迟、高可用性和高可扩展性，为您的所有数据。

**主要功能**
- 从可视化工作室应用中心门户轻松预配到新的 Azure Cosmos 数据库或现有的 Azure Cosmos 数据库。
- NoSQL 数据库支持轻松存储、同步和查询应用程序数据。
- 此服务基于 Azure Cosmos DB 构建，继承了 Azure Cosmos DB 提供的大部分关键功能，并可以在全球范围内扩展以满足业务需求。
- 在线和脱机同步功能，用于跨设备同步数据。
- 移动客户端 SDK，可用于轻松管理专用应用程序数据。
- 对 iOS、Android、Xamarin 和反应本机平台支持。

**引用**
- [使用可视化工作室应用中心注册](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [开始使用应用中心数据](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)是一个全局分布的多模型数据库服务。 您可以使用它构建行星级应用程序。 使用 Azure Cosmos DB，您可以弹性地独立地扩展全球任意数量的 Azure 区域的吞吐量和存储。 您可以使用您最喜爱的 API 曲面进行快速的、单位毫秒的数据访问。 这些曲面包括 SQL、蒙戈DB、卡桑德拉、表或格雷姆林。 Azure Cosmos DB 唯一提供全面的服务级别协议 （SL），用于吞吐量、延迟、可用性和一致性。

**主要功能**
- 支持广泛的 API，包括 SQL （核心） API、卡桑德拉 API、蒙戈DB API、Gremlin API 和表 API。
- 交钥匙全球分发可随时随地复制您的数据。 用户可以与最接近他们的数据的副本进行交互。
- 没有架构或索引管理，因为数据库引擎是完全架构无关的。
- 无处不在的区域存在，因为 Azure Cosmos DB 在全球所有 Azure 区域都可用，包括公共云中的 54 多个区域。
- 定义精确、多重一致性选项，因为 Azure Cosmos DB 的多主机复制协议经过精心设计，可提供五个定义良好的一致性选项。 这五个选项是强的、有边界的过时、会话、一致的前缀和最终选项。
- 99.999% 的读取和写入可用性。
- 以编程方式（或通过 Azure 门户）调用 Azure Cosmos DB 帐户的区域故障转移，以确保应用程序的设计能够抵御区域灾难。
- 保证全球第 99 个百分位时的低延迟。

**引用**
- [Azure 门户](https://portal.azure.com) 
- [Azure 宇宙数据库文档](/azure/cosmos-db/introduction)

## <a name="azure-sql-database"></a>Azure SQL 数据库
 [Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)是一个通用关系数据库托管服务。 可以使用它为 Azure 云中的应用程序和解决方案创建高可用性和高性能数据存储层。

**主要功能**
- **弹性数据库模型和工具：** 使用弹性数据库，开发人员可以将资源汇集到一组数据库中进行缩放。 要管理这些资源，请将脚本作为作业提交。 然后，SQL 数据库在数据库中执行脚本。
- **高性能：** 高通量应用程序可以利用最新版本。 它可提供 25% 以上的高级数据库功能。
- **备份、复制和高可用性：** 内置复制和 Microsoft 支持的 SLA 在数据库级别提供应用程序连续性和抵御灾难性事件的保护。 活动异地复制允许您配置故障转移和自助服务还原，从而提供对"oops 恢复"的完全控制。 数据恢复可从最多 35 天的数据备份中获取。
- **接近零的维护：** 自动软件是服务的一部分。 内置系统副本有助于提供固有的数据保护、数据库停机时间和系统稳定性。 系统副本将自动移动到新计算机。 它们在旧旧的失败时被动态配置。
- **安全性：** Azure SQL 数据库提供一系列安全功能，以满足组织或行业授权的合规性策略：
    - 审核使开发人员能够执行与合规性相关的任务，并获取有关活动的知识。
    - 开发人员和 IT 可以在数据库级别实施策略，以帮助通过 Azure SQL 数据库的行级安全性、动态数据掩蔽和透明数据加密来限制对敏感数据的访问。
    - Azure SQL 数据库由关键云审核员验证，作为关键 Azure 合规性认证和批准范围的一部分，例如 HIPAA BAA、ISO/IEC 27001：2005、FedRAMP 和欧盟模型条款。

**引用**
- [Azure 门户](https://portal.azure.com) 
- [Azure SQL 数据库文档](/azure/sql-database/) 
