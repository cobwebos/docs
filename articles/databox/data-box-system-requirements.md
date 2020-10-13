---
title: Microsoft Azure Data Box 系统要求 | Microsoft Docs
description: 了解 Azure Data Box 和连接到 Data Box 的客户端的重要系统要求。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/02/2020
ms.author: alkohli
ms.openlocfilehash: 5f1623ef4dde59e816e3afe5a5f5894c49469580
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91767865"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box 系统要求

本文介绍了 Microsoft Azure Data Box 和连接到 Data Box 的客户端的重要系统要求。 建议你在部署 Data Box 之前仔细查看此信息，然后在部署和操作过程中根据需要对其进行引用。

系统需求包括：

* **软件要求：** 对于连接到 Data Box 的主机，为本地 web UI 介绍了支持的操作系统、文件传输协议、存储帐户、存储类型和浏览器。
* **网络要求：** 对于 Data Box，描述了 Data Box 的最佳操作的网络连接和端口要求。


## <a name="software-requirements"></a>软件要求

软件要求包括本地 web UI 支持的操作系统、文件传输协议、存储帐户、存储类型和浏览器。

### <a name="supported-operating-systems-for-clients"></a>客户端支持的操作系统

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>客户端支持的文件传输协议

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

> [!IMPORTANT] 
> 对于出口订单，不支持通过 REST 连接到 Data Box 共享。 

### <a name="supported-storage-accounts"></a>支持的存储帐户

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>受支持的存储类型

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>受支持的 Web 浏览器

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>网络要求

数据中心需要有高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-gbe 连接不可用，则可以使用 1-GbE 数据链接来复制数据，但会影响复制速度。

### <a name="port-requirements"></a>端口要求

下表列出了需要在防火墙中打开以允许 SMB 或 NFS 流量的端口。 在此表中 *，* (*入站*) 是指传入客户端请求访问设备的方向。 *Out* (或 *出站*) 是指 Data Box 设备在外部发送数据的方向，而不是部署：例如，到 Internet 的出站。

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>后续步骤

* [部署 Azure Data Box](data-box-deploy-ordered.md)
