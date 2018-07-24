---
title: 寄回 Microsoft Azure Data Box 磁盘 | Microsoft Docs
description: 通过本教程了解如何将 Azure Data Box 磁盘寄送到 Microsoft
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 783c837bbb9ce22e7354252523e6725753776836
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011052"
---
# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>教程：寄回 Azure Data Box 磁盘并验证 Azure 中的数据上传

这是在系列教程“部署 Azure Data Box 磁盘”中的最后一篇教程。 在本教程中，将了解如何：

> [!div class="checklist"]
> * 将 Data Box 磁盘寄送到 Microsoft
> * 验证 Azure 中的数据上传
> * 从 Data Box 磁盘中擦除数据

## <a name="prerequisites"></a>先决条件

在开始之前，请确保已完成[教程：将数据复制到 Azure Data Box 磁盘并验证](data-box-disk-deploy-copy-data.md)。

## <a name="ship-data-box-disk-back"></a>寄回 Data Box 磁盘

1. 数据验证完成后，请取出磁盘。 拔下连接线。
2. 将磁盘和连接线包装在汽泡袋中，并在其放入包装箱。
3. 使用粘贴在包装箱上的透明塑料套管中提供的退件发货标签。 如果该标签损坏或丢失，请从 Azure 门户下载新的发货标签，并将其粘贴在设备上。 转到“概况”>“下载发货标签”。 

    ![下载发货标签](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    此操作会下载如下所示的退件发货标签。

    ![示例发货标签](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)

4. 密封包装箱，并确保退件发货标签可见。
5. 如果在美国境内退回设备，请安排 UPS 提货。 如果在欧洲使用 DHL 退回设备，请访问 DHL 网站并指定航空运单号，请求 DHL 提货。 转到 DHL Express 运营国家/地区的网站，选择“Book a Courier Collection > eReturn Shipment”（“预订快递取件”>“eReturn 发货”）。

    ![DHL eReturn 发货](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
    指定运单号，然后单击“Schedule Pickup”（安排提货）以安排提货。

      ![安排提货](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

7. 承运人提取磁盘后，门户中的订单状态将更新为“已提货”。 此外还会显示一个跟踪 ID。

    ![磁盘已提货](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

## <a name="verify-data-upload-to-azure"></a>验证 Azure 中的数据上传

当 Microsoft 接收和扫描磁盘时，作业状态将更新为“已接收”。 

![磁盘已接收](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

将磁盘连接到 Azure 数据中心的服务器后，会自动复制数据。 根据数据大小，复制操作可能需要几小时到几天的时间才能完成。 可以在门户中监视复制作业的进度。

复制完成后，订单状态将更新为“已完成”。

![数据复制完成](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

从源中删除数据之前，请确认数据已存储在存储帐户中。 若要验证数据是否已上传到 Azure，请执行以下步骤：

1. 转到与磁盘订单关联的存储帐户。
2. 转到“Blob 服务”>“浏览 Blob”。 此时会显示容器列表。 在存储帐户中，将创建与 *BlockBlob* 和 *PageBlob* 文件夹下的子文件夹同名的容器。
    如果文件夹名称不符合 Azure 命名约定，则无法将数据上传到 Azure。

4. 若要验证是否已加载整个数据集，请使用 Microsoft Azure 存储资源管理器。 附加对应于磁盘租赁订单的存储帐户，然后查看 Blob 容器列表。 选择容器，然后依次单击“...更多”、“文件夹统计信息”。 “活动”窗格中会显示该文件夹的统计信息，包括 Blob 数目和 Blob 总大小。 以字节表示的 Blob 总大小应与数据集大小匹配。

    ![存储资源管理器中的文件夹统计信息](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>从 Data Box 磁盘中擦除数据

完成复制并已验证数据位于 Azure 存储帐户中后，请根据 NIST 标准安全擦除磁盘。 

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 磁盘的主题，例如：

> [!div class="checklist"]
> * 将 Data Box 磁盘寄送到 Microsoft
> * 验证 Azure 中的数据上传
> * 从 Data Box 磁盘中擦除数据


转到下一篇操作指南，了解如何通过 Azure 门户管理 Data Box 磁盘。

> [!div class="nextstepaction"]
> [使用 Azure 门户管理 Azure Data Box 磁盘](./data-box-portal-ui-admin.md)


