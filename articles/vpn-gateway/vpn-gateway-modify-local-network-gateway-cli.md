---
title: 修改本地网络网关 IP 地址前缀和 VPN 网关 IP 地址 | Azure| CLI | Microsoft Docs
description: 本文介绍了如何使用 Azure CLI 更改本地网络网关的 IP 地址前缀。
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: a8f0c95acf872431fe7538acbd4ff1023c1496c0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961218"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>使用 Azure CLI 修改本地网络网关设置

有时，本地网络网关的地址前缀或网关 IP 地址的设置会变更。 本文演示如何修改本地网络网关设置。 还可以选择以下列表中的其他选项，使用另一种方法来修改这些设置：

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before"></a>准备工作

安装最新版本的 CLI 命令（2.0 或更高版本）。 有关安装 CLI 命令的信息，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="ipaddprefix"></a>修改 IP 地址前缀

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="gwip"></a>修改网关 IP 地址

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>后续步骤

可验证网关连接。 请参阅[验证网关连接](vpn-gateway-verify-connection-resource-manager.md)。

