---
title: 返回或替换 Azure Stack 边缘设备 |Microsoft Docs
description: 描述如何返回或替换 Azure Stack Edge 设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: dfd9c949767d3dc8e63e3f15ba12641f27d46b80
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569350"
---
# <a name="return-or-replace-your-azure-stack-edge-device"></a>返回或替换 Azure Stack 边缘设备

本文介绍如何擦除数据，然后返回 Azure Stack Edge 设备。 返回设备后，还可以删除与设备关联的资源，或订购更换设备。

在本文中，学习如何：

> [!div class="checklist"]
> * 擦除设备上的数据磁盘中的数据
> * 打开支持票证以返回设备
> * 打包设备并安排分拣
> * 删除 Azure 门户中的资源
> * 获取更换设备

## <a name="erase-data-from-the-device"></a>擦除设备上的数据

若要将数据从设备的数据磁盘中擦除，需要重置设备。 你可以使用本地 web UI 或 PowerShell 界面重置设备。

重置之前，请根据需要在设备上创建本地数据的副本。 可以将设备中的数据复制到 Azure 存储容器。

若要使用本地 web UI 重置设备，请执行以下步骤。

1. 在本地 web UI 中，请参阅**维护 > 设备重置**。
2. 选择 "**重置设备**"。

    ![重置设备](media/azure-stack-edge-return-device/device-reset-1.png)

3. 出现确认提示时，查看警告，然后选择 **"是"** 以继续。

    ![确认重置](media/azure-stack-edge-return-device/device-reset-2.png)  

Reset 会清除设备数据磁盘中的数据。 根据设备上的数据量，此过程大约需30-40 分钟。

或者，连接到设备的 PowerShell 接口，并使用`Reset-HcsAppliance` cmdlet 从数据磁盘中擦除数据。 有关详细信息，请参阅[重置设备](azure-stack-edge-connect-powershell-interface.md#reset-your-device)。

> [!NOTE]
> - 如果正在交换或升级到新设备，我们建议你在收到新设备后重置设备。
> - 设备重置仅删除设备上的所有本地数据。 云中的数据不会被删除，并会收取[费用](https://azure.microsoft.com/pricing/details/storage/)。 此数据需要使用云存储管理工具（如[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)）单独删除。

## <a name="open-a-support-ticket"></a>打开支持票证

若要开始返回过程，请执行以下步骤。

1. 使用 Microsoft 支持部门打开支持票证，表示你希望返回设备。 选择 "问题类型" 作为 " **Azure Stack 边缘硬件**"。

    ![创建支持票证](media/azure-stack-edge-return-device/open-support-ticket-1.png)  

2. Microsoft 支持部门工程师将与你联系。 提供寄送详细信息。
3. 如果需要退回寄盒，可以请求它。 对于问题回答 **"是"** ，**需要一个空的框返回**。


## <a name="schedule-a-pickup"></a>计划分拣

1. 关闭设备。 在本地 Web UI 中，转到“维护”>“电源设置”。****
2. 选择 "**关闭**"。 出现确认提示时，单击 **"是"** 以继续。 有关详细信息，请参阅[管理电源](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)。
3. 拔下电源线，并拔下设备的所有网络电缆。
4. 使用自己的 box 或从 Azure 接收到的空框来准备发货包。 将设备和设备附带的电源线置于包装盒中。
5. 将从 Azure 接收的装运标签添加到包中。
6. 安排区域快递公司取件。 如果返回美国的设备，则你的运营商可能是 UPS 或 FedEx。 使用 UPS 计划装货：

    1. 给本地 UPS 打电话（特定于国家/地区的免费电话号码）。
    2. 在调用中，将反向发货跟踪号括起来，如打印的标签所示。
    3. 如果跟踪号未加引号，则 UPS 会要求你在装货期间支付额外的费用。

    您还可以将 Azure Stack 边缘拖离最近的下拉位置，而不是计划分拣。

## <a name="delete-the-resource"></a>删除资源

在 Azure 数据中心接收到设备后，会检查设备是否损坏或任何篡改迹象。

- 如果设备的运行状况不佳，则该资源的计费指示器将停止。 Microsoft 支持部门将与你联系以确认返回了设备。 然后，你可以在 Azure 门户中删除与设备关联的资源。
- 如果设备出现严重损坏，可能会收取罚款。 有关详细信息，请参阅[丢失或损坏的设备](https://azure.microsoft.com/pricing/details/databox/edge/)和[产品条款的](https://www.microsoft.com/licensing/product-licensing/products)常见问题。  


可以在 Azure 门户中删除设备：
-    在您放置了订单后，在 Microsoft 准备好设备之前。
-    将设备返回给 Microsoft 后，它会通过 Azure 数据中心的物理检测，并 Microsoft 支持部门调用以确认返回了设备。

如果你已根据另一个订阅或位置激活了设备，Microsoft 将在一个工作日内将你的订单转移到新的订阅或位置。 移动订单后，可以删除此资源。


执行以下步骤以删除 Azure 门户中的设备和资源。

1. 在 Azure 门户中，请先前往资源，然后再切换到 "**概述**"。 在命令栏中，选择 "**删除**"。

    ![选择“删除”](media/azure-stack-edge-return-device/delete-resource-1.png)

2. 在 "**删除设备**" 边栏选项卡中，键入要删除的设备的名称，然后选择 "**删除**"。

    ![确认删除](media/azure-stack-edge-return-device/delete-resource-2.png)

设备和关联的资源成功删除后，会收到通知。

## <a name="get-a-replacement-device"></a>获取更换设备

如果现有设备有硬件故障或需要升级，则需要更换设备。 当设备出现硬件问题时，请执行以下步骤：

1. 提出[硬件问题的支持票证](#open-a-support-ticket)。 Microsoft 支持部门将确定字段替换单元（FRU）在此实例上不可用，或者设备需要硬件升级。 在这两种情况下，支持将对替换设备进行排序。
2. 为替代设备[创建新资源](azure-stack-edge-deploy-prep.md#create-a-new-resource)。 请确保选中此复选框，以**使其具有 Azure Stack Edge 设备**。 
3. 收到更换设备后，请根据新资源[安装](azure-stack-edge-deploy-install.md)和[激活](azure-stack-edge-deploy-connect-setup-activate.md)替代设备。
4. 按照所有步骤操作，返回原始设备：
    1. 打开另一个票证以返回原始设备。
    2. [擦除设备上的数据](#erase-data-from-the-device)。
    3. [计划分拣](#schedule-a-pickup)。
    5. 删除与返回的设备关联[的资源](#delete-the-resource)。



## <a name="next-steps"></a>后续步骤

- 了解如何[管理带宽](azure-stack-edge-manage-bandwidth-schedules.md)。
