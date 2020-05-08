---
title: Azure Stack Edge 管理带宽计划 |Microsoft Docs
description: 描述如何使用 Azure 门户管理 Azure Stack 边缘的带宽计划。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: a21828413b8d8750827fafa077002863439f8643
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569584"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge"></a>使用 Azure 门户管理 Azure Stack 边缘的带宽计划  

本文介绍如何在 Azure Stack 边缘上管理用户。 使用带宽计划可以根据多个日期时间计划配置网络带宽用量。 可将这些计划应用到在设备与云之间执行的上传和下载操作。

可以通过 Azure 门户添加、修改或删除 Azure Stack 边缘的带宽计划。

在本文中，学习如何：

> [!div class="checklist"]
> * 添加计划
> * 修改计划
> * 删除计划


## <a name="add-a-schedule"></a>添加计划

在 Azure 门户中执行以下步骤可以添加计划。

1. 在 Azure Stack Edge 资源的 Azure 门户中，请参阅 "**带宽**"。
2. 在右窗格中，选择“+ 添加计划”****。

    ![选择带宽](media/azure-stack-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. 在“添加计划”中：**** 

   1. 提供计划的“开始日期”、“结束日期”、“开始时间”和“结束时间”。****************
   2. 如果此计划应该全天运行，请选中“全天”选项。****
   3. “带宽速率”是指设备在执行涉及到云的操作时使用的带宽，以每秒兆位数 (Mbps) 表示。**** 为此字段提供介于 20 和 1,000,000,007 之间的数字。
   4. 如果不希望限制数据上传和下载带宽，请选中“无限制”。****
   5. 选择“添加”  。

      ![添加计划](media/azure-stack-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. 此时会使用指定的参数创建一个计划。 此计划随后会显示在门户中的带宽计划列表中。

    ![更新的带宽计划列表](media/azure-stack-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>编辑计划

执行以下步骤可以编辑带宽计划。

1. 在 Azure 门户中，请切换到 Azure Stack Edge 资源，然后再中转到 "**带宽**"。 
2. 在带宽计划列表中，选择要修改的计划。
    ![选择带宽计划](media/azure-stack-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. 进行所需的更改并保存更改。

    ![修改用户](media/azure-stack-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. 修改计划后，计划列表将会更新，以反映修改后的计划。

    ![修改用户](media/azure-stack-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>删除计划

执行以下步骤以删除与 Azure Stack Edge 设备关联的带宽计划。

1. 在 Azure 门户中，请切换到 Azure Stack Edge 资源，然后再中转到 "**带宽**"。  

2. 在带宽计划列表中，选择要删除的计划。 在“编辑计划”中****，选择“删除”****。 出现确认提示时，选择“是”****。

   ![删除用户](media/azure-stack-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. 删除计划后，计划列表将会更新。


## <a name="next-steps"></a>后续步骤

- 了解如何[管理共享](azure-stack-edge-manage-shares.md)。
