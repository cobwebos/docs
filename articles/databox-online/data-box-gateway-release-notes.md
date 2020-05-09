---
title: Azure Data Box Gateway 公开发行说明 |Microsoft Docs
description: 描述运行正式发行版的 Azure Data Box Gateway 的重要打开问题和解决方法。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: 1a8a9840cc6e1f3627c5fbd30e0b7432db0f16e4
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561049"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure Data Box Edge/Azure Data Box Gateway 公开发行说明

## <a name="overview"></a>概述

以下发行说明针对 Azure Data Box Edge 和 Azure Data Box Gateway 确定了公开上市（GA）发布的重要问题和解决问题。 

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署 Data Box Edge/Data Box Gateway 之前，请仔细查看发行说明中包含的信息。

GA 版本对应于软件版本：

- **Data Box Gateway 1903 （1.5.814.447）**
- **Data Box Edge 1903 （1.5.814.447）**


## <a name="whats-new"></a>新增功能

- **新的虚拟磁盘映像**-新的 VHDX 和 VMDK 现在可在 Azure 门户中使用。 下载这些映像来预配、配置和部署新的 Data Box Gateway GA 设备。 在早期预览版本中创建的 Data Box Gateway 设备无法更新到此版本。 有关详细信息，请转到[准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。
- **Nfs 支持**-nfs 支持目前处于预览阶段，可用于访问 Data Box Edge 和 Data Box Gateway 设备的3.0 和 v2.0 客户端。
- **存储复原**-Data Box Edge 设备可以承受一个具有存储复原功能的数据磁盘故障。 此功能目前以预览版提供。 可以通过在本地 web UI 的**存储设置**中选择**弹性**选项来启用存储复原。


## <a name="known-issues-in-ga-release"></a>GA 版本中的已知问题

下表提供了 Data Box Gateway 运行版本的已知问题的摘要。

| 否。 | Feature | 问题 | 解决方法/备注 |
| --- | --- | --- | --- |
| **1.** |文件类型 | 不支持以下文件类型：字符文件、块文件、套接字、管道、符号链接。  |复制这些文件会导致在 NFS 共享上创建 0 长度文件。 这些文件仍处于错误状态，并将在 *error.xml* 中报告。 <br> 指向目录的符号链接导致目录永远不会被标记为脱机。 因此，可能看不到目录上有表明目录处于脱机状态的灰色叉，且所有相关内容都完全上传到 Azure 中。 |
| **2.** |删除 | 由于此版本中的一个 bug，删除 NFS 共享时，可能不会将其删除。 共享状态将显示“正在删除”**。  |只有当该共享使用了不受支持的文件名时，才会发生这种情况。 |
| **3.** |复制 | 数据复制失败并出现错误：由于文件系统限制，请求的操作无法完成。  |不支持与文件大小大于 128 KB 关联的备用数据流（ADS）。   |


## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。
- [准备部署 Azure Data Box Edge](azure-stack-edge-deploy-prep.md)。
