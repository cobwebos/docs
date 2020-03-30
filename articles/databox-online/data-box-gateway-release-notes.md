---
title: Azure 数据盒网关通用可用性发行说明*微软文档
description: 介绍运行常规可用性版本的 Azure 数据盒网关的关键打开问题和解决方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265397"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure 数据框边缘/Azure 数据框网关通用可用性发布说明

## <a name="overview"></a>概述

以下发行说明标识 Azure 数据框边缘和 Azure 数据框网关的通用可用性 （GA） 发布的关键未决问题和已解决的问题。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署数据框边缘/数据盒网关之前，请仔细查看发行说明中包含的信息。

GA 版本对应于软件版本：

- **数据盒网关 1903 （1.5.814.447）**
- **数据框边缘 1903 （1.5.814.447）**


## <a name="whats-new"></a>新增功能

- **新的虚拟磁盘映像**- 新的 VHDX 和 VMDK 现在在 Azure 门户中可用。 下载这些映像以预配、配置和部署新的数据盒网关 GA 设备。 在早期预览版本中创建的 Data Box Gateway 设备无法更新到此版本。 有关详细信息，请转到[准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。
- **NFS 支持**- NFS 支持当前处于预览状态，可用于访问数据框边缘和数据盒网关设备的 v3.0 和 v4.1 客户端。
- **存储恢复能力**- 您的数据盒边缘设备可以使用存储恢复功能承受一个数据磁盘的故障。 此功能目前处于预览状态。 通过在本地 Web UI 中的 **"存储"设置**中选择 **"弹性"** 选项，可以启用存储恢复能力。


## <a name="known-issues-in-ga-release"></a>GA 版本中的已知问题

下表提供了运行版本的数据盒网关的已知问题的摘要。

| 不是。 | Feature | 问题 | 解决方法/备注 |
| --- | --- | --- | --- |
| **1.** |文件类型 | 不支持以下文件类型：字符文件、块文件、套接字、管道、符号链接。  |复制这些文件会导致在 NFS 共享上创建 0 长度文件。 这些文件仍处于错误状态，并将在 *error.xml* 中报告。 <br> 指向目录的符号链接导致目录永远不会被标记为脱机。 因此，可能看不到目录上有表明目录处于脱机状态的灰色叉，且所有相关内容都完全上传到 Azure 中。 |
| **2.** |删除 | 由于此版本中的一个 bug，删除 NFS 共享时，可能不会将其删除。 共享状态将显示“正在删除”**。  |只有当该共享使用了不受支持的文件名时，才会发生这种情况。 |
| **3.** |复制 | 数据复制失败与错误：由于文件系统限制，无法完成请求的操作。  |不支持与大于 128 KB 的文件大小关联的备用数据流 （ADS）。   |


## <a name="next-steps"></a>后续步骤

- [准备部署 Azure 数据框网关](data-box-gateway-deploy-prep.md)。
- [准备部署 Azure 数据框边缘](data-box-edge-deploy-prep.md)。
