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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: 5a2410a5ccaa10867e94b3a2ec10228a006fbe20
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534628"
---
# <a name="set-up-email-notifications-for-security-alerts"></a>为安全警报设置电子邮件通知 

若要确保你的组织中的适当人员收到有关环境中的安全警报的通知，请在 "**电子邮件通知**设置" 页中输入其电子邮件地址。

设置通知时，可以将电子邮件配置为发送给特定个人，或者配置为具有订阅的特定 Azure 角色的任何人。 

为了避免警报疲劳，安全中心会限制传出邮件的数量。 对于每个订阅，安全中心将发送：

- 对于**高严重性**警报，每天最多**四**个电子邮件
- 对于**中等严重性**警报，每天最多发送**两**封电子邮件
- 对于**低严重性**警报，每天最多发送**一**封电子邮件

## <a name="availability"></a>可用性

- 发布状态：**正式发布版**
- 必需的角色：**安全管理员**或**订阅所有者** 
- 云：✔商用云✔ US Gov （partial）✘民族/主权（中国 Gov，其他 Gov）


## <a name="set-up-email-notifications-for-alerts"></a>为警报设置电子邮件通知 <a name="email"></a>

你可以向具有特定 Azure 角色的个人或所有用户发送电子邮件通知。

1. 从安全中心的**定价 & 设置**"区域、相关订阅，并选择"**电子邮件通知**"。

1. 定义通知的收件人：

    - 从下拉列表中，从可用角色中进行选择。
    - 和/或输入用逗号分隔的特定电子邮件地址。 可输入的电子邮件地址数量无限制。

1. 若要将安全联系人信息应用到订阅，请选择“保存”。


## <a name="see-also"></a>另请参阅
若要了解有关安全警报的详细信息，请参阅以下内容：

* [安全警报-参考指南](alerts-reference.md)--了解你可能在 Azure 安全中心的威胁防护模块中看到的安全警报
* [管理和响应 Azure 安全中心的安全警报](security-center-managing-and-responding-alerts.md)-了解如何管理和响应安全警报