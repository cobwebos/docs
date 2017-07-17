---
title: "修改本地网络网关 IP 地址前缀和 VPN 网关 IP 地址 | Azure| CLI | Microsoft Docs"
description: "本文介绍了如何使用 Azure CLI 更改本地网络网关的 IP 地址前缀。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: cbcb0b626aceccf9c5a29e60e5b0c66e7812dee1
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017


---
# 使用 Azure CLI 修改本地网络网关设置
<a id="modify-local-network-gateway-settings-using-the-azure-cli" class="xliff"></a>

有时，本地网络网关的地址前缀或网关 IP 地址的设置会变更。 本文演示如何修改本地网络网关设置。 你还可以使用另一种方法来修改这些设置，该方法是通过从以下列表中选择一个不同的选项：

> [!div class="op_single_selector"]
> * [Azure 门户](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## 开始之前
<a id="before-you-begin" class="xliff"></a>

安装最新版本的 CLI 命令（2.0 或更高版本）。 有关安装 CLI 命令的信息，请参阅 [Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)（安装 Azure CLI 2.0）。

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## 修改 IP 地址前缀
<a id="modify-ip-address-prefixes" class="xliff"></a>

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## 修改网关 IP 地址
<a id="modify-the-gateway-ip-address" class="xliff"></a>

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## 后续步骤
<a id="next-steps" class="xliff"></a>

可验证网关连接。 请参阅[验证网关连接](vpn-gateway-verify-connection-resource-manager.md)。


