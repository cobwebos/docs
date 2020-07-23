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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "77133597"
---
使用提升的权限打开 PowerShell 控制台。

如果要在本地运行 Azure PowerShell，请连接到 Azure 帐户。 Connect-AzureRmAccount cmdlet 会提示输入凭据  。 进行身份验证后，它会下载帐户设置，以便 Azure PowerShell 可以使用这些设置。 如果使用 Azure Cloud Shell，则不需要运行*AzAccount*。 Azure Cloud Shell 会自动连接到 Azure 帐户。

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