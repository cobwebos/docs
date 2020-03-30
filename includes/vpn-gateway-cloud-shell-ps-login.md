---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77133597"
---
使用提升的权限打开 PowerShell 控制台。

如果要在本地运行 Azure PowerShell，请连接到 Azure 帐户。 Connect-AzureRmAccount cmdlet 会提示输入凭据**。 进行身份验证后，它会下载帐户设置，以便 Azure PowerShell 可以使用这些设置。 如果改为使用 Azure 云外壳，则不需要运行*Connect-AzAccount*。 Azure 云外壳会自动连接到 Azure 帐户。

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