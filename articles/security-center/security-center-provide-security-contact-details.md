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
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: c591dc1efc3b64b0f926e95c14cff681dbcdf957
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385004"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>在 Azure 安全中心提供安全联系人详细信息
Azure 安全中心建议对 Azure 订阅提供安全联系人的详细信息（如果尚未提供）。 如果 Microsoft 安全响应中心 (MSRC) 发现用户的客户数据被某方非法访问或未经授权访问，Microsoft 会使用该信息联系用户。 MSRC 会执行 Azure 网络和基础结构的选择安全监视，并接收来自第三方的威胁情报和恶意投诉。

每日第一个警报发生时会发送电子邮件通知（仅对于高严重级别的警报）。 只能对订阅策略配置电子邮件首选项。 订阅内的资源组将继承这些设置。 警报仅在 Azure 安全中心的"标准层"中可用。

将会发送警报电子邮件通知：
- 每天每种警报类型发送给一个电子邮件收件人  
- 一天之内发送给一个收件人的电子邮件不得超过 3 封
- 每封电子邮件都包含一个警报，而不是警报的聚合
- 仅适用于高严重性警报

> [!TIP]
> 对于具有其他严重性级别的警报，创建[工作流自动化](workflow-automation.md)以使用将电子邮件发送给相关人员的逻辑应用。
 
例如，如果已发送电子邮件消息提醒你有关 RDP 攻击的信息，则即使有其他警报触发，你也不会在同一天收到有关 RDP 攻击的其他电子邮件。 

> [!NOTE]
> 本文档将使用示例部署介绍该服务。  这并非一份循序渐进的指南。

## <a name="set-up-email-notifications-for-alerts"></a>为警报设置电子邮件通知<a name="email"></a>

1. 打开**电子邮件通知**页面：

    1. 对于警报，打开**定价&设置**，选择相关订阅，并选择**电子邮件通知**。

    1. 如果要实施建议，请在 **"建议"** 下选择"**提供安全联系人详细信息**"，选择 Azure 订阅以提供其联系信息。 这将打开“电子邮件通知”****。

   ![提供安全联系人详细信息][2]

   * 输入安全联系人的电子邮件地址或地址，用逗号隔开。 您可以输入的电子邮件地址数量没有限制。
   * 若要接收有关于高严重级别警报的电子邮件，请打开选项“通过电子邮件向我发送警报”****。 对于其他严重性级别，请使用[工作流自动化](workflow-automation.md)中所述的逻辑应用。
   * 您可以向订阅所有者（经典服务管理员和共同管理员，以及订阅范围内的 RBAC 所有者角色）发送电子邮件通知。
   * 要将安全联系信息应用于订阅，请选择"**保存**"。

## <a name="see-also"></a>请参阅
若要了解有关安全中心的详细信息，请参阅以下文章：

* [在 Azure 安全中心设置安全策略](tutorial-security-policy.md)-- 了解如何为 Azure 订阅和资源组配置安全策略。
* [在 Azure 安全中心管理安全建议](security-center-recommendations.md)-- 了解建议如何帮助保护 Azure 资源。
* [Azure 安全中心中的安全运行状况监视](security-center-monitoring.md)-- 了解如何监视 Azure 资源的运行状况。
* [管理和响应 Azure 安全中心中的安全警报](security-center-managing-and-responding-alerts.md)-- 了解如何管理和响应安全警报。
* [使用 Azure 安全中心监视合作伙伴解决方案](security-center-partner-solutions.md)-- 了解如何监视合作伙伴解决方案的运行状况。

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
