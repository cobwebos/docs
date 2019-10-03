---
title: 预留折扣如何应用于 Azure 应用服务独立印花
description: 了解预留折扣如何应用于 Azure 应用服务独立印花。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 759f83001353957f23b917440b68719b54690bea
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718800"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>预留折扣如何应用于 Azure 应用服务独立印花

购买 Azure 应用服务独立印花费保留容量后，预留折扣将自动应用到某个区域中的印花费。 预留折扣适用于独立印花费计量发出的用量。 辅助角色、其他前端以及任何其他与印花关联的资源继续按常规费率计费。

## <a name="reservation-discount-application"></a>预留折扣应用

应用服务独立印花费折扣按小时应用到正在运行的独立印花。 如果在一个小时内没有部署印花，则该小时的预留容量将浪费。 它不会结转。

购买后，你购买的预留将与在特定区域中运行的独立印花相匹配。 如果关闭该印花，则预留折扣将自动应用于在该区域运行的任何其他印花。 如果不存在印花，预留折扣会应用到在该区域创建的下一个印花。

如果印花未运行一整小时，预留折扣会自动应用到该小时内同一区域中的其他匹配印花。

## <a name="choose-a-stamp-type---windows-or-linux"></a>选择印花类型 - Windows 或 Linux

默认情况下，空的独立印花发出 Windows 印花计量。 例如，在没有部署辅助角色的情况下。 如果部署 Windows 辅助角色，它会继续发出此计量。 如果部署 Linux 辅助角色，则计量更改为 Linux 印花计量。 同时部署 Linux 和 Windows 辅助角色时，印花发出 Windows 记量。

因此，印花计量可以在印花生存期中在 Windows 和 Linux 之间来回变换。 另外，预留特定于操作系统。 需要购买一个支持计划部署到印花的辅助角色的预留。 仅限 Windows 的印花和混合印花使用 Windows 预留。 只包含 Linux 辅助角色的印花使用 Linux 预留。

只购买 Linux 预留的前提是计划只  在印花中部署 Linux 辅助角色。

## <a name="discount-examples"></a>折扣示例

以下示例演示了如何根据部署应用独立印花费预留实例折扣。

- **示例 1**：在没有应用服务独立印花的区域中购买独立预留印花容量的一个实例。 将一个新的印花部署到该区域，为该印花按预留费率付款。
- **示例 2**：在已经部署应用服务独立印花的区域中购买独立预留印花容量的一个实例。 开始收到已部署印花的预留费率。
- **示例 3**：在已经部署应用服务独立印花的区域中购买独立预留印花容量的一个实例。 开始收到已部署印花的预留费率。 稍后，删除该印花并部署新印花。 收到新印花的预留费率。 对于没有部署印花的时段，折扣不会结转。
- **示例 4**：在某个区域中购买独立 Linux 预留印花容量的一个实例，然后向该区域部署新印花。 印花一开始部署时没有辅助角色，它发出 Windows 印花计量。 未收到任何折扣。 当第一个 Linux 辅助角色部署到印花时，它发出 Linux 印花计量，此时会应用预留折扣。 如果随后将 Windows 辅助角色部署到印花，印花计量会恢复为 Windows。 你不再收到独立 Linux 预留印花预留项的折扣。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](billing-manage-reserved-vm-instance.md)。
- 若要详细了解如何预先购买应用服务独立印花预留容量以节省资金，请参阅[通过预留容量预先支付 Azure 应用服务独立印花费](billing-prepay-app-service-isolated-stamp.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](billing-save-compute-costs-reservations.md)
  - [管理 Azure 中的预留](billing-manage-reserved-vm-instance.md)
  - [了解采用即用即付费率的订阅的预留使用情况](billing-understand-reserved-instance-usage.md)
  - [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
