---
title: 管理 Azure Data Box Gateway 上的带宽计划 | Microsoft Docs
description: 介绍如何使用 Azure 门户管理 Azure Data Box Gateway 上的带宽计划。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: feae75b1fbb832f833a01fa20ce07962ab5c484f
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82561414"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-gateway"></a>使用 Azure 门户管理 Azure Data Box Gateway 上的带宽计划  

本文介绍如何在 Azure Data Box Gateway 中管理用户。 使用带宽计划可以根据多个日期时间计划配置网络带宽用量。 可将这些计划应用到在设备与云之间执行的上传和下载操作。 

可以通过 Azure 门户添加、修改或删除 Data Box Gateway 的带宽计划。

在本文中，学习如何：

> [!div class="checklist"]
> * 添加计划
> * 修改计划
> * 删除计划 


## <a name="add-a-schedule"></a>添加计划

在 Azure 门户中执行以下步骤可以添加用户。

1. 在 Data Box Gateway 资源的 Azure 门户中，转到“带宽”。****
2. 在右窗格中，单击“+ 添加计划”****。

    ![单击“添加用户”](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-1.png)

3. 在“添加计划”中：**** 

   1. 提供计划的“开始日期”、“结束日期”、“开始时间”和“结束时间”。**************** 
   2. 如果此计划全天运行，可以选中“全天”选项。**** 
   3. “带宽速率”是指设备在执行涉及到云的操作时使用的带宽，以每秒兆位数 (Mbps) 表示。**** 为此字段提供介于 1 和 1,000 之间的数字。 
   4. 如果不希望限制数据上传和下载带宽，请选中“无限制”。**** 
   5. 单击“添加”  。

      ![单击“添加用户”](media/data-box-gateway-manage-bandwidth-schedules/add-schedule-2.png)

3. 此时会使用指定的参数创建一个计划。 此计划随后会显示在门户中的带宽计划列表中。


## <a name="edit-schedule"></a>编辑计划

执行以下步骤可以编辑带宽计划。 

1. 在 Azure 门户中，转到自己的 Data Box Gateway 资源，然后转到“带宽”。 
2. 在带宽计划列表中，选择并单击要修改的计划。
    ![修改用户](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-1.png)

3. 进行所需的更改并保存更改。

    ![修改用户](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-2.png)

4. 修改计划后，计划列表将会更新，以反映修改后的计划。

    ![修改用户](media/data-box-gateway-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>删除计划

执行以下步骤可以删除与 Data Box Gateway 设备关联的带宽计划。

1. 在 Azure 门户中，请切换到 Data Box Gateway 资源，然后再中转到 "**带宽**"。  

2. 在带宽计划列表中，选择要删除的计划。 单击右键以显示上下文菜单，然后单击“删除”。**** 

   ![删除用户](media/data-box-gateway-manage-bandwidth-schedules/delete-schedule-1.png)

3.  删除计划后，计划列表将会更新。



## <a name="next-steps"></a>后续步骤

- 了解如何[管理带宽](data-box-gateway-manage-bandwidth-schedules.md)。 
