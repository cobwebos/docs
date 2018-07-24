---
title: 有关订购 Microsoft Azure Data Box 磁盘的教程 | Microsoft Docs
description: 使用本教程了解如何注册和订购用于将数据导入 Azure 的 Azure Data Box 磁盘。
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
ms.date: 07/17/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: f46a3ecb3c4ef6ace31b7010ba9068ab04a43315
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126535"
---
# <a name="tutorial-order-an-azure-data-box-disk-preview"></a>教程：订购 Azure Data Box 磁盘（预览版）

Azure Data Box 磁盘是一个云解决方案，可用于快速、方便、可靠地将本地数据导入 Azure 中。 你可以将数据传输到 Microsoft 提供的固态硬盘 (SSD)，并寄回磁盘。 然后，此数据将上传到 Azure。 

本教程介绍如何订购 Azure Data Box 磁盘。 本教程的介绍内容包括：

> [!div class="checklist"]
> * 注册 Data Box 磁盘
> * 订购 Data Box 磁盘
> * 跟踪订单
> * 取消订单

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!IMPORTANT]
> - Data Box 磁盘以预览版提供。 在订购和部署此解决方案之前，请查看 [Azure 预览版服务的条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 
> - 在预览期，Data Box 磁盘可以寄送到美国和欧盟的客户。 有关详细信息，请参阅[适用区域](data-box-disk-overview.md#region-availability)。

## <a name="sign-up"></a>注册 

Data Box 磁盘以预览版提供，需要注册该服务。 执行以下步骤注册 Data Box 服务：

1. 登录到 Azure 门户：[https://aka.ms/azuredataboxfromdiskdocs](https://aka.ms/azuredataboxfromdiskdocs)。
2. 选择要为预览版启用的订阅。 回答有关数据大小、数据所在国家/地区、时间范围和数据传输频率的问题。 单击“注册!”。
3. 注册并为预览版启用订阅后，即可订购 Data Box 磁盘。

## <a name="order-data-box-disk"></a>订购 Data Box 磁盘

在 [Azure 门户](https://aka.ms/azuredataboxfromdiskdocs)中执行以下步骤订购 Data Box 磁盘。

1. 在门户的左上角单击“+ 创建资源”，然后搜索“Azure Data Box”。 单击“Azure Data Box”。
    
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

  
5.  选择“Data Box 磁盘”。 单次订购的解决方案（包括 5 个磁盘）的最大容量为 35 TB。 可以创建多个订单，以增加数据大小。 

     ![选择 Data Box 磁盘选项](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  在“订单”中指定“订单详细信息”。 输入或选择以下信息。

    |设置|值|
    |---|---|
    |名称|提供友好名称用于跟踪订单。<br> 名称可以为 3 到 24 个字符，包括字母、数字和连字符。 <br> 名称必须以字母或数字开头和结尾。 |
    |资源组| 使用现有资源组，或创建新的资源组。 <br> 资源组是可以统一管理或部署的资源的逻辑容器。 |
    |目标 Azure 区域| 选择存储帐户的区域。<br> 目前，所有的美国、西欧和北欧区域都支持存储帐户。 |
    |存储帐户|根据指定的 Azure 区域，从现有存储帐户的筛选列表中进行选择。 <br>也可以创建新的常规用途 v1 或 v2 帐户。 |
    |估计数据大小(TB)| 输入估计值，以 TB 为单位。 <br>根据数据大小，Microsoft 会寄送适当数量的 8 TB SSD（7 TB 可用容量）。 <br>5 个磁盘的最大可用容量高达 35 TB。 |

13. 单击“下一步”。 

    ![提供订单详细信息](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. 在“寄送地址”选项卡中，提供你的名字和姓氏、公司的名称和邮政地址，以及有效的电话号码。 单击“验证地址”。 服务将验证寄送地址，以确定服务是否在该区域可用。 如果服务在指定的寄送地址可用，则会将结果通知给你。 

    ![提供寄送地址](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. 在“通知详细信息”中，指定电子邮件地址。 服务会将有关任何订单状态更新的电子邮件通知发送到指定的电子邮件地址。 

    我们建议使用组电子邮件，以便在组中的管理员离任后，可以持续收到通知。

16. 查看与订单、联系人、通知和隐私条款相关的信息**摘要**。 选中对应于同意隐私条款的复选框。

17. 单击“订购”。 创建订单需要几分钟时间。

 
## <a name="track-the-order"></a>跟踪订单

下单后，可以从 Azure 预览门户跟踪订单状态。 转到自己的订单，然后转到“概况”查看状态。 门户中会显示作业处于“已订购”状态。 

![“已订购”Data Box 磁盘状态](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

如果磁盘缺货，你会收到通知。 如果磁盘有货，Microsoft 会指定要发货的磁盘，并准备磁盘包装。 在磁盘准备期间，会执行以下操作：

- 使用 AES-128 BitLocker 加密技术将磁盘加密。  
- 锁定磁盘，防止有人未经授权访问磁盘。
- 在此过程中，将生成用于解锁磁盘的支持密钥。

磁盘准备完成后，门户会显示订单处于“已处理”状态。

然后，Microsoft 会安排区域承运人发运磁盘。 磁盘发货后，你会收到跟踪号。 门户会显示订单处于“已发运”状态。



## <a name="cancel-the-order"></a>取消订单

若要取消此订单，请在 Azure 预览门户中转到“概况”，然后在命令栏中单击“取消”。 

只能在订购磁盘后我们正在处理订单交货时取消订单。 一旦订单已处理，就再也不能取消订单。 

![取消订单](media/data-box-disk-deploy-ordered/cancel-order1.png)

若要删除已取消的订单，请转到“概况”，然后在命令栏中单击“删除”。 


## <a name="next-steps"></a>后续步骤

本教程介绍了有关 Azure Data Box 的主题，例如：

> [!div class="checklist"]
> * 注册 Data Box 磁盘
> * 订购 Data Box 磁盘
> * 跟踪订单
> * 取消订单

请继续学习下一篇教程，了解如何设置 Data Box 磁盘。

> [!div class="nextstepaction"]
> [设置 Azure Data Box 磁盘](./data-box-disk-deploy-set-up.md)


