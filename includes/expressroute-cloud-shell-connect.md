---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "67172683"
---
 如果使用的是 Azure Cloud Shell，请在单击 "试用" 后自动登录到 Azure 帐户。 若要在本地登录，请用提升的权限打开 PowerShell 控制台，然后运行 cmdlet 进行连接。

```azurepowershell
Connect-AzAccount
```

如果有多个订阅，请获取 Azure 订阅的列表。

```azurepowershell-interactive
Get-AzSubscription
```

指定要使用的订阅。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```