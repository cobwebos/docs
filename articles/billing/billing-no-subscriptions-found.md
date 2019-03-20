---
title: “找不到任何订阅”错误 – Azure 门户注册 | Microsoft Docs
description: 提供针对登录 Azure 门户或 Azure 帐户中心时出现“找不到任何订阅”错误这一问题的解决方案。
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 094d79026a55e651a1f67a5047dff20c769c359a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57881106"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>在 Azure 门户或 Azure 帐户中心注册时出现“找不到任何订阅”错误

尝试登录 [Azure 门户](https://portal.azure.com/)或 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)时，可能会出现“找不到任何订阅”错误消息。 本文提供针对此问题的解决方案。

## <a name="symptom"></a>症状

尝试登录 [Azure 门户](https://portal.azure.com/)或 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)时，出现以下错误消息：“找不到任何订阅”。

## <a name="cause"></a>原因

如果选择了错误的目录，或者帐户没有足够的权限，会出现此问题。 

## <a name="solution"></a>解决方案

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>方案 1：[Azure 门户](https://portal.azure.com)收到错误消息

解决此问题：

* 通过单击右上角的帐户确保已选择正确的 Azure 目录。

  ![选择 Azure 门户右上角的目录](./media/billing-no-subscriptions-found/directory-switch.png)
* 如果已选择正确的 Azure 目录，但仍收到错误消息，[请将所有者角色分配给帐户](../role-based-access-control/role-assignments-portal.md)。

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>方案 2：[Azure 帐户中心](https://account.windowsazure.com/Subscriptions)收到错误消息

请检查使用的帐户是否是帐户管理员。 要验证谁是帐户管理员，请执行下列步骤：

1. 登录到 [Azure 门户中的订阅视图](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)。
1. 选择要检查的订阅，并关注“设置”下的信息。
1. 选择“属性”。 订阅的帐户管理员会显示在“帐户管理员”框中。  

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如果有疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。
