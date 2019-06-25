---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 853bd32cf3ea97571929d54fb7d3ba04bde16b81
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172819"
---
在开始此配置之前，必须登录到 Azure 帐户。 该 cmdlet 会提示提供 Azure 帐户的登录凭据。 登录后，它会下载帐户设置，供 Azure PowerShell 使用。 有关详细信息，请参阅[将 Windows PowerShell 与 Resource Manager 配合使用](../articles/powershell-azure-resource-manager.md)。

若要登录，请使用提升的权限打开 PowerShell 控制台，并连接到帐户。 使用下面的示例来帮助连接：

```powershell
Connect-AzAccount
```

如果有多个 Azure 订阅，请查看该帐户的订阅。

```powershell
Get-AzSubscription
```

指定要使用的订阅。

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
