---
title: Azure Data Box 门户管理员指南 | Microsoft Docs
description: 介绍如何使用 Azure 门户管理 Azure Data Box。
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 10/19/2018
ms.author: alkohli
ms.openlocfilehash: f2ad4dc3d74771d28ca91e41f2b0eb547d678908
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122988"
---
# <a name="use-the-azure-portal-to-administer-your-data-box"></a>使用 Azure 门户管理 Data Box

本文介绍了可对 Data Box 执行的一些复杂工作流和管理任务。 你可以通过 Azure 门户和本地 Web UI 管理 Data Box。 

本文重点介绍可以使用 Azure 门户执行的任务。 使用 Azure 门户可以管理订单、管理 Data Box，以及跟踪订单在从头到尾的处理过程中的状态。


## <a name="cancel-an-order"></a>取消订单

下达订单后，你可能会出于各种原因需要取消订单。 只能在订单处理之前取消订单。 一旦订单已处理并且已准备好 Data Box，就不能取消订单。 

可以执行以下步骤来取消订单。

1.  转到“概况”>“取消”。 

    ![取消订单 1](media/data-box-portal-admin/cancel-order1.png)

2.  填写取消订单的原因。  

    ![取消订单 2](media/data-box-portal-admin/cancel-order2.png)

3.  取消订单后，门户会更新订单的状态，并显示订单“已取消”。 

## <a name="clone-an-order"></a>克隆订单

克隆操作在某些情况下很有用。 例如，用户已使用 Data Box 传输了一些数据。 随着生成的数据越来越多，需要使用 Data Box 将这些数据传输到 Azure。 在这种情况下，只需克隆同一份订单即可。

执行以下步骤来克隆订单。

1.  转到“概况”>“克隆”。 

    ![克隆订单 1](media/data-box-portal-admin/clone-order1.png)

2.  订单的所有详细信息保持不变。 订单名称是原始订单名称后接 *-Clone*。 选中表示确认你已查看隐私信息的复选框。 单击“创建”。

几分钟后将会创建克隆的订单，并且门户会更新，以显示新订单。


## <a name="delete-order"></a>删除订单

订单处理完成后，你可能想要删除订单。 订单中包含姓名、地址和联系信息等个人信息。 删除订单会删除这些个人信息。

只能删除已完成或已取消的订单。 执行以下步骤删除订单。

1. 转到“所有资源”。 搜索订单。

2. 单击要删除的订单，并转到“概况”。 在命令栏中，单击“删除”。

    ![删除 Data Box 订单 1](media/data-box-portal-admin/delete-order1.png)

3. 当系统提示确认删除订单时，请输入订单名称。 单击“删除” 。

## <a name="download-shipping-label"></a>下载发货标签

如果 Data Box 的电子墨水显示屏不工作并且没有返回发货标签，则你可能需要下载发货标签。 

执行以下步骤下载发货标签。

1.  转到“概况”>“下载发货标签”。 只有在设备已发货后，此选项才可用。 

    ![下载发货标签](media/data-box-portal-admin/download-shipping-label.png)

2.  这会将下载以下退件发货标签。 保存该标签并将其打印出来。折叠标签并将插入到设备上的透明封套中。 请确保标签可见。 清除在之前的发货中留在设备上的任何贴纸。

    ![示例发货标签](media/data-box-portal-admin/example-shipping-label.png)

## <a name="edit-shipping-address"></a>编辑寄送地址

下单后，你可能需要编辑寄送地址。 只能在发运设备之前执行此操作。 一旦设备已发运，此选项就不再可用。

执行以下步骤编辑订单。

1. 转到“订单详细信息”>“编辑寄送地址”。

    ![编辑寄送地址 1](media/data-box-portal-admin/edit-shipping-address1.png)

2. 编辑并验证寄送地址，然后保存更改。

    ![编辑寄送地址 2](media/data-box-portal-admin/edit-shipping-address2.png)

## <a name="edit-notification-details"></a>编辑通知详细信息

你可能需要更改订单状态电子邮件收件人用户。 例如，当设备已妥投或提货时，需要告知某个用户。 完成数据复制时可能需要告知另一个用户，使该用户在从源中删除数据之前，可以验证数据是否在 Azure 存储帐户中。 在这种情况下，可以编辑通知详细信息。

执行以下步骤编辑通知详细信息。

1. 转到“订单详细信息”>“编辑通知详细信息”。

    ![编辑通知详细信息 1](media/data-box-portal-admin/edit-notification-details1.png)

2. 现在可以编辑通知详细信息，然后保存更改。
 
    ![编辑通知详细信息 2](media/data-box-portal-admin/edit-notification-details2.png)


## <a name="download-order-history"></a>下载订单历史记录

Data Box 订单完成以后，会擦除设备磁盘上的数据。 当设备清理完成后，可以在 Azure 门户中下载订单历史记录。

若要下载订单历史记录，请执行以下步骤。

1. 在 Data Box 订单中，转到“概览”。 请确保订单完整。 如果订单完整且设备清理已完成，则请转到“订单详细信息”。 “下载订单历史记录”选项可用。

    ![下载订单历史记录](media/data-box-portal-admin/download-order-history-1.png)

2. 单击“下载订单历史记录”。 在下载的历史记录中，会看到一个有关承运人跟踪日志的记录。 如果向下滚动到该日志的底部，则可看到以下内容的链接：
    
   - **复制日志** - 包含一个文件列表，其中的文件是在将数据从 Data Box 复制到 Azure 存储帐户的过程中出错的。
   - **审核日志** - 包含有关 Data Box 启动的信息，以及当它位于 Azure 数据中心外部时在其上共享访问权限的信息。
   - **BOM 文件** - 包含在**准备寄送**过程中可以下载的文件的列表（也称文件清单），其中包含文件名、文件大小和文件校验和。

       ```
       -------------------------------
       Microsoft Data Box Order Report
       -------------------------------
        
       Name                                               : eastusdryrun                                      
       StartTime(UTC)                                     : 9/6/2018 12:54:47 PM +00:00                       
       DeviceType                                         : ImolaPod                                          
        
       -------------------
       Data Box Activities
       -------------------
        
       Time(UTC)             | Activity                       | Status          | Description                                                                                                                                           
        
       9/6/2018 12:54:51 PM  | OrderCreated         | Completed  |                                                                                                                              
       9/11/2018 8:57:38 PM  | DevicePrepared       | Completed  |                                                                                                                                                       
       9/12/2018 7:28:15 PM  | ShippingToCustomer   | InProgress | Pickup Scan. Local Time : 9/12/2018 2:52:31 PM at Chantilly                                                                                           
       9/13/2018 2:33:04 AM  | ShippingToCustomer   | InProgress | Departure Scan. Local Time : 9/12/2018 9:00:00 PM at Chantilly                                                                                                                                                                                                                                                              
       9/13/2018 12:40:31 PM | ShippingToCustomer   | InProgress | Arrival Scan. Local Time : 9/13/2018 5:00:00 AM at Oakland                                                                                            
       9/13/2018 2:42:10 PM  | ShippingToCustomer   | InProgress | Departure Scan. Local Time : 9/13/2018 6:08:00 AM at Oakland                                                                                          
       9/13/2018 3:42:12 PM  | ShippingToCustomer   | InProgress | Destination Scan. Local Time : 9/13/2018 8:14:08 AM at Sunnyvale                                                                                      
       9/13/2018 4:43:05 PM  | ShippingToCustomer   | InProgress | Destination Scan. Local Time : 9/13/2018 8:56:54 AM at Sunnyvale                                                                                      
       9/13/2018 4:43:05 PM  | ShippingToCustomer   | InProgress | Out For Delivery Today. Local Time : 9/13/2018 9:11:21 AM at Sunnyvale                                                                                
       9/13/2018 5:43:07 PM  | ShippingToCustomer   | Completed  | Delivered. Local Time : 9/13/2018 9:44:17 AM at SUNNYVALE                                                                                             
       9/14/2018 11:48:35 PM | ShippingToDataCenter | InProgress | Pickup Scan. Local Time : 9/14/2018 3:55:37 PM at Sunnyvale                                                                                                                                                                                 
       9/15/2018 1:52:35 AM  | ShippingToDataCenter | InProgress | Arrival Scan. Local Time : 9/14/2018 6:31:00 PM at San Jose                                                                                           
       9/15/2018 2:52:39 AM  | ShippingToDataCenter | InProgress | Departure Scan. Local Time : 9/14/2018 7:17:00 PM at San Jose                                                                                                                                                                             
       9/17/2018 8:23:31 AM  | ShippingToDataCenter | InProgress | Destination Scan. Local Time : 9/17/2018 4:14:37 AM at Chantilly                                                                                      
       9/17/2018 12:24:42 PM | ShippingToDataCenter | InProgress | Loaded on Delivery Vehicle. Local Time : 9/17/2018 7:45:36 AM at Chantilly                                                                            
       9/17/2018 1:25:11 PM  | ShippingToDataCenter | InProgress | Out For Delivery Today. Local Time : 9/17/2018 8:27:11 AM at Chantilly                                                                                
       9/17/2018 2:25:51 PM  | ShippingToDataCenter | Completed | Delivered. Local Time : 9/17/2018 9:56:32 AM at STERLING                                                                                              
       9/18/2018 9:55:41 PM  | DeviceBoot           | Completed | Appliance booted up successfully                                                                                                                      
       9/18/2018 11:00:25 PM | DataCopy             | Started   |                                                                                                                                                       
       9/18/2018 11:01:33 PM | DataCopy             | Completed | Copy Completed.                                                                                                                                       
       9/18/2018 11:20:58 PM | SecureErase          | Started   |                                                                                                                                                       
       9/18/2018 11:28:46 PM | SecureErase          | Completed | Azure Data Box:BY506B4B616700 has been sanitized according to NIST 800 -88 Rev 1.                                                                     
        
       ----------------------
       Data Box Job Log Links
       ----------------------
        
       Account Name         : eastusdryrun                                         
       Copy Logs Path       : copylog/copylogd695869a2a294396b7b903296c208388.xml                                                                                                                                                     
       Audit Logs Path      : azuredatabox-chainofcustodylogs\3b4cf163-f1af-475c-a391-f8afea3fa327\by506b4b616700                                                                                                                     
       BOM Files Path       : azuredatabox-chainofcustodylogs\3b4cf163-f1af-475c-a391-f8afea3fa327\by506b4b616700
       ```
     然后，可以转到存储帐户并查看复制日志。

![登录存储帐户](media/data-box-portal-admin/logs-in-storage-acct-2.png)

也可查看包含审核日志和 BOM 文件的一系列监管日志。

![登录存储帐户](media/data-box-portal-admin/logs-in-storage-acct-1.png)

## <a name="view-order-status"></a>查看订单状态

当设备状态在门户中发生更改时，你会通过电子邮件收到通知。

|订单状态 |说明 |
|---------|---------|
|已订购     | 已成功下单。 <br>如果设备有货，Microsoft 会确定要发货的设备，并准备设备。 <br> 如果不是可以立即提供设备，则将在有设备可用时处理订单。 订单可能需要花费几天到几个月的时间来进行处理。 如果不能在 90 天内履行订单，则订单将取消并且会向你发送通知。         |
|已处理     | 订单处理已完成。 根据你的订单，在数据中心内做好了设备的发货准备工作。         |
|已发运     | 订单已发货。 可以使用门户中你的订单上显示的跟踪 ID 来跟踪货物。        |
|已交货     | 货物已交付到订单中指定的地址。        |
|已提货     |承运人已提取并扫描了你的回寄设备。         |
|Received     | 已收到你的设备并在 Azure 数据中心对其进行了扫描。 <br> 在检查发运的设备后，将启动设备上传。      |
|数据复制     | 正在复制数据。 可以在 Azure 门户中跟踪订单的复制进度。 <br> 请等待数据复制完成。 |
|已完成       |已成功完成订单。<br> 从服务器中删除本地数据之前，请验证数据是否已在 Azure 中。         |
|已完成但出错| 数据复制已完成，但在复制期间发生错误。 <br> 请使用 Azure 门户中提供的路径查看复制日志。   |
|已取消            |订单已取消。 <br> 你取消了订单，或者由于遇到错误，服务取消了订单。 如果不能在 90 天内履行订单，则订单也将取消并且会向你发送通知。     |
|清理 | 已擦除设备磁盘上的数据。 当 Azure 门户中提供了可供下载的订单历史记录时，可以认为设备清理已完成。|



## <a name="next-steps"></a>后续步骤

- 了解如何[排查 Data Box 问题](data-box-faq.md)。
