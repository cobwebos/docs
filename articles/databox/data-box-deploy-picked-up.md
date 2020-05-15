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
ms.openlocfilehash: 03fe40cea5891e87f42f0d6a50eedee80a68f25e
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200521"
---
::: zone target="docs"

# <a name="tutorial-return-azure-data-box-and-verify-data-upload-to-azure"></a>教程：寄回 Azure Data Box 并验证上传到 Azure 的数据

::: zone-end

::: zone target="chromeless"

## <a name="return-data-box-and-verify-data-upload-to-azure"></a>退回 Data Box 并验证上传到 Azure 的数据

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

## <a name="in-us-canada-europe"></a>[在美国、加拿大和欧洲](#tab/in-us-canada-europe)

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


## <a name="in-australia"></a>[在澳大利亚](#tab/in-australia)

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

## <a name="in-japan"></a>[在日本](#tab/in-japan) 

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

## <a name="in-singapore"></a>[在新加坡](#tab/in-singapore) 

1. 保留寄送设备所用的原始包装盒供退回寄送时使用。
2. 记下跟踪编号（在 Data Box 本地 Web UI 的“准备交付”页面上显示为参考编号）。 这在“准备交付”步骤成功完成后可用。 从此页面上下载发货标签并粘贴在包装箱上。 
3. 关闭设备电源并拔下电缆。
4. 将设备随附的电源线卷好并安全地放在设备后面。 
5. 使用带有跟踪编号的以下电子邮件模板向 SingPost 客户服务发送电子邮件。

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pick-up - OrderName 
    Body: 
        1. Requestor name  
        2. Requestor contact number
        3. Requestor collection address
        4. Preferred collection date
    ```

   > [!NOTE]
   > 对于在工作日内的以下时间收到的预订请求：
   > - 如果在下午 3 点之前收到，分拣将在下一个工作日的上午 9 点到下午 1 点进行。
   > - 如果在下午 3 点之后收到，分拣将在下一个工作日的下午 2 点到下午 6 点进行。  

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>验证 Azure 中的数据上传

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>从 Data Box 中擦除数据
 
上传到 Azure 完成后，Data Box 将根据 [NIST SP 800-88 修订版 1 准则](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)擦除其磁盘上的数据。

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end


<!--## [In Korea](#tab/in-korea) 

1. Retain the original box used to ship the device for return shipment.
2. Note down the tracking number (shown as reference number on the Prepare to Ship page of the Data Box local web UI). This is available after the prepare to ship step successfully completes. Download the shipping label from this page and paste on the packing box. 
3. Power off the device and remove the cables.
4. Spool and securely place the power cord that was provided with the device in the back of the device. 

Request pickup  
If consignment note is present:  

1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number to arrange for a collection.
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.  
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.  
4. You will receive an email confirmation for the pickup schedule.  

Exception process
If the consignment note is not present:
1. Call Quantium Solutions International hotline at 070-8231-1418 during office hours (10 AM to 5 PM, Monday to Friday). Quote Microsoft Azure pickup and the service request number. Specify that you need a new consignment note to arrange for a collection. Provide sender (customer), receiver information (Azure datacenter), and reference number (service request number).
2. If the hotline is busy, email microsoft@rocketparcel.com, with the email subject Microsoft Azure Pickup and the service request number as reference.
3. If the courier does not arrive for collection, call Quantium Solutions International hotline for alternate arrangements.
4. You get a verbal confirmation if request is made via telephone.  
::: zone target="chromeless"

## Verify data upload to Azure

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## Erasure of data from Data Box
 
Once the upload to Azure is complete, the Data Box erases the data on its disks as per the [NIST SP 800-88 Revision 1 guidelines](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end
-->

## <a name="self-managed"></a>[自托管](#tab/in-selfmanaged) 

如果在日本、新加坡、韩国和西欧使用 Data Box，并在创建订单时选择了自托管寄送选项，请按以下说明操作。 

1. 成功完成此步骤后，请记下 Data Box 本地 Web UI 的“准备交付”页上显示的授权代码。
2. 关闭设备电源并拔下电缆。 将设备随附的电源线卷好并安全地放在设备后面。
3. 准备好返回设备时，请使用以下模板将电子邮件发送到 Azure Data Box 运营团队。
    
    ```
    To: adbops@microsoft.com 
    Subject: Request for Azure Data Box drop-off for order: ‘orderName’ 
    Body: 
        1. Order name  
        2. Authorization code available after Prepare to Ship has completed [Yes/No]  
        3. Contact name of the person dropping off. You will need to display a Government approved ID during the drop off.
    ```

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>验证 Azure 中的数据上传

[!INCLUDE [data-box-verify-upload](../../includes/data-box-verify-upload.md)]

## <a name="erasure-of-data-from-data-box"></a>从 Data Box 中擦除数据
 
上传到 Azure 完成后，Data Box 将根据 [NIST SP 800-88 修订版 1 准则](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi)擦除其磁盘上的数据。

::: zone-end

::: zone target="docs"

[!INCLUDE [data-box-verify-upload-return](../../includes/data-box-verify-upload-return.md)]

::: zone-end

