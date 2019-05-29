---
title: 有关订购 Azure Data Box Disk 的教程 | Microsoft Docs
description: 使用本教程了解如何注册和订购用于将数据导入 Azure 的 Azure Data Box 磁盘。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 1d25ea2ce6e365e0d04fab325f9c13bb37382758
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603166"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>教程：订购 Azure Data Box Disk

Azure Data Box Disk 是一个混合云解决方案，可用于快速、方便、可靠地将本地数据导入 Azure 中。 你可以将数据传输到 Microsoft 提供的固态硬盘 (SSD)，并寄回磁盘。 然后，此数据将上传到 Azure。

本教程介绍如何订购 Azure Data Box 磁盘。 本教程的介绍内容包括：

> [!div class="checklist"]
> * 订购 Data Box 磁盘
> * 跟踪订单
> * 取消订单

## <a name="prerequisites"></a>先决条件

在部署之前，请完成适用于 Data Box 服务和 Data Box Disk 的以下配置先决条件。

### <a name="for-service"></a>对于服务

在开始之前，请确保：
- 具有 Microsoft Azure 存储帐户和访问凭据。
- 确保用于 Data Box 服务的订阅是下述类型之一：
    - Microsoft 企业协议 (EA)。 详细了解 [EA 订阅](https://azure.microsoft.com/pricing/enterprise-agreement/)。
    - 云解决方案提供商 (CSP)。 详细了解 [Azure CSP 计划](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)。
- 确保有订阅的所有者或参与者访问权限，以便创建 Data Box 订单。

### <a name="for-device"></a>对于设备

在开始之前，请确保：
- 你有一台可用的客户端计算机，可以从中复制数据。 客户端计算机必须：
    - 运行[支持的操作系统](data-box-disk-system-requirements.md#supported-operating-systems-for-clients)。
    - 已安装其他[所需软件](data-box-disk-system-requirements.md#other-required-software-for-windows-clients)（如果是 Windows 客户端）。  

## <a name="order-data-box-disk"></a>订购 Data Box 磁盘

在 [Azure 门户](https://aka.ms/azuredataboxfromdiskdocs)中执行以下步骤订购 Data Box 磁盘。

1. 在门户的左上角单击“+ 创建资源”，然后搜索“Azure Data Box”。   单击“Azure Data Box”。 
    
   ![搜索“Azure Data Box 1”](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. 单击“创建”。 

3. 检查 Data Box 服务是否在你的区域可用。 输入或选择以下信息，然后单击“应用”。 

    ![选择 Data Box 磁盘选项](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |设置|值|
    |---|---|
    |订阅|选择已为其启用了 Data Box 服务的订阅。<br> 订阅链接到计费帐户。 |
    |传输类型| 导入到 Azure|
    |来源国家/地区 | 选择数据当前所在的国家/地区。|
    |目标 Azure 区域|选择要在其中传输数据的 Azure 区域。|

  
5.  选择“Data Box 磁盘”。  单次订购的解决方案（包括 5 个磁盘）的最大容量为 35 TB。 可以创建多个订单，以增加数据大小。

     ![选择 Data Box 磁盘选项](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  在“订单”中指定“订单详细信息”。   输入或选择以下信息。

    |设置|值|
    |---|---|
    |名称|提供友好名称用于跟踪订单。<br> 名称可以为 3 到 24 个字符，包括字母、数字和连字符。 <br> 名称必须以字母或数字开头和结尾。 |
    |资源组| 使用现有资源组，或创建新的资源组。 <br> 资源组是可以统一管理或部署的资源的逻辑容器。 |
    |目标 Azure 区域| 选择存储帐户的区域。<br> 目前，支持美国、西欧和北欧、加拿大和澳大利亚的所有区域的存储帐户。 |
    |估计数据大小(TB)| 输入估计值，以 TB 为单位。 <br>根据数据大小，Microsoft 会寄送适当数量的 8 TB SSD（7 TB 可用容量）。 <br>5 个磁盘的最大可用容量高达 35 TB。 |
    |磁盘密钥| 如果选中“使用自定义密钥而不是 Azure 生成的密钥”  ，请提供磁盘密钥。 <br> 提供一个 12 到 32 个字符的字母数字密钥，其中至少包含一个数字和一个特殊字符。 允许使用的特殊字符是 `@?_+`。 <br> 可以选择跳过此选项并使用 Azure 生成的密钥来解锁磁盘。|
    |存储目标     | 从存储帐户和/或托管磁盘中进行选择。 <br> 根据指定的 Azure 区域，从现有存储帐户的筛选列表中选择一个存储帐户。 Data Box 可以与最多 10 个存储帐户链接。 <br> 也可以创建新的**常规用途 v1**、**常规用途 v2** 或 **Blob 存储帐户**。 <br>无法使用已配置了规则的存储帐户。 存储帐户必须在防火墙和虚拟网络部分中**允许来自所有网络的访问**。|

    如果使用存储帐户作为存储目标，则会看到以下屏幕截图：

    ![存储帐户的 Data Box Disk 订单](media/data-box-disk-deploy-ordered/order-storage-account.png)

    如果使用 Data Box Disk 从本地 VHD 创建托管磁盘，则还需提供以下信息：

    |设置  |值  |
    |---------|---------|
    |资源组     | 若要从本地 VHD 创建托管磁盘，请创建新的资源组。 使用现有资源组的前提是，它是由 Data Box 服务为托管磁盘的 Data Box Disk 订单创建的。 <br> 只支持一个资源组。|

    ![托管磁盘的 Data Box Disk 订单](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    为托管磁盘指定的存储帐户用作临时存储帐户。 Data Box 服务将 VHD 上传到临时存储帐户，然后将其转换为托管磁盘并移到资源组。 有关详细信息，请参阅[验证 Azure 中的数据上传](data-box-disk-deploy-picked-up.md#verify-data-upload-to-azure)。

13. 单击“下一步”。 

    ![提供订单详细信息](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. 在“寄送地址”选项卡中，提供你的名字和姓氏、公司的名称和邮政地址，以及有效的电话号码。  单击“验证地址”。  服务将验证寄送地址，以确定服务是否在该区域可用。 如果服务在指定的寄送地址可用，则会将结果通知给你。 

    ![提供寄送地址](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. 在“通知详细信息”中，指定电子邮件地址。  服务会将有关任何订单状态更新的电子邮件通知发送到指定的电子邮件地址。 

    我们建议使用组电子邮件，以便在组中的管理员离任后，可以持续收到通知。

16. 查看与订单、联系人、通知和隐私条款相关的信息**摘要**。 选中对应于同意隐私条款的复选框。

17. 单击“订购”。  创建订单需要几分钟时间。

 
## <a name="track-the-order"></a>跟踪订单

下单后，可以从 Azure 门户跟踪订单状态。 转到自己的订单，然后转到“概况”查看状态。  门户中会显示作业处于“已订购”状态。 

![“已订购”Data Box 磁盘状态](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

如果磁盘缺货，你会收到通知。 如果磁盘有货，Microsoft 会指定要发货的磁盘，并准备磁盘包装。 在磁盘准备期间，会执行以下操作：

- 使用 AES-128 BitLocker 加密技术将磁盘加密。  
- 锁定磁盘，防止有人未经授权访问磁盘。
- 在此过程中，将生成用于解锁磁盘的支持密钥。

磁盘准备完成后，门户会显示订单处于“已处理”状态。 

然后，Microsoft 会安排区域承运人发运磁盘。 磁盘发货后，你会收到跟踪号。 门户会显示订单处于“已发运”状态。 

## <a name="cancel-the-order"></a>取消订单

若要取消此订单，请在 Azure 门户中转到“概览”，然后在命令栏中单击“取消”。  

只能在订购磁盘后我们正在处理订单交货时取消订单。 一旦订单已处理，就再也不能取消订单。

![取消订单](media/data-box-disk-deploy-ordered/cancel-order1.png)

若要删除已取消的订单，请转到“概况”，然后在命令栏中单击“删除”。  


## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 订购 Data Box 磁盘
> * 跟踪订单
> * 取消订单

请继续学习下一篇教程，了解如何设置 Data Box 磁盘。

> [!div class="nextstepaction"]
> [设置 Azure Data Box 磁盘](./data-box-disk-deploy-set-up.md)
