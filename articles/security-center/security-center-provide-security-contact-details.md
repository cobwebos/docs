---
title: 在 Azure 安全中心提供安全联系人详细信息 | Microsoft 文档
description: 本文档演示如何在 Azure 安全中心中提供安全联系人详细信息。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: aa35d1325e339af515c8bfc052d1af524b464e09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91446010"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>设置安全警报的电子邮件通知 

为确保组织中的相应人员收到有关环境中的安全警报的通知，请在“电子邮件通知”设置页面中输入其电子邮件地址。

设置通知时，可以配置将电子邮件发送给具有特定 Azure 订阅角色的特定个人或任何人。 

为了避免警报疲劳，安全中心限制了外发邮件的数量。 对于每个订阅，安全中心都会发送以下数量的电子邮件：

- 每天最多四封电子邮件（适用于“高严重性”警报） 
- 每天最多两封电子邮件（适用于“中等严重性”警报） 
- 每天最多一封电子邮件（适用于“低严重性”警报） 


:::image type="content" source="./media/security-center-provide-security-contacts/email-notification-settings.png" alt-text="配置将接收有关安全警报的电子邮件的联系人的详细信息。" :::

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布 (GA)|
|定价：|免费|
|所需角色和权限：|**安全管理员**<br>**订阅所有者** |
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![是](./media/icons/yes-icon.png) US Gov（部分）<br>![否](./media/icons/no-icon.png) China Gov，其他 Gov|
|||


## <a name="set-up-email-notifications-for-alerts"></a>为警报设置电子邮件通知 <a name="email"></a>

可以将电子邮件通知发送给具有特定 Azure 角色的个人或所有用户。

1. 在安全中心的“定价和设置”区域中，找到相关的订阅，然后选择“电子邮件通知” 。

1. 定义通知的收件人：

    - 从下拉列表中，选择某个可用角色。
    - 和/或输入特定电子邮件地址（用逗号分隔）。 可输入的电子邮件地址数量无限制。

1. 若要将安全联系人信息应用到订阅，请选择“保存”。


## <a name="see-also"></a>另请参阅
若要详细了解安全警报，请参阅以下文章：

* [安全警报 - 参考指南](alerts-reference.md) - 了解可能会在 Azure 安全中心的威胁防护模块中看到的安全警报
* [在 Azure 安全中心中管理和响应安全警报](security-center-managing-and-responding-alerts.md) - 了解如何管理和响应安全警报
* [工作流自动化](workflow-automation.md) - 通过自定义通知逻辑自动响应警报