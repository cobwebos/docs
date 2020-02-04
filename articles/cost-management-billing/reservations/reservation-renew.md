---
title: 自动续订 Azure 预留
description: 了解如何自动续订 Azure 预留以继续获取预留折扣。
services: billing
author: bandersmsft
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: dfff02c554c94daf1dd7fd844cea461fd76589d8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "75995710"
---
# <a name="automatically-renew-reservations"></a>自动续订预留

可以续订预留，以便在现有预留到期时自动购买替换项。 可以通过自动续订轻松地继续获取预留折扣。 另外，你不必密切监视预留的到期日期。 使用自动续订，你不必手动续订，防止节省权益丢失。 默认情况下，续订设置处于关闭状态。 在现有预留过期之前，可以随时启用或禁用续订设置。

当现有预留过期后，续订预留会创建新的预留， 而不会延长现有预留的期限。

选择加入即可随时自动续订。 续订价格在现有预留过期之前 30 天提供。 如果在预留过期之前 30 多天启用续订，系统会在预留过期之前 30 天向你发送一封电子邮件，详述续订成本。 在锁定续订价格后的续订期间，预留价格可能会变化。 如果出现这种情况，续订成本以二者中较低的为准。 可以更改预留数量。 如果你这样做，系统会对续订进行更新，使用在更改数量时设置的市场内价格。

你没有义务进行续订，可以在现有预留过期之前随时选择退出续订。

## <a name="set-up-renewal"></a>设置续订

转到 Azure 门户 >“预留”。 

1. 选择该预订。
2. 单击“续订”。 
3. 选择“在过期后自动购买新预留”  。  
  ![显示预留续订的示例](./media/reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>如果不续订

服务会继续正常运行。 预留过期后系统会按即用即付费率收取使用费。

## <a name="required-renewal-permissions"></a>所需续订权限

若要续订预留，以下条件是必需的：

- 你必须是现有预留的所有者。
- 如果预留局限于单个订阅或资源组，你必须是订阅的所有者。
- 如果订阅具有共享范围，你必须是订阅的所有者。

## <a name="default-renewal-settings"></a>默认续订设置

默认情况下，续订继承即将过期的预留的所有属性。 预留续订购买具有相同的 SKU、区域、范围、计费订阅、期限和数量。

但是，你可以更新续订预留购买数量，以便优化节省量。

## <a name="when-the-new-reservation-is-purchased"></a>购买新预留时

当现有预留过期后，购买新预留。 我们会尝试避免这两个预留之间出现任何延迟。 持续性可确保你的成本可以预测，并且你可以继续获得折扣。

## <a name="changing-parent-reservation-after-setting-renewal"></a>在设置续订后更改父预留

如果对要过期的预留进行下述任何更改，则会取消预留续订：

- 拆分
- 合并
- 将预留从一个帐户转移到另一个帐户
- 将预留从 WebDirect 订阅转移到企业协议 (EA) 订阅，或任何其他的购买方式
- 续订注册

在续订期间，新的预留从要过期的预留继承范围和实例大小灵活性设置。

## <a name="new-reservation-permissions"></a>新预留权限

Azure 将权限从要过期的预留复制到新预留。 另外，预留购买的订阅帐户管理员可以访问新预留。

## <a name="potential-renewal-problems"></a>潜在的续订问题

如果出现以下情况，Azure 可能不处理续订：

- 无法收集付款
- 在续订期间出现系统错误
- 在续订期间，要过期的 SKU 未处于活动状态
- EA 续订到其他 EA 中

如果出现上述任意条件，续订停用，你会收到电子邮件通知。

## <a name="renewal-notification"></a>续订通知

电子邮件会发送给不同的人员，具体取决于购买方式：

- EA 客户 - 电子邮件发送给在 EA 门户中设置的通知联系人。
- 使用即用即付费率的各个订阅客户 - 电子邮件发送给被设置为帐户管理员的用户。
- 云解决方案提供商客户 - 电子邮件发送给合作伙伴通知联系人。

## <a name="next-steps"></a>后续步骤
- 若要详细了解 Azure 预留，请参阅[什么是 Azure 保留？](save-compute-costs-reservations.md)
