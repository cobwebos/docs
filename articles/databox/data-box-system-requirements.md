---
title: Microsoft Azure Data Box 系统要求 | Microsoft Docs
description: 了解 Azure Data Box 的软件和网络要求
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380587"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box 系统要求

本文介绍 Microsoft Azure Data Box 以及连接到 Data Box 的客户端的重要系统要求。 建议在部署 Data Box 之前仔细查看信息，然后在部署和后续操作期间根据需要重新参考。

系统需求包括：

* **连接到 Data Box 的主机的软件要求** - 介绍支持的平台、本地 Web UI 的浏览器、SMB 客户端以及可连接到 Data Box 的主机的任何其他要求。
* **Data Box 的网络要求** - 提供有关 Data Box 最佳操作的网络要求的信息。


## <a name="software-requirements"></a>软件要求

软件要求包括有关支持的操作系统、本地 Web UI 支持的浏览器和 SMB 客户端的信息。

### <a name="supported-operating-systems-for-clients"></a>客户端支持的操作系统

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Linux 客户端支持的文件系统

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>支持的存储帐户

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>受支持的存储类型

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>受支持的 Web 浏览器

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>网络要求

数据中心需要有高速网络。 强烈建议你至少建立一个 10-GbE 连接。 如果 10-GbE 连接不可用，则可使用 1-GbE 数据链路复制数据，但复制速度会受影响。

### <a name="port-requirements"></a>端口要求

下表列出了需要在防火墙中打开以允许 SMB 或 NFS 通信的端口。 在此表中，*入*或*入站*表示传入客户端请求访问设备的方向。 " *Out* " 或 "*出站*" 是指 Data Box 设备在外部发送数据的方向，而不是部署：例如，到 Internet 的出站。

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>后续步骤

* [部署 Azure Data Box](data-box-deploy-ordered.md)
