---
title: "无服务器数据库计算 - Azure Functions 和 Azure Cosmos DB| Microsoft Docs"
description: "了解 Azure Cosmos DB 和 Azure Functions 如何一起使用，以创建事件驱动型无服务器计算应用。"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mimig
ms.openlocfilehash: 192bdde86e12472a6fd87ba50fd597ea8b01247d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-serverless-database-computing-using-azure-functions"></a>Azure Cosmos DB：使用 Azure Functions 的无服务器数据库计算

无服务器计算涉及关注可重复和无状态的各部分逻辑的功能。 这些部分无需基础结构管理，并且仅消耗几秒或几毫秒运行占用的资源。 无服务器计算移动的核心是函数，这些函数在 Azure 生态系统中通过 [Azure Functions](https://azure.microsoft.com/services/functions) 使用。

借助 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) 和 Azure Functions 的本机集成，可以直接在 Azure Cosmos DB 帐户中创建数据库触发器、输入绑定和输出绑定。 通过使用 Azure Functions 和 Azure Cosmos DB，可以创建和部署事件驱动的无服务器应用，并可使全局用户群低延迟访问丰富的数据。

## <a name="overview"></a>概述

Azure Cosmos DB 和 Azure Functions 支持采用以下方式集成数据库和无服务器应用：

* 在 Azure Function 中创建事件驱动的 **Azure Cosmos DB 触发器**。 此触发器依靠[更改源](change-feed.md)流监视 Azure Cosmos DB 容器的更改情况。 当对容器进行任何更改时，更改源流将发送到可调用 Azure Function 的触发器。
* 或者，使用“输入绑定”将 Azure Function 绑定到 Azure Cosmos DB 集合。 执行某个函数时，输入绑定将从容器中读取函数。
* 使用“输出绑定”将函数绑定到 Azure Cosmos DB 集合。 当函数执行完成时，输出绑定会将数据写入容器。

下图介绍了所有这三种集成： 

![Azure Cosmos DB 和 Azure Functions 如何集成](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Azure Cosmos DB 触发器、输入绑定和输出绑定可在以下组合中使用：
* Azure Cosmos DB 触发器可与其他 Azure Cosmos DB 容器的输出绑定一起使用。 函数在更改源中的某个项目上执行操作后，可以将其写入其他容器（将其写入其来源的同一容器将有效创建递归循环）。 或者，将 Azure Cosmos DB 触发器与某个输出绑定一起使用，可有效地将所有更改的项目从一个容器迁移到其他容器。
* Azure Cosmos DB 的输入绑定和输出绑定可在相同 Azure Function 中使用。 这非常适用于以下情况：使用输入绑定查找某些数据，在 Azure Function 中进行修改，并在修改后将其保存到相同容器或不同容器。
* Azure Cosmos DB 容器的输入绑定可在 Azure Cosmos DB 触发器的相同函数中使用，并且无论是否与输出绑定一起使用都可正常运行。 可以使用此组合以将最新汇率信息（使用输入绑定提取到汇率容器）应用到购物车服务中新订单的更改源。 已对更新的购物车总额应用当前的货币兑换，可以使用输出绑定将其写入第三个容器中。

> [!NOTE]
> 此时，Azure Cosmos DB 触发器、输入绑定和输出绑定仅可与 DocumentDB、表和图形 API 帐户一起使用。

## <a name="use-cases"></a>用例

下面的用例展示通过将数据连接到事件驱动的 Azure Functions，可充分利用 Azure Cosmos DB 数据的几种方法。

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>IoT 用例 - Azure Cosmos DB 触发器和输出绑定

在 IoT 实现中，当引擎检查灯显示在连接的汽车中时，可以调用一个函数。

**实现：**使用 Azure Cosmos DB 触发器和输出绑定

1. **Azure Cosmos DB 触发器**用于触发与汽车警报相关的事件，例如引擎检查灯在连接的汽车上发光。
2. 当引擎检查灯发光时，传感器数据将发送到 Azure Cosmos DB。
3. Azure Cosmos DB 创建或更新新的传感器数据文档，然后这些更改将流式传输到 Azure Cosmos DB 触发器。
4. 每当传感器数据集合发生数据更改时都会调用触发器，因为所有更改均通过更改源流式传输。
5. 在函数中使用阈值条件以将传感器数据发送到保修部门。
6. 如果温度也超过特定值，也会向所有者发送警报。
7. 函数中的“输出绑定”更新其他 Azure Cosmos DB 集合中的汽车记录，以存储关于检查引擎事件的信息。

下图显示在 Azure 门户中为此触发器编写的代码。

![在 Azure 门户中创建 Azure Cosmos DB 触发器](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>财务用例 - 计时器触发器和输入绑定

在财务实现中，当银行帐户余额低于特定金额时，可以调用一个函数。

**实现：**使用 Azure Cosmos DB 输入绑定的计时器触发器

1. 通过使用 [计时器触发器](../azure-functions/functions-bindings-timer.md)，可以使用“输入绑定”每隔一定时间检索存储在 Azure Cosmos DB 容器中的银行帐户余额信息。
2. 如果余额低于用户设置的低余额阈值，则采取 Azure Function 中的某个措施。
3. 输出绑定可以是 [SendGrid 集成](../azure-functions/functions-bindings-sendgrid.md)，它可将电子邮件从服务帐户发送到为每个低余额帐户标识的电子邮件地址。

下图显示了 Azure 门户中适用于此方案的代码。

![用于财务方案的计时器触发器的 Index.js 文件](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![用于财务方案的计时器触发器的 Run.csx 文件](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>游戏用例 - Azure Cosmos DB 触发器和输出绑定

在游戏中，当新用户创建时，可以使用 [Azure Cosmos DB 图形 API](graph-introduction.md) 搜索可能知道新用户的其他用户。 然后，将结果写入 [Azure Cosmos DB 表数据库](table-introduction.md)以便于检索。

**实现：**使用 Azure Cosmos DB 触发器和输出绑定

1. 通过使用 Azure Cosmos DB [图形数据库](graph-introduction.md)存储所有用户，可以使用 Azure Cosmos DB 触发器创建新函数。 
2. 每当插入新用户时，都将调用该函数，然后使用“输出绑定”存储结果。
3. 该函数将查询图形数据库，以搜索与新用户直接相关的所有用户，并将该数据集返回到函数。
4. 随后，此数据作为一组键值对存储在 Azure Cosmos DB [表数据库](table-introduction.md)中，并且这些键值对可由任何前端应用程序（向新用户显示有联系的好友）轻松检索。

### <a name="retail-use-case---multiple-functions"></a>零售用例 - 多个函数

在零售实现中，当用户向购物篮添加商品时，可以为可选业务管道组件灵活创建和调用函数。

**实现：**侦听一个集合的多个 Azure Cosmos DB 触发器

1. 通过将 Azure Cosmos DB 触发器添加到每个 Azure Functions 可以创建多个 Azure Functions，它们全部都侦听购物车数据的同一更改源。 
2. 每当新商品添加到用户的购物车时，更改源都将从购物车容器中独立调用每个函数。
    * 一个函数可能使用当前购物篮的内容更改用户可能有兴趣的其他商品的显示内容。
    * 另一个函数可能更新库存总数。
    * 另一个函数可能将某些产品的客户信息发送到营销部门，该部门将向它们发送促销邮件程序。 

    任何部门通过侦听更改源都可以创建 Azure Cosmos DB 触发器，并可确保它们在此过程中不会延迟关键的订单处理事件。

在所有这些用例中，由于函数本身已分离应用，所以无需一直启动新应用实例。 相反，Azure Functions 会在需要时启动个别函数以完成离散进程。

## <a name="tooling"></a>工具

在 Azure 门户中可以本机集成 Azure Cosmos DB 和 Azure Functions。
* 在 Azure Functions 门户中，可以创建 Azure Cosmos DB 触发器。 有关快速入门说明，请参阅[在 Azure 门户中创建 Azure Cosmos DB 触发器](https://aka.ms/cosmosdbtriggerportalfunc)和![在 Azure Functions 门户中创建 Azure Cosmos DB 触发器](./media/serverless-computing-database/azure-function-cosmos-db-trigger.png) 
* 在 Azure Functions 门户中，还可以将 Azure Cosmos DB 输入绑定和输出绑定添加到其他类型的触发器。 有关快速入门说明，请参阅[使用 Azure Functions 和 Cosmos DB 存储非结构化数据](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)。
    ![在 Azure Functions 门户中创建 Azure Cosmos DB 触发器](./media/serverless-computing-database/function-portal-input-binding.png)
*   在 Azure Cosmos DB 门户中，可以将 Azure Cosmos DB 触发器添加到同一资源组中的现有 Azure Function 应用。
    ![在 Azure Functions 门户中创建 Azure Cosmos DB 触发器](./media/serverless-computing-database/cosmos-db-portal.png)

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>为什么为无服务器计算选择 Azure Functions 集成？

Azure Functions 提供创建可扩展工作单元的功能，或者提供按需运行的简洁逻辑部分，无需预配或管理基础结构。 通过使用 Azure Functions，无需创建全面应用来响应 Azure Cosmos DB 数据库中的更改，即可为特定任务创建可重复使用的较小函数。 此外，还可以将 Azure Cosmos DB 数据用作 Azure Function 的输入或输出，以响应 HTTP 请求或定时触发器等事件。

出于以下原因，建议对无服务器计算体系结构使用 Azure Cosmos DB 数据库：

* **即时访问所有数据**：可以粒度访问每个存储的值，因为 Azure Cosmos DB 在默认情况下会[自动建立所有数据的索引](indexing-policies.md)，并使这些索引立即可用。 这意味着可以不断查询和更新新项目，并将它们添加到数据库，还可以通过 Azure Functions 即时访问这些项目。

* **无架构**。 Azure Cosmos DB 没有架构，因此只有它可以处理来自 Azure Function 的任何数据输出。 这种“处理任何数据”的方法可以直接创建全部输出到 Azure Cosmos DB 的各种 Functions。

* **可缩放的吞吐量**。 在 Azure Cosmos DB 中，吞吐量可以立即增加和减少。 如果在同一集合中拥有数百或数千个 Functions 查询和写入，可以增加 [RU/s](request-units.md) 以处理负载。 通过使用分配的 RU/s，所有函数都可并行运行，并且可保证数据[一致](consistency-levels.md)。

* **全局复制**。 可以[全局](distribute-data-globally.md)复制 Azure Cosmos DB 数据以减少延迟，因为这样可以使数据在地理位置上最靠近用户。 对于全部 Azure Cosmos DB 查询，事件驱动的触发器的数据从最靠近用户的 Azure Cosmos DB 读取。

如果要寻求与 Azure Functions 集成以存储数据并且无需深层索引，或者如果需要存储附件和媒体文件，[Azure Blob 存储触发器](../azure-functions/functions-bindings-storage-blob.md)可能是一个更好的选择。

Azure Functions 的优点： 

* **事件驱动**。 Azure Functions 受事件驱动，并且可以侦听 Azure Cosmos DB 的更改源。 这意味着无需创建侦听逻辑，只关注所侦听内容的更改即可。 

* **无限制**。 函数均并行执行，并且可根据需要启动任意数量的服务。 设置参数。

* **适用于快捷任务**。 每当事件触发时，服务都会触发函数的新实例，并在函数完成后立即关闭它们。 只需支付函数运行时间的费用。

如果不确定流、逻辑应用、Azure Functions 或 WebJobs 是否最适用于你的实现，请参阅[在流、逻辑应用、Functions 和 WebJobs 之间进行选择](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)。

## <a name="next-steps"></a>后续步骤

现在让我们真正连接 Azure Cosmos DB 和 Azure Functions： 

* [在 Azure 门户中创建 Azure Cosmos DB 触发器](https://aka.ms/cosmosdbtriggerportalfunc)
* [使用 Azure Cosmos DB 输入绑定创建 Azure Functions HTTP 触发器](https://aka.ms/cosmosdbinputbind)
* [使用 Azure Functions 和 Cosmos DB 存储非结构化数据](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)
* [Azure Cosmos DB 绑定和触发器](../azure-functions/functions-bindings-documentdb.md)


 



