---
title: Internet 对等互连-常见问题
titleSuffix: Azure
description: Internet 对等互连-常见问题
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775493"
---
# <a name="internet-peering---faqs"></a>Internet 对等互连-常见问题

对于一般问题，你可以查看以下信息。

**Internet 对等互连服务与对等互连服务之间的区别是什么？**

对等互连服务是一种服务，旨在为企业客户提供企业级的公共 IP 连接到 Microsoft。 企业级 Internet 包括通过与 Microsoft 的高吞吐量连接性和用于实现 HA 连接的冗余的 Isp 提供的连接。 此外，用户流量经过优化，可实现最近的 Microsoft Edge 的延迟。 对等互连服务建立与合作伙伴运营商的对等互连。 与合作伙伴的对等互连必须是直接对等互连，而不是 Exchange 对等互连。 直接对等必须具有本地和异地冗余。

**什么是旧式对等互连？**

使用 Azure PowerShell 设置的对等互连连接作为 Azure 资源进行管理。 在过去设置的对等互连连接以传统对等互连的形式存储在系统中，你可以选择将其转换为 Azure 资源管理。

**调用 New-AzPeeringDirectConnectionObject 时，向 Microsoft 和对等设备提供了哪些 IP 地址？**

在调用 New-AzPeeringDirectConnectionObject cmdlet 时，会输入/31 地址 (a. b./31) 或/30 地址， (输入的) 。 第一个 IP 地址 (为对等机的设备指定了一个. b. + 0) ，并向 Microsoft 设备提供) 了 (的第二个 IP 地址。

**什么是 New-AzPeeringDirectConnectionObject cmdlet 中的 MaxPrefixesAdvertisedIPv4 和 MaxPrefixesAdvertisedIPv6 参数？**

MaxPrefixesAdvertisedIPv4 和 MaxPrefixesAdvertisedIPv6 参数表示对等方希望 Microsoft 接受的 IPv4 和 IPv6 前缀的最大数目。 可以随时修改这些参数。