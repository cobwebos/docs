---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fee97b29f24d8bb4f50a2929c3ceb33af85a5e21
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852348"
---
使用提升的权限打开 PowerShell 控制台。



如果要在本地运行 Azure PowerShell，请连接到 Azure 帐户。 *Connect-AzureRmAccount* cmdlet 会提示你输入凭据。 进行身份验证后，它会下载帐户设置，以便 Azure PowerShell 可以使用这些设置。 如果未在本地运行 PowerShell，而是在浏览器中使用 Azure Cloud Shell“试用”，请跳过此第一步。 你将自动连接到 Azure 帐户。

```azurepowershell
Connect-AzureRmAccount
```

如果有多个订阅，请获取 Azure 订阅的列表。

```azurepowershell-interactive
Get-AzureRmSubscription
```

指定要使用的订阅。

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```