---
title: 寄回 Azure Data Box Disk 的教程| Microsoft Docs
description: 通过本教程了解如何将 Azure Data Box 磁盘寄送到 Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: ca42532fe6ba954180ce06ed0e3561e42f1fb447
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424236"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk-and-verify-data-upload-to-azure"></a>教程：退回 Azure Data Box Disk 并验证到 Azure 的数据上传

这是本系列的最后一个教程：部署 Azure Data Box Disk。 在本教程中，将了解如何：

> [!div class="checklist"]
> * 将 Data Box 磁盘寄送到 Microsoft
> * 验证 Azure 中的数据上传
> * 从 Data Box 磁盘中擦除数据

## <a name="prerequisites"></a>先决条件

在开始之前，请确保已完成[教程：将数据复制到 Azure Data Box Disk 并进行验证](data-box-disk-deploy-copy-data.md)。


## <a name="ship-data-box-disk-back"></a>寄回 Data Box 磁盘

1. 数据验证完成后，请取出磁盘。 拔下连接线。
2. 将磁盘和连接线包装在汽泡袋中，并在其放入包装箱。 如果缺少附件，我们可能会收取费用。
    - 重复使用最初的发货包装。  
    - 我们建议使用加固的气泡袋包装磁盘。
    - 确保填塞物贴合，以避免磁盘在包装箱中发生移动。

接下来的步骤根据在何处退回设备而定。

### <a name="pick-up-in-us-canada"></a>在美国和加拿大取件

如果在美国或加拿大退回设备，请执行以下步骤。

1. 使用粘贴在包装箱上的透明塑料套管中提供的退件发货标签。 如果标签受损或丢失：
    - 转到“概况”>“下载发货标签”。 

        ![下载发货标签](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        此操作会下载如下所示的退件发货标签。

        ![示例发货标签](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - 将标签粘贴在设备上。

2. 密封包装箱，并确保退件发货标签可见。
3. 安排 UPS 取件。 安排提货：

    - 给本地 UPS 打电话（特定于国家/地区的免费电话号码）。
    - 在通话中，引用反向装运跟踪号码，如打印的标签中所示。
    - 如果未引用跟踪号码，UPS 将要求你在提货时支付额外费用。
    - 也可以在最近的卸货位置放置 Data Box Disk，而不是安排提货。


### <a name="pick-up-in-europe"></a>在欧洲取件

如果在欧洲退回设备，请执行以下步骤。

1. 使用粘贴在包装箱上的透明塑料套管中提供的退件发货标签。 如果标签受损或丢失：
    - 转到“概况”>“下载发货标签”。 

        ![下载发货标签](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        此操作会下载如下所示的退件发货标签。

        ![示例发货标签](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - 将标签粘贴在设备上。

2. 密封包装箱，并确保退件发货标签可见。
3. 如果在欧洲使用 DHL 退回设备，请访问 DHL 网站并指定航空运单号，请求 DHL 提货。
4. 转到 DHL Express 运营国家/地区的网站，选择“Book a Courier Collection > eReturn Shipment”（“预订快递取件”>“eReturn 发货”）。 

    ![DHL 退还运输](media/data-box-disk-deploy-picked-up/dhl-ship-1.png)
    
3. 指定运单号，然后单击“Schedule Pickup”（安排提货）以安排提货。 

      ![安排提货](media/data-box-disk-deploy-picked-up/dhl-ship-2.png)

### <a name="pick-up-in-asia-pacific-region"></a>在亚太区域取件

此区域会提供有关在日本、韩国、澳大利亚和新加坡取件的说明。

#### <a name="pick-up-in-australia"></a>在澳大利亚取件

澳大利亚的 Azure 数据中心会提供额外的安全通知。 所有入境装运必须收到提前通知。 对于澳大利亚的取件，请执行以下步骤。

1. 向 `adbops@microsoft.com` 发送电子邮件，请求带有入境 ID 或 TAU 码的发货标签。 请在计划发货日期之前的至少 3 天发出请求，以便及时收到标签。
2. 电子邮件主题应是：*Request for reverse shipping label with TAU code*。 请务必在电子邮件中包含以下详细信息： 

    - 订单名称
    - 地址
    - 联系人姓名

#### <a name="pick-up-in-japan"></a>在日本取件

1. 在托运单上写下公司名称和地址信息作为发件人信息。
2. 使用以下电子邮件模板将电子邮件发送给 Quantium Solution。

    - 如果日本邮政货到付款托运单未包含在内或缺失，请在这封电子邮件中注明。 Quantium Solutions Japan 将请求日本邮政在取件时带上托运单。
    - 如果你有多个订单，请分别发送电子邮件以确保单独取件。

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```

3. 在预订完取件后，将会收到 Quantium Solutions 发来的电子邮件确认函。 该电子邮件确认函还包含与货到付款托运单相关的信息。

如果需要，可通过以下信息联系 Quantium Solution 支持（日语）： 

- 电子邮件：Customerservice.JP@quantiumsolutions.com 
- 电话：03-5755-0150 

#### <a name="pick-up-in-korea"></a>在韩国取件

1. 请务必附上退还托运单。
2. 若要在提供有托运单的情况下请求取件，请执行以下操作：
    1. 在办公时间（星期一到星期五的上午 10 点到下午 5 点）拨打 *Quantium Solutions International* 热线 070-8231-1418。 请注明“Microsoft Azure 取件”  和服务请求编号，以便安排收件。  
    2. 如果热线繁忙，请向 `microsoft@rocketparcel.com` 发送电子邮件，并附上电子邮件主题“Microsoft Azure 取件”  和服务请求编号作为参考。
    3. 如果快递公司未前来收件，请拨打 *Quantium Solutions International* 热线安排其他快递公司。 
    4. 你将会收到取件安排的电子邮件确认函。
3. 仅当托运单未提供时，才执行此步骤。 请求取件：
    1. 在办公时间（星期一到星期五的上午 10 点到下午 5 点）拨打 *Quantium Solutions International* 热线 070-8231-1418。 请注明“Microsoft Azure 取件”  和服务请求编号，以便安排收件。 指明你需要新的托运单，以便安排收件。 请提供发件人（客户）、收件人信息（Azure 数据中心）以及参考编号（服务请求编号）。 
    2. 如果热线繁忙，请向 `microsoft@rocketparcel.com` 发送电子邮件，并附上电子邮件主题“Microsoft Azure 取件”  和服务请求编号作为参考。
    3. 如果快递公司未前来收件，请拨打 *Quantium Solutions International* 热线安排其他快递公司。 
    4. 如果请求是通过电话进行的，你会收到口头确认。

### <a name="pick-up-in-singapore"></a>在新加坡取件

1. 打印发货标签并将其贴在盒子上。 如果标签受损或丢失：
    - 转到“概况”>“下载发货标签”。 

        ![下载发货标签](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

        此操作会下载如下所示的退件发货标签。

        ![示例发货标签](media/data-box-disk-deploy-picked-up/exmple-shipping-label.png)
    - 将标签粘贴在设备上。 请确保该标签是可见的。

2. 请求取件：
    - 请在办公时间（周一至周五，上午 9 点到下午 5 点）拨打 **SingPost** 热线，电话为 **6845 6485**。  
    - 请注明“Microsoft Azure 取件”  和服务请求编号（退还发货标签上的跟踪编号），以便安排收件。 
    - 你将会收到有关该取件安排的口头确认。 
    - 如果快递员未前来收件，请致电 **SingPost**（电话为 **6845 6485**），要求另作安排。 
3. 转交给快递员。 


## <a name="verify-data-upload-to-azure"></a>验证 Azure 中的数据上传

承运人提取磁盘后，门户中的订单状态将更新为“已提货”。  此外还会显示一个跟踪 ID。

![磁盘已提货](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

当 Microsoft 接收和扫描磁盘时，作业状态将更新为“已接收”。  

![磁盘已接收](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

将磁盘连接到 Azure 数据中心的服务器后，会自动复制数据。 根据数据大小，复制操作可能需要几小时到几天的时间才能完成。 可以在门户中监视复制作业的进度。

复制完成后，订单状态将更新为“已完成”。 

![数据复制完成](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

如果复制完成但出现错误，请参阅[排查上传错误](data-box-disk-troubleshoot-upload.md)。

从源中删除数据之前，请确认数据已存储在存储帐户中。 你的数据可位于：

- Azure 存储帐户。 将数据复制到 Data Box 时，会根据类型将数据将上传到 Azure 存储帐户中的以下路径之一。

  - 对于块 blob 和页 blob：`https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - 对于 Azure 文件：`https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    或者，可以转到 Azure 门户中的 Azure 存储帐户并从那里导航。

- 托管磁盘资源组。 创建托管磁盘时，VHD 作为页 blob 进行上传，然后转换为托管磁盘。 托管磁盘会附加到在创建排序时指定的资源组上。

  - 如果在 Azure 中成功复制到托管磁盘，则可转到 Azure 门户中的“订单详细信息”，记下为托管磁盘指定的资源组  。

      ![查看订单详细信息](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    转到所记录的资源组，并找到你的托管磁盘。

      ![托管磁盘资源组](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - 如果复制了 VHDX 或动态/差异 VHD，则 VHDX/VHD 会作为块 blob 上传到临时存储帐户。 请转到临时存储帐户 > Blob，然后选择响应的容器 - StandardSSD、StandardHDD 或 PremiumSSD  。 VHDX/VHD 应会在临时存储帐户中显示为块 blob。

若要验证数据是否已上传到 Azure，请执行以下步骤：

1. 转到与磁盘订单关联的存储帐户。
2. 转到“Blob 服务”>“浏览 Blob”。  此时会显示容器列表。 在存储帐户中，将创建与 *BlockBlob* 和 *PageBlob* 文件夹下的子文件夹同名的容器。
    如果文件夹名称不符合 Azure 命名约定，则无法将数据上传到 Azure。

4. 若要验证是否已加载整个数据集，请使用 Microsoft Azure 存储资源管理器。 附加对应于磁盘租赁订单的存储帐户，然后查看 Blob 容器列表。 选择容器，然后依次单击“...更多”、“文件夹统计信息”。   “活动”窗格中会显示该文件夹的统计信息，包括 Blob 数目和 Blob 总大小。  以字节表示的 Blob 总大小应与数据集大小匹配。

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

::: zone-end


::: zone target="chromeless"

# <a name="return-the-disk-and-verify-upload-to-azure"></a>退回磁盘并验证到 Azure 的数据上传

## <a name="ship-to-azure"></a>寄送到 Azure

1. 数据验证完成后，请取出磁盘。 拔下连接线。
2. 将磁盘和连接线包装在汽泡袋中，并在其放入包装箱。 如果缺少附件，我们可能会收取费用。
    - 重复使用最初的发货包装。  
    - 我们建议使用加固的气泡袋包装磁盘。
    - 确保填塞物贴合，以避免磁盘在包装箱中发生移动。
3. 接下来的步骤根据在何处退回设备而定。
    - [如果在美国或加拿大境内退回设备，请安排 UPS 取件](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada)。
    - 通过访问 DHL 网站并指定航空帐单编号，[安排欧洲 DHL 取件](data-box-disk-deploy-picked-up.md#pick-up-in-europe)。
    - [为澳大利亚 - 太平洋地区的国家/地区（如澳大利亚、日本、韩国和新加坡等）安排取件](data-box-disk-deploy-picked-up.md#pick-up-in-asia-pacific-region)。
4. 承运人收取磁盘后，门户中的订单状态将更新，并显示跟踪 ID。

## <a name="verify-upload-to-azure"></a>验证到 Azure 的数据上传

将数据上传到 Azure 后，从源中删除数据之前，请确认数据已存储在存储帐户中。 你的数据可位于：

- Azure 存储帐户。 将数据复制到 Data Box 时，会根据类型将数据将上传到 Azure 存储帐户中的以下路径之一。

    - **对于块 blob 和页 blob**： https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt

    - **对于 Azure 文件存储**： https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt

    或者，可以转到 Azure 门户中的 Azure 存储帐户并从那里导航。

- 托管磁盘资源组。 创建托管磁盘时，VHD 作为页 blob 进行上传，然后转换为托管磁盘。 托管磁盘会附加到在创建排序时指定的资源组上。

::: zone-end


