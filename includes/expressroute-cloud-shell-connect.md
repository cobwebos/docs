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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "67172683"
---
 如果你使用 Azure Cloud Shell，则可在单击“试用”后自动登录到你的 Azure 帐户。 若要在本地登录，请使用提升的权限打开 PowerShell 控制台，并运行 cmdlet 以进行连接。

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