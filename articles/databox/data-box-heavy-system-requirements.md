---
title: Microsoft Azure 数据框大量的系统要求 |Microsoft Docs
description: 了解有关在 Azure 数据框大量的网络要求的软件
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: 8cd2f96954cde367eb99d89e89bcf672b53dd590
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247989"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure 数据框大量的系统要求

本文介绍 Azure 数据框大量设备以及用于连接到设备的客户端的重要的系统要求。 我们建议在部署你的数据框重负载，然后返回到它根据需要在过程中引用的部署和后续操作之前仔细查看此信息。

系统需求包括：

* **连接到数据框大量的主机的软件要求**-介绍受支持的平台、 本地 web UI 的浏览器、 SMB 客户端和主机可以连接到 Data Box 的任何其他要求。
* **数据框大量的网络要求**-提供有关最佳操作的数据框大量设备的网络要求的信息。

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

数据中心需要有高速网络。 有关复制速度最快，两个 40 千兆以太网连接可以利用并行 （每个节点一个）。 如果您不具备 40 GbE 可用，我们建议具有至少两个 10 千兆以太网连接 （每个节点一个）。

## <a name="next-steps"></a>后续步骤

* [部署 Azure Data Box](data-box-deploy-ordered.md)
