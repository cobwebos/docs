---
title: 互联网对等互连 - 常见问题解答
titleSuffix: Azure
description: 互联网对等互连 - 常见问题解答
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9b0b2b08e01c99fc918c4bc5649197c9caa4978a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775493"
---
# <a name="internet-peering---faqs"></a>互联网对等互连 - 常见问题解答

您可以查看以下信息，了解一般问题。

**互联网对等互连和对等服务之间的区别是什么？**

对等服务是一项旨在为其企业客户提供企业级公共 IP 连接的服务。 企业级 Internet 包括通过与 Microsoft 具有高吞吐量连接的 ISP 的连接和 HA 连接的冗余。 此外，用户流量针对延迟到最近的 Microsoft Edge 进行了优化。 对等服务基于与合作伙伴运营商的对等互连连接。 与合作伙伴的对等互连连接必须是直接对等互连，而不是 Exchange 对等互连。 直接对等互连必须具有本地和地理冗余。

**什么是传统对等互连？**

使用 Azure PowerShell 设置的对等互连连接作为 Azure 资源进行管理。 过去设置的对等互连连接存储在我们的系统中，作为旧对等互连，您可以选择将其转换为作为 Azure 资源进行管理。

**当调用 New-Az 对等互连直接连接对象时，向 Microsoft 和对等设备提供了哪些 IP 地址？**

调用 New-AzPingDirectConnectObject cmdlet 时，将输入 /31 地址 （a.b.c.d/31） 或 /30 地址 （a.b.c.d/30）。 第一个 IP 地址 （a.b.c.d_0） 提供给对等方的设备，第二个 IP 地址 （a.b.c.d_1） 提供给 Microsoft 设备。

**什么是 MaxPrefixes 广告IPv4 和 MaxPrefixes 广告IPv6 参数在 New-AzPing 直接连接对象 cmdlet？**

MaxPrefixes广告IPv4和MaxPrefixes广告IPv6参数表示 IPv4 的最大数量，IPv6 前缀是同行希望 Microsoft 接受的。 这些参数可以随时修改。