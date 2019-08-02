---
title: 自动续订 Azure 保留
description: 了解如何自动续订 Azure 保留以继续获得预订折扣。
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: banders
ms.openlocfilehash: c19c6af68bcde753ec9bed990e08aa81eabdd37d
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679453"
---
# <a name="automatically-renew-reservations"></a>自动续订预订

你可以续订预订以便在现有保留过期时自动购买替换项。 自动续订提供了一种简单的方法来继续获得预订折扣。 它还使您无需密切监视预订的过期时间。 通过自动续订, 无需手动续订便可避免节省的收益。 续订设置默认情况下处于关闭状态。 随时启用或禁用续订设置, 直到现有预订过期。

续订预订将在现有预订过期时创建新预订。 它不会扩展现有预订的期限。

随时选择自动续订。 续订价格在现有预订过期前30天可用。 如果在保留过期前30天内启用续订, 则会向你发送一封电子邮件, 其中详细介绍了30天后过期。 保留价格可能会在你锁定续订价格和续订时间之间发生变化。 如果是这样, 则您的续订成本是这两个成本中的较低者。 您可以对预订数量进行更改。 如果这样做, 续订会更新, 以便在数量发生变化时使用市场价格设置。

无任何义务需要续订, 你可以随时选择退出现有保留期之前的续订。

## <a name="set-up-renewal"></a>设置续订

请参阅 Azure 门户 >**预订**。

1. 选择该预订。
2. 单击 "**续订**"。
3. 选择 "**到期后自动购买新预订**"。  
  ![示例显示保留续订](./media/billing-reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>如果不续订

你的服务将继续正常运行。 在保留期到期后, 你将按即用即付费率对你的使用情况收费。

## <a name="required-renewal-permissions"></a>必需的续订权限

续订预订需要满足以下条件:

- 您必须是现有预订的所有者。
- 如果预订的作用域为单个订阅或资源组, 则你必须是订阅的所有者。
- 如果订阅具有共享作用域, 则必须是该订阅的所有者。

## <a name="default-renewal-settings"></a>默认续订设置

默认情况下, 续订继承过期预订的所有属性。 预订续订购买具有相同的 SKU、区域、范围、计费订阅、条款和数量。

不过, 你可以更新续订预订购买数量, 以优化你的节省量。

## <a name="when-the-new-reservation-is-purchased"></a>购买新预订时

当现有保留过期时, 将购买新的保留。 我们尝试防止两个保留之间的任何延迟。 连续性确保你的成本可预测, 并可继续获取折扣。

## <a name="changing-parent-reservation-after-setting-renewal"></a>设置续订后更改父保留

如果对过期预订进行了以下更改, 则取消保留续订:

- 拆分
- 合并
- 将预订从一个帐户转移到另一个帐户
- 将预订从 WebDirect 订阅传输到企业协议 (EA) 订阅或任何其他购买方法
- 续订注册

续订期间, 新的保留项会从过期保留继承范围和实例大小的灵活性设置。

## <a name="new-reservation-permissions"></a>新的预订权限

Azure 会将到期保留的权限复制到新的保留。 此外, 预订购买的订阅帐户管理员有权访问新预订。

## <a name="potential-renewal-problems"></a>潜在续订问题

Azure 可能不会在以下情况中处理续订:

- 无法收集付款
- 续订期间出现系统错误
- 续订期间过期 SKU 未处于活动状态
- EA 将续订到不同的 EA

如果出现上述任何一种情况且续订被停用, 则会收到电子邮件通知。

## <a name="renewal-notification"></a>续订通知

电子邮件根据购买方法发送给不同人员:

- EA 客户-将电子邮件发送到在 EA 门户上设置的通知联系人。
- 采用即用即付费率的各个订阅客户-电子邮件将发送到设置为帐户管理员的用户。
- 云解决方案提供商客户-向合作伙伴通知联系人发送电子邮件。

## <a name="next-steps"></a>后续步骤
- 若要详细了解 Azure 保留, 请参阅[什么是 Azure 保留？](billing-save-compute-costs-reservations.md)
