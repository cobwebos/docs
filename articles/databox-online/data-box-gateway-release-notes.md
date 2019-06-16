---
title: Azure 数据框网关正式版发行说明 |Microsoft Docs
description: 介绍 Azure 数据框网关运行公开发行版的重要待解决问题和解决方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60754193"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>数据框边缘/Azure 数据框网关正式发布的 azure 发行说明

## <a name="overview"></a>概述

以下发行说明识别重要的待解决问题和已解决的问题的正式版 (GA) 发布为 Azure 数据框边缘和 Azure 数据框网关。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署你的数据框边缘/数据框的网关之前，请仔细查看发行说明中包含的信息。

GA 版本对应于软件版本：

- **数据框网关 1903 (1.5.814.447)**
- **数据框边缘 1903 (1.5.814.447)**


## <a name="whats-new"></a>新增功能

- **新的虚拟磁盘映像**-现可在 Azure 门户中新的 VHDX 和 VMDK。 下载这些映像来预配、 配置和部署新的数据框网关 GA 设备。 在早期预览版本中创建的 Data Box Gateway 设备无法更新到此版本。 有关详细信息，请转到[准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。
- **NFS 支持**-NFS 的支持目前处于预览状态，可用于 v3.0 和 4.1 版访问的数据框边缘和数据框网关设备的客户端。
- **存储复原能力**-你的数据框边缘设备可以承受具有存储复原功能的一个数据磁盘的故障。 此功能目前处于预览状态。 可以通过选择启用存储复原能力**复原**选项**存储设置**在本地 web UI。


## <a name="known-issues-in-ga-release"></a>GA 版本中已知的问题

下表提供数据框网关的运行版本的已知问题的摘要。

| 不。 | Feature | 问题 | 解决方法/备注 |
| --- | --- | --- | --- |
| **1.** |文件类型 | 不支持以下文件类型： 字符文件、 阻止的文件、 套接字、 管道、 符号链接。  |复制这些文件会导致在 NFS 共享上创建 0 长度文件。 这些文件仍处于错误状态，并将在 *error.xml* 中报告。 <br> 指向目录的符号链接导致目录永远不会被标记为脱机。 因此，可能看不到目录上有表明目录处于脱机状态的灰色叉，且所有相关内容都完全上传到 Azure 中。 |
| **2.** |删除 | 由于此版本中的一个 bug，删除 NFS 共享时，可能不会将其删除。 共享状态将显示“正在删除”  。  |只有当该共享使用了不受支持的文件名时，才会发生这种情况。 |
| **3.** |复制 | 数据复制失败，出现错误：因文件系统限制而无法完成请求的操作。  |不支持备用数据 Stream (ADS) 与文件大小大于 128 KB。   |


## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。
- [准备部署 Azure 数据框边缘](data-box-edge-deploy-prep.md)。
