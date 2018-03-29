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
ms.openlocfilehash: 9b168231669c50c8f00d3527288fd03ab3bf9ce8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
### <a name="noconnection"></a>修改本地网关 IP 地址前缀 - 无网关连接

如果没有网关连接且需要添加或删除 IP 地址前缀，则可使用 [az network local-gateway create](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_create) 命令，该命令也是用来创建本地网关的。 也可使用该命令来更新 VPN 设备的网关 IP 地址。 请使用本地网关的现有名称来覆盖当前设置。 如果使用其他名称，请创建一个新的本地网关，而不是覆盖现有的。

每次进行更改时，必须指定前缀的完整列表，不能仅指定要更改的前缀。 仅指定需要保留的前缀。 此例中为 10.0.0.0/24 和 20.0.0.0/24

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 -g TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="withconnection"></a>修改本地网关 IP 地址前缀 - 存在网关连接

如果有网关连接且需要添加或删除 IP 地址前缀，可使用 [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_update) 更新前缀。 这会导致 VPN 连接中断一段时间。 修改 IP 地址前缀时，不需删除 VPN 网关。

每次进行更改时，必须指定前缀的完整列表，不能仅指定要更改的前缀。 在此示例中，10.0.0.0/24 和 20.0.0.0/24 已存在。 我们会添加前缀 30.0.0.0/24 和 40.0.0.0/24，并在更新时指定所有 4 个前缀。

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 -g TestRG1
```
