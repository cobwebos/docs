---
title: 预留折扣如何应用于 Azure SQL 数据仓库 | Microsoft Docs
description: 了解预留折扣如何应用于 Azure SQL 数据仓库可帮助你节省资金。
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: banders
ms.openlocfilehash: 24a673dc7949d5ce05aa1a701cc8b939a766fd99
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313920"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>预留折扣如何应用于 Azure SQL 数据仓库

购买 Azure SQL 数据仓库保留容量后，预留折扣将自动应用到该区域中存在的数据仓库。 预留折扣适用于 SQL 数据仓库 cDWU 计量器发出的用量。 存储和网络按照即用即付费率进行收费。

## <a name="reservation-discount-application"></a>预留折扣应用

SQL 数据仓库保留容量折扣按小时应用于正在运行的仓库。 如果在一个小时内没有部署仓库，则该小时的保留容量将浪费。 它不会结转。

购买后，你购买的预留将与任何时间点运行仓库所发出的 SQL 数据仓库使用量相匹配。 如果关闭了某些仓库，则预留折扣将自动应用于任何其他匹配仓库。

对于未运行整一小时的仓库，预留将自动应用于该小时内的其他匹配实例。

## <a name="discount-examples"></a>折扣示例

以下示例演示了如何根据部署应用 SQL 数据仓库保留容量折扣。

- **示例 1**：购买5个单位的 100 cDWU 保留容量。 运行 DW1500c SQL 数据仓库实例一小时。 在这种情况下，将针对 15 个单位的 100 cDWU 使用量发出用量。 预留折扣适用于所使用的 5 个单位。 对于所使用的其余 10 个单位的 100 cDWU 使用量，将按即用即付费率向你收费。 换句话说，多个预订可以进行部分覆盖。

- **示例 2**：购买5个单位的 100 cDWU 保留容量。 运行两个 DW100c SQL 数据仓库实例一小时。 在这种情况下，对于 1 个单位的 100 cDWU 使用量将发出两个使用事件。 这两个使用事件都将获得保留容量折扣。 剩余的 3 个单位的 100 cDWU 保留容量将浪费，并且无法结转以供将来使用。 换句话说，单个预订可以与多个 SQL 数据仓库实例匹配。

- **示例 3**：购买1个单元的 100 cDWU 预留容量。 运行两个 DW100c SQL 数据仓库实例。 每个实例运行 30 分钟。 在这种情况下，这两个使用事件都将获得保留容量折扣。 按照即用即付费率不收取使用费。

## <a name="need-help-contact-us"></a>需要帮助？ 联系我们

- 如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤

若要了解有关 Azure 预订的详细信息，请参阅以下文章：

- [什么是 Azure 预订？](save-compute-costs-reservations.md)
- [查看预留交易](view-reservations.md)
- [通过 API 获取预留交易和利用率](reservation-apis.md)
- [管理预留](manage-reserved-vm-instance.md)
