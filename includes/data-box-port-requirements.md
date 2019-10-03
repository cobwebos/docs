---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: 4a3925752d1af5e43d5984b06c0a68aa9faa214b
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839770"
---
| 端口号。| 入或出 | 端口范围| 必填| 说明 |   |
|--------|-----|-----|-----------|----------|-----------|
| TCP 80 (HTTP)|In|LAN|是|此端口用于通过 HTTP 连接到数据框博客存储 REST Api。 如果未连接到 REST Api，这会自动重定向到本地 web UI 超过 8443。 |
| TCP 443 (HTTPS)|In|LAN|是|此端口用于通过 HTTPS 连接到数据框博客存储 REST Api。 如果未连接到 REST Api，这会自动重定向到本地 web UI 超过 8443。 |
| TCP 8443 (HTTPS-Alt)|In|LAN|是|这是为支持 HTTPS 的备用端口，连接到设备管理的本地 web UI 时使用。 |
| TCP 445 (SMB)|出/入|LAN|某些情况下<br>请参阅说明|仅当通过 SMB 进行连接时，才需要此端口。 |
| TCP 2049 (NFS)|出/入|LAN|某些情况下<br>请参阅说明|仅当要通过 NFS 连接时，才需要此端口。 |
| TCP 111 (NFS)|出/入|LAN|某些情况下<br>请参阅说明|此端口是用于 rpcbind/端口映射，只需要您要连接通过 NFS。  |
