---
title: Azure Data Box Gateway 预览版发行说明 | Microsoft Docs
description: 介绍了运行预览版的 Azure Data Box Gateway 的严重待解决问题。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/07/2019
ms.author: alkohli
ms.openlocfilehash: 738cc5b4e90d9572b65f122076973e2d9f1b264f
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450484"
---
# <a name="azure-data-box-gateway-preview-release-notes"></a>Azure Data Box Gateway 预览版发行说明

## <a name="overview"></a>概述

以下发行说明标识了 Microsoft Azure Data Box Gateway 预览版的严重待解决问题和已解决的问题。

发行说明会持续进行更新，并且会陆续将所发现的需要解决的重要问题添加到说明中。 在部署 Data Box Gateway 之前，请仔细查看本发行说明中包含的信息。

本预览版对应于软件版本 **Data Box Gateway 预览版本 2.0**。

## <a name="issues-fixed-in-preview-release"></a>预览版中已修复的问题

下表提供了此版本中所修复问题的摘要。

| 不是。 | 问题 |
| --- | --- |
| **1.** | 在此发行版中，当刷新由另一工具 (AzCopy) 上传的文件，然后以会增大/扩展文件大小的方式更新文件时，会发生以下错误：错误 400: InvalidBlobOrBlock (指定的 blob 或块内容无效。)|
| **2.** |由于此版本中的一个 bug，在 *error.xml* 中可能会看到错误代码 110 的实例以及不可识别的项名称。 | 
| **3.** |由于此版本中的一个 bug，在上传某些文件时，可能会看到错误代码 2003 的实例。 | 
| **4.** |在此版本中，一次只能刷新一个共享。 | 


## <a name="known-issues-in-preview-release"></a>本预览版中的已知问题

下表提供了运行预览版的 Data Box Gateway 的已知问题的汇总。

| 不是。 | Feature | 问题 | 解决方法/备注 |
| --- | --- | --- | --- |
| **1.** |更新 |在早期预览版本中创建的 Data Box Gateway 设备无法更新到此版本。 |从新版本中下载虚拟磁盘映像，然后配置并部署新设备。 有关详细信息，请转到[准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。 |
| **2.** |预配的数据磁盘 |在预配指定大小的数据磁盘，并创建相应的 Data Box Gateway 后，不能立即收缩数据磁盘。 尝试收缩磁盘会导致设备上的所有本地数据丢失。 | |
| **3.** |重命名 |不支持对象重命名。 |如果此功能对你的工作流极其重要，请联系 Microsoft 支持部门。 |
| **4.** |复制| 如何将只读文件复制到设备，则不会保留只读属性。 | |
| **5.** |文件类型 | 不支持以下 Linux 文件类型：字符文件、块文件、套接字、管道、符号链接。  |复制这些文件会导致在 NFS 共享上创建 0 长度文件。 这些文件仍处于错误状态，并将在 *error.xml* 中报告。 |
| **6.** |删除 | 由于此版本中的一个 bug，删除 NFS 共享时，可能不会将其删除。 共享状态将显示“正在删除”。  |只有当该共享使用了不受支持的文件名时，才会发生这种情况。 |
| **7.** |刷新 | 权限和访问控制列表 (ACL) 不会通过刷新操作保存。  | |
| **8.** |联机帮助 |Azure 门户中的“帮助”链接可能无法链接到文档。|在正式发布版中，帮助链接将正常工作。 |



## <a name="next-steps"></a>后续步骤

- [准备部署 Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。


