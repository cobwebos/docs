---
title: 如何将预订折扣应用于 Azure SQL 数据仓库 |Microsoft Docs
description: 了解如何将预订折扣应用于 Azure SQL 数据仓库，可帮助你的资金节省。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 10e19377d31489cd19465fe6171ffb530bd58c28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60918339"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>如何将预订折扣应用于 Azure SQL 数据仓库

购买 Azure SQL 数据仓库保留容量后，将预订折扣将自动应用到在该区域中存在的数据仓库。 预订折扣适用于由 SQL 数据仓库 cDWU 测定仪发出使用率。 即用即付费率收取存储和网络。

## <a name="reservation-discount-application"></a>预订折扣应用程序

SQL 数据仓库保留的容量折扣应用于每小时运行仓库。 如果没有部署了一小时的仓库会浪费该小时内保留的容量。 它不会延续。

购买，你购买的预订匹配发送在时间中的任意位置运行仓库的 SQL 数据仓库使用情况。 如果关闭某些仓库，然后预订折扣会自动应用于任何其他匹配仓库。

对于不运行整个小时内的仓库，预订是自动应用于其他匹配的实例在该小时内。

## <a name="discount-examples"></a>折扣示例

以下示例显示 SQL 数据仓库保留的容量折扣应用的方式，具体取决于部署。

- **示例 1**:购买 5 个单位的 100 cDWU 保留容量。 一小时运行 DW1500c SQL 数据仓库实例。 在这种情况下，为 15 个单位的 100 cDWU 使用情况发出使用情况。 预订折扣应用于您使用的 5 个单位。 使用剩余的 10 个单位的所用的 100 cDWU 使用即用即付费率收费。

- **示例 2**：购买 5 个单位的 100 cDWU 保留容量。 一小时运行两个 DW100c SQL 数据仓库实例。 在这种情况下，两个使用情况事件发出 1 个单位的 100 cDWU 使用情况。 这两个使用情况事件获取预留的容量的折扣。 剩余的 3 个单位的 100 cDWU 保留容量就会浪费掉，并不会以供将来使用延续。

- **示例 3**:购买 100 cDWU 保留容量的 1 个单位。 运行两个 DW100c SQL 数据仓库实例。 每个运行 30 分钟。 在这种情况下，这两个使用情况事件获取预留的容量的折扣。 任何使用情况使用即用即付费率不收费。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

- 如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](billing-save-compute-costs-reservations.md)
- [查看保留项事务](billing-view-reservations.md)
- [获取保留事务和通过 API 的利用率](billing-reservation-apis.md)
- [管理保留项](billing-manage-reserved-vm-instance.md)
