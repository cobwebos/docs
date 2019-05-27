---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 98f9e0377e560fa0bba2fd470ff01431b2ed21d9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161102"
---
| 端口号。| 入或出 | 端口范围| 需要|   说明 |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|输出|WAN |“否”|出站端口用于 Internet 访问以检索更新。 <br>出站 Web 代理可由用户配置。 |
| TCP 443 (HTTPS)|输出|WAN(广域网)|“是”|出站端口用于访问云中的数据。<br>出站 Web 代理可由用户配置。|
| UDP 123 (NTP)|输出|WAN(广域网)|某些情况下<br>请参阅说明|仅当使用基于 Internet 的 NTP 服务器时，才需要此端口。  |   
| UDP 53 (DNS)|出|WAN(广域网)|某些情况下<br>请参阅说明|仅当使用基于 Internet 的 DNS 服务器时，才需要此端口。<br>我们建议使用本地 DNS 服务器。 |
| TCP 5985 (WinRM)|出/入|LAN|某些情况下<br>请参阅说明|通过基于 HTTP 的远程 PowerShell 连接到设备时，需要此端口。  |
| UDP 67 (DHCP)|输出|LAN|某些情况下<br>请参阅说明|仅当使用本地 DHCP 服务器时，才需要此端口。  |
| TCP 80 (HTTP)|出/入|LAN|“是”|此端口是设备上用于本地管理的本地 UI 的入站端口。 <br>通过 HTTP 访问本地 UI 会自动重定向到 HTTPS。  |
| TCP 443 (HTTPS)|出/入|LAN|“是”|此端口是设备上用于本地管理的本地 UI 的入站端口。 |
| TCP 445 (SMB)|In|LAN|某些情况下<br>请参阅说明|仅当通过 SMB 连接时，才需要此端口。 |
| TCP 2049 (NFS)|In|LAN|某些情况下<br>请参阅说明|仅当通过 NFS 连接时，才需要此端口。 |
