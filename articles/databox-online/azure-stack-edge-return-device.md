---
title: 退回或替换 Azure Stack Edge 设备 | Microsoft Docs
description: 介绍如何退回或替换 Azure Stack Edge 设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: bb73494dd5fe22c3be645f732f9d0958e48edb64
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743639"
---
# <a name="return-or-replace-your-azure-stack-edge-device"></a>退回或替换 Azure Stack Edge 设备

本文介绍如何擦除数据，然后退回 Azure Stack Edge 设备。 退回设备后，还可以删除与设备关联的资源，或订购替换设备。

在本文中，学习如何：

> [!div class="checklist"]
> * 擦除设备上数据磁盘中的数据
> * 打开支持票证以退回设备
> * 打包设备并安排取件
> * 删除 Azure 门户中的资源
> * 获取替换设备

## <a name="erase-data-from-the-device"></a>清除设备上的数据

若要擦除设备上数据磁盘中的数据，需要重置设备。 可使用本地 Web UI 或 PowerShell 界面重置设备。

重置之前，请根据需要创建设备上本地数据的副本。 可将设备中的数据复制到 Azure 存储容器。

若要使用本地 Web UI 重置设备，请执行以下步骤。

1. 在本地 Web UI 中，转到“维护”>“设备重置”。
2. 选择“重置设备”。

    ![重置设备](media/azure-stack-edge-return-device/device-reset-1.png)

3. 出现确认提示时，查看警告，并选择“是”以继续。

    ![确认重置](media/azure-stack-edge-return-device/device-reset-2.png)  

重置会清除设备数据磁盘中的数据。 此过程大约需要 30-40 分钟，具体取决于设备上的数据量。

或者，连接到设备的 PowerShell 界面，使用 `Reset-HcsAppliance` cmdlet 清除数据磁盘中的数据。 有关详细信息，请参阅[重置设备](azure-stack-edge-connect-powershell-interface.md#reset-your-device)。

> [!NOTE]
> - 如果要更换或升级为新设备，建议仅在收到新设备后才重置自己的设备。
> - 设备重置仅删除设备上的所有本地数据。 云中的数据不会被删除，并且会[收费](https://azure.microsoft.com/pricing/details/storage/)。 此数据需要使用云存储空间管理工具（如 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)）单独删除。

## <a name="open-a-support-ticket"></a>打开支持票证

若要开始退回过程，请执行以下步骤。

1. 向 Microsoft 支持部门打开支持票证，表示你希望退回设备。 对于问题类型，选择“Azure Stack Edge 硬件”。

    ![创建支持票证](media/azure-stack-edge-return-device/open-support-ticket-1.png)  

2. Microsoft 支持部门的工程师将与你联系。 提供发货详细信息。
3. 如果需要退货箱，可以发送请求。 对于问题“需要一个空箱进行退货”，回答“是” 。


## <a name="schedule-a-pickup"></a>安排取件

1. 关闭设备。 在本地 Web UI 中，转到“维护”>“电源设置”。
2. 选择“关闭”。 出现确认提示时，请单击“是”以继续。 有关详细信息，请参阅[管理电源](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)。
3. 拔下电源线，并拔下设备的所有网络电缆。
4. 准备发货包装：使用自己的箱子或从 Azure 收到的空箱子。 将设备和设备附带的电源线放置在箱中。
5. 将从 Azure 收到的发货标签贴到包装上。
6. 安排区域快递公司取件。 如果在美国退回设备，则快递公司可能是 UPS 或 FedEx。 安排 UPS 取件：

    1. 给本地 UPS 打电话（特定于国家/地区的免费电话号码）。
    2. 在通话中，引用反向装运跟踪号码，如打印的标签上所示。
    3. 如果未引用跟踪号码，UPS 将在取件时要求你支付额外费用。

    如果不安排取件，也可以在最近的卸货位置放置 Azure Stack Edge。

## <a name="delete-the-resource"></a>删除资源

在 Azure 数据中心接收到设备后，会检查设备是否损坏或是否有任何篡改迹象。

- 如果设备到达时完好无损，则该资源的计费指示器将停止。 Microsoft 支持部门将与你联系以确认设备已退回。 然后，你可以在 Azure 门户中删除与该设备关联的资源。
- 如果设备严重损坏，可能会收取罚款。 有关详细信息，请参阅[有关设备丢失或损坏的 FAQ](https://azure.microsoft.com/pricing/details/databox/edge/) 和[产品服务条款](https://www.microsoft.com/licensing/product-licensing/products)。  


可以在以下时段从 Azure 门户删除设备：
-    下订单后，在 Microsoft 准备好设备之前。
-    将设备退回给 Microsoft，设备通过 Azure 数据中心的物理检测，并且 Microsoft 支持部门打电话确认已退回设备之后。

如果你已针对其他订阅或位置激活了设备，Microsoft 将在一个工作日内将你的订单转移到新的订阅或位置。 移动订单后，可以删除此资源。


执行以下步骤以删除 Azure 门户中的设备和资源。

1. 在 Azure 门户中，转到资源，然后转到“概述”。 在命令栏中，选择“删除”。

    ![选择“删除”](media/azure-stack-edge-return-device/delete-resource-1.png)

2. 在“删除设备”边栏选项卡中，键入要删除的设备的名称，然后选择“删除” 。

    ![确认删除](media/azure-stack-edge-return-device/delete-resource-2.png)

设备和关联的资源成功删除后，你会收到通知。

## <a name="get-a-replacement-device"></a>获取替换设备

如果现有设备有硬件故障或需要升级，则需要替换设备。 当设备出现硬件问题时，请执行以下步骤：

1. [针对硬件问题打开支持票证](#open-a-support-ticket)。 Microsoft 支持部门将确定现场可替换装置 (FRU) 不适用于此情况，或设备需要硬件升级。 无论是哪种情况，支持部门都将订购替换设备。
2. 为替换设备[创建新资源](azure-stack-edge-deploy-prep.md#create-a-new-resource)。 请确保选中“我有 Azure Stack Edge 设备”对应的复选框。 
3. 收到替换设备后，[安装](azure-stack-edge-deploy-install.md)替换设备并针对新资源对其进行[激活](azure-stack-edge-deploy-connect-setup-activate.md)。
4. 按照所有步骤操作，退回原始设备：
    1. 打开另一个票证以退回原始设备。
    2. [清除设备上的数据](#erase-data-from-the-device)。
    3. [安排取件](#schedule-a-pickup)。
    5. [删除](#delete-the-resource)与退回的设备相关联的资源。



## <a name="next-steps"></a>后续步骤

- 了解如何[管理带宽](azure-stack-edge-manage-bandwidth-schedules.md)。
