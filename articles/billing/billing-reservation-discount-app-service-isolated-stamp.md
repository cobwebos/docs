---
title: 如何将预订折扣应用于 Azure App Service 隔离的 Stamp
description: 了解如何将预订折扣应用于 Azure App Service 隔离的戳记。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: cb4b371deadb1ae9e6ae048c3157809aff857c9d
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68298251"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>如何将预订折扣应用于 Azure App Service 隔离的 Stamp

购买独立应用服务戳记费预留容量后, 预订折扣将自动应用于区域中的戳记。 预订折扣适用于由单独的戳记费用计量器发出的使用情况。 辅助角色、附加前端以及与该戳记关联的任何其他资源将继续按固定费率计费。

## <a name="reservation-discount-application"></a>预订折扣应用程序

独立应用服务邮票费折扣适用于每小时运行隔离的戳记。 如果在一小时内未部署 stamp, 则会在该小时内浪费预留容量。 它不会执行。

购买后, 您购买的预订将与在特定区域中运行的独立戳记匹配。 如果关闭该戳记, 预订折扣将自动应用于在该区域中运行的任何其他 stamp。 如果不存在任何 stamp, 则将保留应用于在该区域中创建的下一个 stamp。

如果在一小时内未运行 stamp, 预订将自动应用于同一时间段内的其他匹配的 stamp。

## <a name="choose-a-stamp-type---windows-or-linux"></a>选择戳记类型-Windows 或 Linux

默认情况下, 空的独立标记会发出 Windows 戳记计量器。 例如, 当没有部署辅助角色时。 部署 Windows 辅助角色时, 它会继续发出计量。 如果部署 Linux 辅助角色, 则计量器将更改为 Linux 戳记计量器。 在部署 Linux 和 Windows 辅助角色时, 戳记会发出 Windows 计量器。

因此, 在戳记的整个生命周期内, 戳记会在 Windows 和 Linux 之间发生变化。 同时, 预订是特定于操作系统的。 需要购买支持计划部署到戳记的工作人员的预订。 仅限 windows 的 stamp 和混合戳记使用 Windows 保留。 只有 Linux 工作人员的 stamp 使用 Linux 保留。

仅当你计划_仅_在 stamp 中安装 linux 辅助角色时, 你才应该购买 linux 预订。

## <a name="discount-examples"></a>折扣示例

下面的示例演示如何根据部署, 应用独立的戳记费用保留实例折扣。

- **示例 1**:你在不带独立应用服务戳记的区域中购买单独的保留戳记容量的一个实例。 将新的 stamp 部署到区域并支付该戳记的保留费率。
- **示例 2**：您在已部署了独立应用服务戳记的区域中购买一个独立的保留戳记容量实例。 开始接收已部署的 stamp 的保留速率。
- **示例 3**:你在已部署了独立应用服务戳记的区域中购买一个独立的保留戳记容量实例。 开始接收部署的戳记上的保留速率。 稍后, 您将删除该戳记并部署一个新的戳记。 您将收到新 stamp 的保留费率。 没有已部署的 stamp, 折扣不会接管。
- **示例 4**:在某个区域中购买一个独立 Linux 保留戳记容量的实例, 然后将新的 Stamp 部署到该区域。 如果标记最初是在没有辅助角色的情况下部署的, 则会发出 Windows 戳记计量器。 未收到任何折扣。 在部署第一个 Linux 辅助角色时, 它会发出 Linux 戳记计量器, 并应用保留折扣。 如果稍后将 windows 辅助角色部署到戳记, 则戳记会恢复为 Windows。 你不再接收到隔离 Linux 保留戳记保留项的折扣。

## <a name="next-steps"></a>后续步骤

- 若要了解如何管理预留，请参阅[管理 Azure 预留](billing-manage-reserved-vm-instance.md)。
- 若要详细了解预购买独立应用服务戳记保留容量以节省资金, 请参阅支付[预留容量 Azure App Service 隔离的戳记](billing-prepay-app-service-isolated-stamp.md)。
- 若要了解有关 Azure 预订的详细信息，请参阅以下文章：
  - [什么是 Azure 预订？](billing-save-compute-costs-reservations.md)
  - [管理 Azure 中的预留](billing-manage-reserved-vm-instance.md)
  - [使用即用即付费率了解订阅的保留使用情况](billing-understand-reserved-instance-usage.md)
  - [了解企业合约的预留使用情况](billing-understand-reserved-instance-usage-ea.md)
