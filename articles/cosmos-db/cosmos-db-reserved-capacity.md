---
title: Azure 宇宙 DB 中的预留容量以优化成本
description: 了解如何购买 Azure Cosmos DB 预留容量以节省计算成本。
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 16e8f770445218e10ab7e7645a81325d11be55da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505964"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>使用 Azure Cosmos DB 预留容量优化成本

Azure Cosmos DB 预留容量可通过预付为期一年或三年的 Azure Cosmos DB 资源预留费用为你省钱。 使用 Azure Cosmos DB 预留容量，可以获得为 Cosmos DB 资源预配的吞吐量的折扣。 资源示例包括数据库和容器（表、集合和图）。

Azure Cosmos DB 预留容量可以大幅降低 Cosmos DB 成本，相比正常价格最多可以降低 65%，前提是提前承诺使用一年或三年的服务。 预留容量提供一种计费折扣，并且不会影响 Azure Cosmos DB 资源的运行时状态。

Azure Cosmos DB 预留容量涵盖为资源预配的吞吐量的费用。 但它不涵盖存储和网络费用。 购买预留后，不再像即用即付资费一样收取与预留属性相应的吞吐量费用。 预留相关详细信息，请参阅 [Azure 预留](../cost-management-billing/reservations/save-compute-costs-reservations.md)一文。

可从 [Azure 门户](https://portal.azure.com)购买 Azure Cosmos DB 预留容量。 通过[提前付款或按月付款](../cost-management-billing/reservations/monthly-payments-reservations.md)的方式为预留付款。 购买预留容量：

* 您必须在至少一个企业或个人订阅中处于"所有者"角色，该订阅具有即用即付费率。  
* 对于企业订阅，必须在 [EA 门户](https://ea.azure.com)中启用“添加预留实例”****。 或者，如果禁用了该设置，则必须是订阅的 EA 管理员。
* 对于云解决方案提供商 (CSP) 计划，只有管理员代理或销售代理可以购买 Azure Cosmos DB 预留容量。

## <a name="determine-the-required-throughput-before-purchase"></a>在购买之前确定所需吞吐量

预留容量购买的大小应基于现有或即将部署的 Azure Cosmos DB 资源每小时使用的吞吐量总量。 例如：如果这是您一致的每小时使用模式，则购买 30，000 RU/s 预留容量。 在此示例中，任何超过 30，000 RU/s 的预配吞吐量都将使用即用即付费率计费。 如果预配吞吐量在一小时内低于 30，000 RU/s，则该小时的额外预留容量将浪费。

我们根据您的每小时使用模式计算购买建议。 分析了过去 7 天、30 天和 60 天的使用情况，建议购买可最大化您的节省的预留容量。 您可以使用以下步骤在 Azure 门户中查看建议的预留大小：

1. 登录到 Azure[门户](https://portal.azure.com)。  

2. 选择 **"所有服务** > **预订添加** > **Add**"。

3. 在 **"购买预留"** 窗格中，选择**Azure 宇宙数据库**。

4. 选择 **"推荐**"选项卡以查看建议的预订：

您可以按以下属性筛选建议：

- **期限**（1年或3年）
- **计费频率**（每月或预付）
- **吞吐量类型**（RU 与多主机 RU）

此外，您可以将建议范围限定为单个资源组、单个订阅或整个 Azure 注册中。 

下面是一个示例建议：

![预留容量建议](./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png)

购买 30，000 RU/s 预订的建议表明，在 3 年预订中，30，000 RU/s 的预订规模将最大限度地节省。 在这种情况下，建议是根据过去 30 天的 Azure Cosmos DB 使用情况计算的。 如果此客户希望过去 30 天的 Azure Cosmos DB 使用情况代表未来的使用，则通过购买 30，000 RU/s 预留，他们可最大限度地节省成本。

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>购买 Azure Cosmos DB 预留容量

1. 登录到 Azure[门户](https://portal.azure.com)。  

2. 选择 **"所有服务** > **预订添加** > **Add**"。  

3. 在 **"购买预留"** 窗格中，选择**Azure 宇宙 DB**以购买新预留。  

4. 填写必填字段，如下表所示：

   ![填写预留容量表](./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png)

   |字段  |描述  |
   |---------|---------|
   |范围   |   控制有多少订阅可以使用与预留关联的计费权益的选项。 它还控制将预留应用于特定订阅的方式。 <br/><br/>  如果选择“共享”，预留折扣将应用到计费上下文内任何订阅中运行的 Azure Cosmos DB 实例****。 计费上下文基于 Azure 的注册方式。 对于企业客户，共享范围是注册范围，包括注册中的所有订阅。 对于即用即付客户，共享范围是所有由帐户管理员创建的按即用即付费率的单个订阅。  <br/><br/>  如果选择“单个订阅”，预留折扣将应用到所选订阅中的 Azure Cosmos DB 实例****。 <br/><br/> 如果选择 **"单个资源组**"，则预留折扣将应用于所选订阅中的 Azure Cosmos DB 实例以及该订阅中的选定资源组。 <br/><br/> 购买预留容量后，可以更改预留范围。  |
   |订阅  |   用于支付 Azure Cosmos DB 预留容量费用的订阅。 所选订阅的付款方式用于收取费用。 订阅必须是以下类型之一： <br/><br/>  企业协议（优惠编号：MS-AZR-0017P 或 MS-AZR-0148P）：对于企业订阅，费用从注册的货币承诺余额中扣除，或按超额收取。 <br/><br/> 具有即用即付费率的个人订阅（优惠编号：MS-AZR-0003P 或 MS-AZR-0023P）：对于具有即用即付费率的个人订阅，费用由订阅上的信用卡或发票付款方式计费。    |
   | 资源组 | 应用预留容量折扣的资源组。 |
   |术语  |   一年或三年。   |
   |吞吐量类型   |  吞吐量预配为请求单位。 您可以为两个设置购买预配吞吐量的预留 - 单个区域写入以及多个区域写入。 吞吐量类型有两个值可供选择：每小时 100 RU/s 和每小时 100 个多主机 RU/s。|
   | 预留容量单位| 想预留的吞吐量的量。 可以通过确定每个区域的所有 Cosmos DB 资源（例如，数据库或容器）所需的吞吐量来计算此值。 然后，将其乘以您将与 Cosmos 数据库关联的区域数。 例如：如果有五个区域，每个区域 1 百万 RU/秒，则购买预留容量时应选 5 百万 RU/秒。 |


5. 填写表单后，计算购买预留容量所需的价格。 输出还显示所选选项的折扣百分比。 下一步单击**选择**

6. 在 **"购买预订"** 窗格中，查看预订的折扣和价格。 此预留价格适用于使用跨所有区域预配的吞吐量的 Azure Cosmos DB 资源。  

   ![预留容量摘要](./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png)

7. 选择 **"审核 + 立即购买****"，然后立即购买**。 购买成功后，会看到如下页面所示内容：

购买预留后，它立即应用到与预留期相符的所有现有 Azure Cosmos DB 资源。 如果还没有 Azure Cosmos DB 资源，则将在你部署符合预留期的新 Cosmos DB 实例时应用预留。 这两种情况的预留期均于成功购买后立即开始。

预留到期时，Azure Cosmos DB 实例会继续运行，按正常即用即付资费计费。

## <a name="cancel-exchange-or-refund-reservations"></a>对预留执行取消、交换或退款操作

可以在一定的限制下对预留执行取消、交换或退款操作。 有关详细信息，请参阅 [Azure 预留的自助交换和退款](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)。

## <a name="next-steps"></a>后续步骤

预留折扣自动应用于与预留范围和属性相符的 Azure Cosmos DB 资源。 可以通过 Azure 门户、PowerShell、Azure CLI 或 API 更新预留的范围。

*  要了解预留容量折扣是如何应用到 Azure Cosmos DB 的，请参阅[了解 Azure 预留折扣](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)。

* 若要了解有关 Azure 预留的详细信息，请参阅以下文章：

   * [什么是 Azure 预留项？](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [管理 Azure 预留](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [了解企业合约的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [了解即用即付订阅的预留使用情况](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [合作伙伴中心 CSP 计划中的 Azure 预留](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。
