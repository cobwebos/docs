---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/06/2018
ms.author: alkohli
ms.openlocfilehash: 67de9042af11a2b17c4b65f8225ecd0580b95466
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263472"
---
| 端口号。| 入或出 | 端口范围| 必选|   说明 |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|出|WAN |否|出站端口用于 Internet 访问以检索更新。 <br>出站 Web 代理可由用户配置。 |
| TCP 443 (HTTPS)|出|WAN|是|出站端口用于访问云中的数据。<br>用户可配置出站 Web 代理。|   
| UDP 53 (DNS)|出|WAN|某些情况下<br>请参阅说明|仅当使用基于 Internet 的 DNS 服务器时，才需要此端口。<br>我们建议使用本地 DNS 服务器。 |
| UDP 123 (NTP)|出|WAN|某些情况下<br>请参阅说明|仅当使用基于 Internet 的 NTP 服务器时，才需要此端口。  |
| UDP 67 (DHCP)|出|WAN|某些情况下<br>请参阅说明|仅当使用 DHCP 服务器时，才需要此端口。  |
| TCP 80 (HTTP)|In|LAN|是|此端口是设备上用于本地管理的本地 UI 的入站端口。 <br>通过 HTTP 访问本地 UI 会自动重定向到 HTTPS。  |
| TCP 443 (HTTPS)|In|LAN|是|此端口是设备上用于本地管理的本地 UI 的入站端口。 |