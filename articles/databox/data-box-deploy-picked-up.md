---
title: 寄回 Microsoft Azure Data Box | Microsoft Docs
description: 了解如何将 Azure Data Box 寄送到 Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/24/2019
ms.author: alkohli
ms.openlocfilehash: 158c2a8919bccea03f5c7b67aef23cd07022c259
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2019
ms.locfileid: "54900504"
---
# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>教程：寄回 Azure Data Box 并验证上传到 Azure 的数据

本教程介绍如何退回 Azure Data Box 和验证上传到 Azure 的数据。

在本教程中，你将了解如下主题：

> [!div class="checklist"]
> * 先决条件
> * 准备交付
> * 将 Data Box 寄送到 Microsoft
> * 验证 Azure 中的数据上传
> * 从 Data Box 中擦除数据

## <a name="prerequisites"></a>先决条件

在开始之前，请确保：

- 已完成[教程：将数据复制到 Azure Data Box 并进行验证](data-box-deploy-copy-data.md)。 
- 复制作业已完成。 如果复制作业正在进行，则无法运行“准备交付”。

## <a name="prepare-to-ship"></a>准备交付

[!INCLUDE [data-box-prepare-to-ship](../../includes/data-box-prepare-to-ship.md)]

## <a name="ship-data-box-back"></a>寄回 Data Box

1. 确保设备已关闭电源且拔下电缆。 将设备随附的电源线卷好并安全地放在设备后面。
2. 如果设备在美国发货，请确保发货标签显示在电子墨水显示屏上，并与承运人安排好取件。 如果该标签损坏或丢失，或者未显示在电子墨水显示屏上，请在 Azure 门户中转到“概览”>“下载发货标签”。 下载发货标签，将其贴在设备上。

    如果设备在欧洲发货，则电子墨水显示屏不会显示发货标签。 而是，退件发货标签包含在转寄发货标签下的透明袋中。 移除旧的发货标签，并确保发货标签清晰可见。
    
3. 如果要寄回设备，请安排 UPS 提货。 若要安排提货，请呼叫当地的 UPS（国家/地区特定的免费电话号码），或者将 Data Box at 送到最近的自送位置。

4. 承运人提取 Data Box 并进行扫描后，门户中的订单状态将更新为“已提货”。 此外还会显示一个跟踪 ID。

## <a name="verify-data-upload-to-azure"></a>验证 Azure 中的数据上传

当 Microsoft 收到并扫描该设备时，订单状态将更新为“已接收”。 然后，该设备将经受物理验证，以确定是否存在损坏或篡改迹象。

验证完成后，Data Box 将连接到 Azure 数据中心的网络。 数据复制将自动开始。 根据数据大小，复制操作可能需要几小时到几天的时间才能完成。 可以在门户中监视复制作业的进度。

复制完成后，订单状态将更新为“已完成”。

从源中删除数据之前，请确认数据已存储在存储帐户中。 将数据复制到 Data Box 时，会根据类型将数据将上传到 Azure 存储帐户中的以下路径之一。

- 对于块 blob 和页 blob：`https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
- 对于 Azure 文件：`https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

或者，可以转到 Azure 门户中的 Azure 存储帐户并从那里导航。

## <a name="erasure-of-data-from-data-box"></a>从 Data Box 中擦除数据
 
上传到 Azure 完成后，Data Box 将根据 [NIST SP 800-88 修订版 1 准则](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)擦除其磁盘上的数据。 

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 先决条件
> * 准备交付
> * 将 Data Box 寄送到 Microsoft
> * 验证 Azure 中的数据上传
> * 从 Data Box 中擦除数据

请转到以下文章，了解如何通过本地 Web UI 管理 Data Box。

> [!div class="nextstepaction"]
> [使用本地 Web UI 管理 Azure Data Box](./data-box-local-web-ui-admin.md)


