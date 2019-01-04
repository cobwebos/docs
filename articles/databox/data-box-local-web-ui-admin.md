---
title: Microsoft Azure Data Box 本地 Web UI 管理 | 关于数据的 Microsoft Docs
description: 介绍如何使用本地 Web UI 管理 Data Box 设备
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 12/27/2018
ms.author: alkohli
ms.openlocfilehash: c0793cd4adc5359aaa2282eb2505c257315c056f
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793387"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box"></a>使用本地 Web UI 管理 Data Box

本文介绍可在 Data Box 上执行的一些配置和管理任务。 可以通过 Azure 门户 UI 和设备的本地 Web UI 管理 Data Box。 本文重点介绍可使用本地 Web UI 执行的任务。

Data Box 的本地 Web UI 用于设备的初始配置。 还可以使用本地 Web UI 关闭或重启 Data Box、运行诊断测试、更新软件、查看副本日志以及生成供 Microsoft 支持部门使用的日志包。

本文包括以下教程：

- 生成支持包
- 关闭或重启设备
- 查看设备的可用容量
- 跳过校验和验证 

## <a name="generate-support-package"></a>生成支持包

如果遇到任何设备问题，可通过系统日志创建支持包。 Microsoft 支持部门使用此包来解决此问题。 要生成支持包，请执行以下步骤：

1. 在本地 Web UI 中，转到“联系支持部门”并单击“创建支持包”。

    ![创建支持包 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. 将收集到一个支持包。 此操作需要几分钟才能完成。

    ![创建支持包 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. 支持包创建完成后，单击“下载支持包”。 

    ![创建支持包 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. 浏览并选择下载位置。 打开文件夹以查看内容。

    ![创建支持包 5](media/data-box-local-web-ui-admin/create-support-package-5.png)


## <a name="shut-down-or-restart-your-device"></a>关闭或重启设备

可使用本地 Web UI 关闭或重启 Data Box。 在重启之前，建议使共享依次在主机和设备上脱机。 这能在最大程度上减少发生数据损坏的可能性。 关闭设备时，请确保当前未执行数据复制。

要关闭 Data Box，请执行以下步骤。

1. 在本地 Web UI 中，转到“关闭或重启”。
2. 单击“关闭”。

    ![关闭 Data Box 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. 出现确认提示时，单击“确定”以继续。

    ![关闭 Data Box 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

关闭设备后，使用前面板上的电源按钮打开设备。

要重启 Data Box，请执行以下步骤。

1. 在本地 Web UI 中，转到“关闭或重启”。
2. 请单击“重启”。

    ![重启 Data Box 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. 出现确认提示时，单击“确定”以继续。

   设备将关闭并重启。

## <a name="view-available-capacity-of-the-device"></a>查看设备的可用容量

可使用设备仪表板查看设备的可用和已用容量。 

1. 在本地 web UI 中，转到“查看仪表板”。
2. “连接和复制”下显示设备上的可用和已用空间。

    ![查看可用容量](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)


## <a name="skip-checksum-validation"></a>跳过校验和验证

准备交付时，默认情况下会生成数据的校验和。 在某些极少数情况下，性能可能非常慢，具体取决于数据类型（小型文件大小）。 在这种情况，则可以跳过校验和。 

我们强烈建议仅在性能受到严重影响的情况下禁用校验和。

1. 在设备本地 Web UI 的右上角，转到“设置”。

    ![禁用校验和](media/data-box-local-web-ui-admin/disable-checksum.png)

2. 禁用校验和验证
3. 单击“应用”。

## <a name="next-steps"></a>后续步骤

- 了解如何[通过 Azure 门户管理 Data Box](data-box-portal-admin.md)。

