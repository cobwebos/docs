---
title: 在 autopilot 模式下创建 Azure Cosmos 容器和数据库。
description: 了解在 autopilot 模式下预配 Azure Cosmos 数据库和容器的优势、用例和方法。
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246651"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>在 autopilot 模式下创建 Azure Cosmos 容器和数据库（预览版）

Azure Cosmos DB 使你可以在手动或 Autopilot 模式下预配容器上的吞吐量。 本文介绍了 Autopilot 模式的优势和用例。

> [!NOTE]
> Autopilot 模式目前以公共预览版提供。 只能[为新的数据库和容器启用 autopilot](#create-a-database-or-a-container-with-autopilot-mode)。 它对于现有容器和数据库不可用。

除了手动预配吞吐量以外，现在还可以在 autopilot 模式下配置 Azure Cosmos 容器。 在自动驾驶模式下配置的容器和数据库将根据**应用程序需求自动立即扩展预配吞吐量，而不会影响工作负载的可用性、延迟、吞吐量或性能。**

在 autopilot 模式下配置容器和数据库时，需要指定不可超过的最大吞吐量 `Tmax`。 然后，容器可以缩放其吞吐量，以便使 `0.1*Tmax < T < Tmax`。 换而言之，容器和数据库可根据工作负荷的需求，在从配置的最大吞吐量值的 10% 到配置的最大吞吐量值范围内即时缩放。 随时可以更改 autopilot 数据库或容器上的最大吞吐量 (`Tmax`) 设置。 使用 autopilot 选项时，每个容器或数据库 400 RU/秒最小吞吐量的限制将不再适用。

在 autopilot 预览版中，在容器或数据库的指定最大吞吐量方面，系统允许在计算的存储限制范围内操作。 如果超过存储限制，则最大吞吐量会自动调整为更大的值。 在 autopilot 模式下使用数据库级吞吐量时，数据库中允许的容器数的计算方式如下：`0.001*TMax`。 例如，如果预配 20,000 autopilot RU/秒，则数据库可以包含 20 个容器。

## <a name="benefits-of-autopilot-mode"></a>Autopilot 模式的优势

在 autopilot 模式下配置的 Azure Cosmos 容器具有以下优势：

* **简单：** 自动引导模式下的容器消除了手动管理各种容器的预配吞吐量 （R） 和容量的复杂性。

* **可扩展：** 处于自动驾驶模式下的容器根据需要无缝扩展预配的吞吐能力。 客户端连接和应用程序不会中断，且不影响任何现有的 SLA。

* **经济高效：** 使用在自动驾驶模式下配置的容器时，只需按小时支付工作负载所需的资源。

* **高度可用：** 自动驾驶模式下的容器使用相同的全局分布、容错、高度可用的后端，以确保数据的持久性和高可用性。

## <a name="use-cases-of-autopilot-mode"></a>Autopilot 模式的用例

在 autopilot 模式下配置的 Azure Cosmos 容器的用例包括：

* **可变工作负载：** 当您运行一个使用时间轻巧的应用程序时，每天使用时间为 1 小时至几个小时，每天几次或每年多次。 示例包括人力资源、预算和运营报告应用程序。 对于这种情况，可以使用在 autopilot 模式下配置的容器，而不再需要手动根据峰值或平均容量进行预配。

* **不可预测的工作负载：** 当您运行的工作负载时，其中全天都有数据库使用情况，但也运行难以预测的活动高峰。 示例包括当天气预报发生变化时出现活动浪涌的交通信息网站。 在 autopilot 模式下配置的容器会调整容量以满足应用程序峰值负载的需求，并在活动浪涌结束时缩减容量。

* **新应用程序：** 如果要部署新应用程序，并且不确定需要多少预配吞吐量（即有多少个 R。 如果在 autopilot 模式下配置容器，可以根据应用程序的容量需求和要求自动缩放。

* **不常使用的应用程序：** 如果您的应用程序每天、每周或每月只使用几个小时，例如低容量应用程序/Web/博客网站。

* **开发和测试数据库：** 如果您让开发人员在工作时间使用容器，但在夜间或周末不需要容器。 如果在 autopilot 模式下配置容器，它们可以在不使用时缩减为最低的配置。

* **计划生产工作负载/查询：** 当您在单个容器上有一系列计划的请求/操作/查询，并且如果存在要以绝对低吞吐量运行的空闲期间，现在可以轻松执行此操作。 将计划的查询/请求提交到在 autopilot 模式下配置的容器后，它会根据最大需求自动扩展，并运行操作。

以往在解决问题时不仅需要投入大量的时间来进行实施，而且还会在配置或代码方面带来复杂性，此外往往需要人工干预才能解决问题。 Autopilot 模式现成地启用上述方案，使你不再需要担心这些问题。

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>比较 - 在手动模式与 autopilot 模式下配置的容器

|  | 在手动模式下配置的容器  | 在 autopilot 模式下配置的容器 |
|---------|---------|---------|
| **预配吞吐量** | 手动预配。 | 根据工作负荷使用模式自动即时缩放。 |
| **请求/操作的速率限制 (429)**  | 如果消耗量超过预配的容量，则可能会发生。 | 如果消耗的吞吐量处于在 autopilot 模式下选择的最大吞吐量范围内，则不会发生这种情况。   |
| **容量规划** |  必须对所需的吞吐量进行初始容量规划和预配。 |    无需考虑容量规划。 系统会自动处理容量规划和容量管理。 |
| **定价** | 根据手动预配的 RU/秒吞吐量按小时计费。 | 对于单写入区域帐户，需按小时费率为每小时使用的 autopilot RU/秒吞吐量付费。 <br/><br/>对于包含多个写入区域的帐户，autopilot 不收取额外的费用。 需按小时费率为每小时使用的相同多主机 RU/秒吞吐量付费。 |
| **最适合工作负荷类型** |  可预测的稳定工作负荷|   不可预测的可变工作负荷  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>使用 autopilot 模式创建数据库或容器

可以在通过 Azure 门户创建新的数据库或容器时为其配置 autopilot。 使用以下步骤来创建新的数据库或容器、启用 autopilot 并指定最大吞吐量 (RU/s)。

1. 登录到 Azure[门户](https://portal.azure.com)或[Azure 宇宙数据库资源管理器。](https://cosmos.azure.com/)

1. 导航到你的 Azure Cosmos DB 帐户，打开“数据资源管理器”选项卡。****

1. 选择 **"新容器"。** 为数据库、容器和分区键输入一个名称。 在“吞吐量”下，选择“Autopilot”选项，然后选择在使用 autopilot 选项时数据库或容器不能超过的最大吞吐量 (RU/s)。********

   ![创建容器并配置 Autopilot 吞吐量](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. 选择“确定”。

可以通过选择“预配数据库吞吐量”**** 选项来创建采用 autopilot 模式的共享吞吐量数据库。

## <a name="throughput-and-storage-limits-for-autopilot"></a><a id="autopilot-limits"></a>自动驾驶仪的吞吐量和存储限制

下表显示了 autopilot 模式下不同选项的最大吞吐量和存储限制：

|最大吞吐量限制  |最大存储限制  |
|---------|---------|
|4000 RU/秒  |   50 GB    |
|20,000 RU/秒  |  200 GB  |
|100,000 RU/秒    |  1 TB   |
|500,000 RU/秒    |  5 TB  |

## <a name="next-steps"></a>后续步骤

* 查看 [autopilot 常见问题解答](autopilot-faq.md)。
* 了解有关[逻辑分区](partition-data.md)的更多详细信息。
* 了解[如何对 Azure Cosmos 容器预配吞吐量](how-to-provision-container-throughput.md)。
* 了解[如何对 Azure Cosmos 数据库预配吞吐量](how-to-provision-database-throughput.md)。
