---
title: Azure Data Box 寄回教程 | Microsoft Docs
description: 了解如何将 Azure Data Box 寄送到 Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 194b90ab27d02c1fa3eb05bb3ddd78395d351599
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898180"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>教程：寄回 Azure Data Box 并验证上传到 Azure 的数据

::: zone-end

::: zone target="chromeless"

# <a name="return-data-box-and-verify-data-upload-to-azure"></a>退回 Data Box 并验证上传到 Azure 的数据

::: zone-end

::: zone target="docs"

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

::: zone-end

::: zone target="chromeless"

数据复制完成后，准备并寄送设备。 当设备到达 Azure 数据中心时，数据会自动上传到 Azure。

## <a name="prepare-to-ship"></a>准备交付

在准备寄送之前，请确保复制作业已完成。

1. 在本地 Web UI 中转到“准备交付”页面，开始发货准备。  
2. 通过本地 Web UI 关闭设备。 移除设备的电缆。 

接下来的步骤根据在何处退回设备而定。

::: zone-end

::: zone target="docs"

## <a name="ship-data-box-back"></a>寄回 Data Box

确保数据复制到设备已完成，并且“准备交付”  运行成功。 根据要寄送设备的区域，过程会有所不同。

::: zone-end

## <a name="in-us-canada-europetabin-us-canada-europe"></a>[在美国、加拿大和欧洲](#tab/in-us-canada-europe)

如果在美国、加拿大或欧洲退回设备，请执行以下步骤。

1. 确保已关闭设备电源且拔下电缆。 
2. 将设备随附的电源线卷好并安全地放在设备后面。
3. 确保发货标签显示在电子墨水显示屏上，并与承运人安排好取件。 如果该标签损坏或丢失，或者未显示在电子墨水显示屏上，请联系 Microsoft 支持部门。 在支持部门建议的情况下，可以在 Azure 门户中转到“概览”>“下载发货标签”  。 下载发货标签，将其贴在设备上。 
4. 如果要寄回设备，请安排 UPS 提货。 安排提货：

    - 给本地 UPS 打电话（特定于国家/地区的免费电话号码）。
    - 在通话中，引用反向装运跟踪号码，如电子墨水显示屏或打印的标签中所示。
    - 如果未引用跟踪号码，UPS 将要求你在提货时支付额外费用。

    也可以在最近的卸货位置放置 Data Box，而不是安排提货。
4. 承运人提取 Data Box 并进行扫描后，门户中的订单状态将更新为“已提货”。  此外还会显示一个跟踪 ID。


## <a name="in-australiatabin-australia"></a>[在澳大利亚](#tab/in-australia)

澳大利亚的 Azure 数据中心会提供额外的安全通知。 所有入境装运必须收到提前通知。 若要在澳大利亚寄送，请执行以下步骤。


1. 保留寄送设备所用的原始包装盒供退回寄送时使用。
2. 确保数据复制到设备已完成，并且“准备交付”  运行成功。
3. 关闭设备电源并拔下电缆。
4. 将设备随附的电源线卷好并安全地放在设备后面。
5. 向 Quantium Solutions 发送电子邮件以请求取货。 请参考 Azure 门户上指定的服务参考编号。 使用以下电子邮件模板：- *Request for reverse shipping label with TAU code*。 请务必在电子邮件中包含以下详细信息： 

    ```
    To: Azure@quantiumsolutions.com
    Subject: Pickup request for Azure｜Reference number：XXX XXX XXX
    Body: 
    - Company name：
    - Address:
    - Contact name:
    - Contact number:
    - Requested pickup date: mm/dd
    ```
6. Quantium Solutions Australia 将通过电子邮件提供退货发运标签。
7. 打印退货标签并将其贴在包装盒上。
8. 将包裹转交给快递员。

如果需要，可以将电子邮件发送到 Azure@quantiumsolutions.com 或打电话联系 Quantium Solution 支持人员。

有关如何通过打电话对订单进行查询，下面提供了相应的步骤：

- 首先发送一封取件电子邮件。
- 在打电话时提供订单名称。

## <a name="in-japantabin-japan"></a>[在日本](#tab/in-japan) 

1. 保留寄送设备所用的原始包装盒供退回寄送时使用。
2. 关闭设备电源并拔下电缆。
3. 将设备随附的电源线卷好并安全地放在设备后面。
4. 在托运单上写下公司名称和地址信息作为发件人信息。
5. 使用以下电子邮件模板将电子邮件发送给 Quantium Solution。

    - 如果日本邮政货到付款托运单未包含在内或缺失，请在这封电子邮件中注明。 Quantium Solutions Japan 将请求日本邮政在取件时带上托运单。
    - 如果你有多个订单，请分别发送电子邮件以确保单独取件。

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Azure Data Box｜Job name： 
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

::: zone target="docs"

## <a name="verify-data-upload-to-azure"></a>验证 Azure 中的数据上传

当 Microsoft 收到并扫描该设备时，订单状态将更新为“已接收”。  然后，该设备将经受物理验证，以确定是否存在损坏或篡改迹象。

验证完成后，Data Box 将连接到 Azure 数据中心的网络。 数据复制将自动开始。 根据数据大小，复制操作可能需要几小时到几天的时间才能完成。 可以在门户中监视复制作业的进度。

复制完成后，订单状态将更新为“已完成”。 

从源中删除数据之前，请确认数据已上传到 Azure。 你的数据可位于：

- Azure 存储帐户。 将数据复制到 Data Box 时，会根据类型将数据将上传到 Azure 存储帐户中的以下路径之一。

  - 对于块 blob 和页 blob：`https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - 对于 Azure 文件：`https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    或者，可以转到 Azure 门户中的 Azure 存储帐户并从那里导航。

- 托管磁盘资源组。 创建托管磁盘时，VHD 作为页 blob 进行上传，然后转换为托管磁盘。 托管磁盘会附加到在创建排序时指定的资源组上。 

    - 如果在 Azure 中成功复制到托管磁盘，则可转到 Azure 门户中的“订单详细信息”，记下为托管磁盘指定的资源组  。

        ![标识托管磁盘资源组](media/data-box-deploy-copy-data-from-vhds/order-details-managed-disk-resource-groups.png)

        转到所记录的资源组，并找到你的托管磁盘。

        ![附加到资源组的托管磁盘](media/data-box-deploy-copy-data-from-vhds/managed-disks-resource-group.png)

    - 如果复制了 VHDX 或动态/差异 VHD，则 VHDX/VHD 会作为页 blob 上传到暂存存储帐户，但 VHD 转换到托管磁盘将失败。 请转到临时“存储帐户”>“Blob”，然后选择相应的容器 - 标准 SSD、标准 HDD 或高级 SSD  。 VHD 作为页 blob 上传到暂存存储帐户。

::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>验证 Azure 中的数据上传

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

::: zone-end

## <a name="erasure-of-data-from-data-box"></a>从 Data Box 中擦除数据
 
上传到 Azure 完成后，Data Box 将根据 [NIST SP 800-88 修订版 1 准则](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)擦除其磁盘上的数据。

::: zone target="docs"

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

::: zone-end


