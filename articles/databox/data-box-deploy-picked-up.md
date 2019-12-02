---
title: Azure Data Box 寄回教程 | Microsoft Docs
description: 了解如何将 Azure Data Box 寄送到 Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 1c375370ec6bae2775ad758688825b92cbbbca50
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407030"
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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>验证 Azure 中的数据上传

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>从 Data Box 中擦除数据
 
上传到 Azure 完成后，Data Box 将根据 [NIST SP 800-88 修订版 1 准则](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)擦除其磁盘上的数据。

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]



::: zone-end


## <a name="in-australiatabin-australia"></a>[在澳大利亚](#tab/in-australia)

澳大利亚的 Azure 数据中心会提供额外的安全通知。 所有入境装运必须收到提前通知。 若要在澳大利亚寄送，请执行以下步骤。


1. 保留寄送设备所用的原始包装盒供退回寄送时使用。
2. 确保数据复制到设备已完成，并且“准备交付”  运行成功。
3. 关闭设备电源并拔下电缆。
4. 将设备随附的电源线卷好并安全地放在设备后面。
5. 打开 [DHL 链接](https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference)，在线预约取件。

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>验证 Azure 中的数据上传

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>从 Data Box 中擦除数据
 
上传到 Azure 完成后，Data Box 将根据 [NIST SP 800-88 修订版 1 准则](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)擦除其磁盘上的数据。

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

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

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>验证 Azure 中的数据上传

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>从 Data Box 中擦除数据
 
上传到 Azure 完成后，Data Box 将根据 [NIST SP 800-88 修订版 1 准则](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)擦除其磁盘上的数据。

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end



