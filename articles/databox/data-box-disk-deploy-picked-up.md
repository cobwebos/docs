---
title: 寄回 Azure Data Box Disk 的教程| Microsoft Docs
description: 通过本教程了解如何将 Azure Data Box 磁盘寄送到 Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: alkohli
ms.localizationpriority: high
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: b67e7bf0b6b79d2ae29c2ba8ed7c337f152faebc
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092083"
---
::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>退回 Azure Data Box Disk 

::: zone-end

::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>教程：退回 Azure Data Box Disk 

本教程介绍如何安排取件以退回 Azure Data Box Disk。 取件说明取决于你将设备退回的位置。 

在本教程中，将了解如何：

> [!div class="checklist"]
> * 将 Data Box 磁盘寄送到 Microsoft
> * 在不同区域中提取 Data Box Disk

## <a name="prerequisites"></a>先决条件

在开始之前，请确保已完成[教程：将数据复制到 Azure Data Box Disk 并进行验证](data-box-disk-deploy-copy-data.md)。


## <a name="ship-data-box-disk-back"></a>寄回 Data Box 磁盘

::: zone-end

1. 数据验证完成后，请取出磁盘。 拔下连接线。
2. 将磁盘和连接线包装在汽泡袋中，并在其放入包装箱。 如果缺少附件，我们可能会收取费用。
    - 重复使用最初的发货包装。  
    - 我们建议使用加固的气泡袋包装磁盘。
    - 确保填塞物贴合，以避免磁盘在包装箱中发生移动。

接下来的步骤根据在何处退回设备而定。 对于美国/加拿大、欧盟 (EU)、澳大利亚或亚洲国家/地区，说明会有所不同。

### <a name="in-us-or-canadatabin-us-or-canada"></a>[在美国或加拿大](#tab/in-us-or-canada)

如果在美国或加拿大退回设备，请执行以下步骤。

1. 使用粘贴在包装箱上的透明塑料套管中提供的退件发货标签。 如果标签受损或丢失：
    - 转至“概述”>“下载发货标签”  并下载退件发货标签。
    - 将标签粘贴在设备上。

2. 密封包装箱，并确保退件发货标签可见。
3. 安排 UPS 取件。 安排提货：

    - 给本地 UPS 打电话（特定于国家/地区的免费电话号码）。
    - 在通话中，引用反向装运跟踪号码，如打印的标签中所示。
    - 如果未引用跟踪号码，UPS 将要求你在提货时支付额外费用。
    - 也可以在最近的卸货位置放置 Data Box Disk，而不是安排提货。

### <a name="in-europetabin-europe"></a>[在欧洲](#tab/in-europe)

如果在欧洲退回设备，请执行以下步骤。

1. 使用粘贴在包装箱上的透明塑料套管中提供的退件发货标签。 如果标签受损或丢失：
    - 转至“概述”>“下载发货标签”  并下载退件发货标签。
    - 将标签粘贴在设备上。

2. 密封包装箱，并确保退件发货标签可见。
3. 如果在欧洲使用 DHL 退回设备，请访问 DHL 网站并指定航空运单号，请求 DHL 提货。
4. 转到 DHL Express 运营国家/地区的网站，选择“Book a Courier Collection > eReturn Shipment”（“预订快递取件”>“eReturn 发货”）。     
3. 指定运单号，然后单击“Schedule Pickup”（安排提货）以安排提货。 

### <a name="in-australiatabin-australia"></a>[在澳大利亚](#tab/in-australia)

澳大利亚的 Azure 数据中心会提供额外的安全通知。 所有入境装运必须收到提前通知。 对于澳大利亚的取件，请执行以下步骤。

1. 使用提供的退货发运标签，并确保在它上面写下 TAU 代码（参考编号）。 如果缺少提供的发运标签，或者有任何其他问题，请向 [Data Box 亚洲运营部门](mailto:adbo@microsoft.com)发送电子邮件。 在主题标题中提供订单名称，并提供所面临问题的详细信息。
3. 将标签粘贴在盒子上。 
4. 打开链接 https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference ，在线预约取件。 

### <a name="in-japantabin-japan"></a>[在日本](#tab/in-japan)

1. 在托运单上写下公司名称和地址信息作为发件人信息。
2. 使用以下电子邮件模板将电子邮件发送给 Quantium Solution。

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```
    - **如果在大阪取件**，则将电子邮件模板中的主题修改为：`Pickup request for Microsoft Azure OSA`。
    - 如果日本邮政货到付款托运单未包含在内或缺失，请在这封电子邮件中注明。 Quantium Solutions Japan 将请求日本邮政在取件时带上托运单。
    - 如果你有多个订单，请分别发送电子邮件以确保单独取件。

3. 在预订完取件后，将会收到 Quantium Solutions 发来的电子邮件确认函。 该电子邮件确认函还包含与货到付款托运单相关的信息。

如果需要，可通过以下信息联系 Quantium Solution 支持（日语）： 

- 电子邮件：Customerservice.JP@quantiumsolutions.com 
- 电话：03-5755-0150 

### <a name="in-koreatabin-korea"></a>[在韩国](#tab/in-korea)

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


### <a name="in-singaporetabin-singapore"></a>[在新加坡](#tab/in-singapore)

1. 打印发货标签并将其贴在盒子上。 如果标签受损或丢失：
    - 转至“概述”>“下载发货标签”  并获取退件发货标签。
    - 将标签粘贴在设备上。 请确保该标签是可见的。

2. 若要请求分拣，请使用以下模板和跟踪号（跟踪号可在送达的包裹中提供的退货标签上找到），向 SingPost 客户服务发送电子邮件。

    ```
    To: kadcustcare@singpost.com
    Subject: Microsoft Azure Pick-up - XZ00001234567 
    Body: 
     a. Requestor name
     b. Requestor contact number
     c. Requestor collection address
     d. Preferred collection date
    ```

   > [!NOTE]
   > 对于在工作日内的以下时间收到的预订请求：
   > - 如果在下午 3 点之前收到，分拣将在下一个工作日的上午 9 点到下午 1 点进行。
   > - 如果在下午 3 点之后收到，分拣将在下一个工作日的下午 2 点到下午 6 点进行。

   如果遇到任何问题，请访问 Data Box Operations Asia，网址为 adbo@microsoft.com。 提供主题标头中的作业名称和遇到的问题。

3. 转交给快递员。 


::: zone target="docs"

## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 磁盘的主题，例如：

> [!div class="checklist"]
> * 将 Data Box 磁盘寄送到 Microsoft
> * 在不同区域中提取 Data Box Disk

转到下一个操作指南，了解如何验证从 Data Box Disk 上传到 Azure 存储帐户的数据。

> [!div class="nextstepaction"]
> [验证从 Azure Data Box Disk 上传的数据](./data-box-disk-deploy-picked-up.md)

::: zone-end




