---
title: "尝试登录 Azure 门户或 Azure 帐户中心时出现“找不到任何订阅”错误 | Microsoft Docs"
description: "提供针对登录 Azure 门户或 Azure 帐户中心时出现“找不到任何订阅”错误这一问题的解决方案。"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 08/30/2017
ms.author: genli
ms.openlocfilehash: 19f27aa81d7b5e03828f18f5a38cf3362df17694
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Azure 门户或 Azure 帐户中心的“找不到任何订阅”错误

尝试登录 [Azure 门户](https://portal.azure.com/)或 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)时，可能会出现“找不到任何订阅”错误消息。 本文提供针对此问题的解决方案。

## <a name="symptom"></a>症状

尝试登录 [Azure 门户](https://portal.azure.com/)或 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)时，出现以下错误消息：“找不到任何订阅”。

## <a name="cause"></a>原因

如果选择了错误的目录，或者帐户没有足够的权限，会出现此问题。 

## <a name="solution"></a>解决方案

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>场景 1：[Azure 门户](https://portal.azure.com)收到错误消息

解决此问题：

* 通过单击右上角的帐户确保已选择正确的 Azure 目录。

  ![选择 Azure 门户右上角的目录](./media/billing-no-subscriptions-found/directory-switch.png)
* 如果已选择正确的 Azure 目录，但仍收到错误消息，[请将帐户添加为所有者](billing-add-change-azure-subscription-administrator.md)。

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>场景 2：[Azure 帐户中心](https://account.windowsazure.com/Subscriptions)收到错误消息

请检查使用的帐户是否是帐户管理员。 要验证谁是帐户管理员，请执行下列步骤：

1. 登录到 [Azure 门户中的订阅视图](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 选择要检查的订阅，并关注“设置”下的信息。
1. 选择“属性”。 订阅的帐户管理员会显示在“帐户管理员”框中。  

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果仍需帮助，请[联系支持人员](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)以快速解决问题。 
