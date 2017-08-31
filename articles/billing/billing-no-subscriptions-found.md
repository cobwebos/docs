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
ms.topic: article
ms.date: 08/21/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: a4ce9b219c05f8469379c2aac5241fcfffd16033
ms.contentlocale: zh-cn
ms.lasthandoff: 08/24/2017

---

# <a name="no-subscriptions-found-error-in-azure-portal-or-azure-account-center"></a>Azure 门户或 Azure 帐户中心的“找不到任何订阅”错误
尝试登录 [Azure 门户](https://portal.azure.com/)或 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)时，可能会出现“找不到任何订阅”错误消息。 本文提供针对此问题的解决方案。

## <a name="symptom"></a>症状

尝试登录 [Azure 门户](https://portal.azure.com/)或 [Azure 帐户中心](https://account.windowsazure.com/Subscriptions)时，出现以下错误消息：“找不到任何订阅”。

## <a name="cause"></a>原因

如果帐户没有足够的权限，会出现此问题。 

## <a name="solution"></a>解决方案

请确保以正确的管理员身份登录。 帐户管理员只能访问帐户中心。 服务管理员 (SA) 和共同管理员 (CA) 则只能访问 Azure 门户或 Azure 经典门户。

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>场景 1：[Azure 门户](https://portal.azure.com)收到错误消息

解决此问题：

* 通过单击右上角的帐户确保已选择正确的 Azure 目录。

  ![选择 Azure 门户右上角的目录](./media/billing-no-subscriptions-found/directory-switch.png)

* 如果已选择正确的 Azure 目录，但仍收到错误消息，[请将帐户添加为所有者](billing-add-change-azure-subscription-administrator.md)。

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>场景 2：[Azure 帐户中心](https://account.windowsazure.com/Subscriptions)收到错误消息

请检查使用的帐户是否是帐户管理员。 要验证谁是帐户管理员，请执行下列步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在“中心”菜单上，选择“订阅”。
3. 选择要检查的订阅，并选择“设置”。
4. 选择“属性”。 订阅的帐户管理员会显示在“帐户管理员”框中。

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。
如果仍需帮助，请[联系支持人员](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)以快速解决问题。 

