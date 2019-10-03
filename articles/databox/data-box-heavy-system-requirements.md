---
title: Microsoft Azure Data Box Heavy 系统要求 | Microsoft Docs
description: 了解 Azure Data Box Heavy 的软件和网络要求
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839783"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure 数据框大量的系统要求

本文介绍 Azure Data Box Heavy 设备以及连接到该设备的客户端的重要系统要求。 建议在部署 Data Box Heavy 之前仔细查看信息，然后在部署和后续操作期间根据需要重新参考。

系统需求包括：

* **连接到 Data Box Heavy 的主机的软件要求** - 介绍支持的平台、本地 Web UI 的浏览器、SMB 客户端以及可连接到 Data Box 的主机的任何其他要求。
* **Data Box Heavy 的网络要求** - 提供有关 Data Box Heavy 设备最佳操作的网络要求的信息。

## <a name="software-requirements"></a>软件要求

软件要求包括有关支持的操作系统、本地 Web UI 支持的浏览器和 SMB 客户端的信息。

### <a name="supported-operating-systems-for-clients"></a>客户端支持的操作系统

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Linux 客户端支持的文件系统

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>支持的存储帐户

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>支持的存储类型

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>受支持的 Web 浏览器

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>网络要求

数据中心需要有高速网络。 为获得最快的复制速度，可以同时利用两个 40-GbE 连接（在每个节点上使用一个）。 如果没有可用的 40-GbE，我们建议至少开通两个 10-GbE 连接（在每个节点上使用一个）。

### <a name="port-requirements"></a>端口要求

下表列出了需要在防火墙以允许 SMB 或 NFS 流量打开端口。 在此表中，*入*或*入站*表示传入客户端请求访问设备的方向。 *Out*或*出站*表示在其中的数据框大量设备发送数据的外部，部署的方向： 例如，出站到 Internet。

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>后续步骤

* [部署 Azure Data Box](data-box-deploy-ordered.md)
