---
title: 返回或替换 Azure 数据框边缘设备 |微软文档
description: 描述如何返回或替换 Azure 数据框边缘设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/26/2020
ms.author: alkohli
ms.openlocfilehash: 521277b2eed7edfba016f6a80e8f877decfb0ac5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651094"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>返回或替换 Azure 数据框边缘设备

本文介绍如何擦除数据，然后返回 Azure 数据框边缘设备。 返回设备后，还可以删除与设备关联的资源或订购替换设备。

在本文中，学习如何：

> [!div class="checklist"]
> * 从设备上的数据磁盘上擦除数据
> * 打开支持票证以退回设备
> * 打包设备并安排取件
> * 删除 Azure 门户中的资源
> * 获取更换设备

## <a name="erase-data-from-the-device"></a>从设备擦除数据

要擦除设备数据磁盘上的数据，您需要重置设备。 您可以使用本地 Web UI 或 PowerShell 接口重置设备。

重置之前，如果需要，请创建设备上本地数据的副本。 可以将数据从设备复制到 Azure 存储容器。

要使用本地 Web UI 重置设备，请执行以下步骤。

1. 在本地 Web UI 中，转到**维护>设备重置**。
2. 选择**重置设备**。

    ![重置设备](media/data-box-edge-return-device/device-reset-1.png)

3. 当提示确认时，查看警告并选择 **"是**"以继续。

    ![确认重置](media/data-box-edge-return-device/device-reset-2.png)  

重置会擦除设备数据磁盘上的数据。 根据设备上的数据量，此过程大约需要 30-40 分钟。

或者，连接到设备的 PowerShell 接口，`Reset-HcsAppliance`并使用 cmdlet 从数据磁盘擦除数据。 有关详细信息，请参阅[重置设备](data-box-edge-connect-powershell-interface.md#reset-your-device)。

> [!NOTE]
> - 如果要交换或升级到新设备，我们建议您仅在收到新设备后重置设备。
> - 设备重置仅删除设备中的所有本地数据。 云中的数据不会被删除并收取[费用](https://azure.microsoft.com/pricing/details/storage/)。 此数据需要使用[Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)等云存储管理工具单独删除。

## <a name="open-a-support-ticket"></a>打开支持票证

要开始返回过程，请执行以下步骤。

1. 使用 Microsoft 支持打开支持票证，指示您希望返回设备。 选择问题类型作为**数据框边缘硬件**。

    ![创建支持票证](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. 微软支持工程师将与您联系。 提供运输详细信息。
3. 如果您需要退货运输箱，您可以请求它。 回答**是**，问题**需要一个空框返回**。


## <a name="schedule-a-pickup"></a>安排取件

1. 关闭设备。 在本地 Web UI 中，转到“维护”>“电源设置”。****
2. 选择 **"关闭**"。 当提示确认时，单击"**是**"以继续。 有关详细信息，请参阅[管理电源](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)。
3. 拔下电源线，然后从设备上取下所有网络电缆。
4. 使用自己的框或从 Azure 收到的空框准备装运包。 将设备随设备随附的电源线放入包装盒中。
5. 在包上粘贴从 Azure 收到的发货标签。
6. 安排区域快递公司取件。 如果在美国退回设备，您的运营商可能是 UPS 或 FedEx。 要安排 UPS 的取件：

    1. 给本地 UPS 打电话（特定于国家/地区的免费电话号码）。
    2. 在通话中，请引用打印标签上所示的反向货件跟踪号码。
    3. 如果未引用追踪号码，UPS 将要求您在取件期间支付额外费用。

    您还可以将数据框边缘放在最近的放置位置，而不是安排取件。

## <a name="delete-the-resource"></a>删除资源

在 Azure 数据中心接收设备后，将检查设备是否有损坏或任何篡改迹象。

- 如果设备完好无损地到达，并且状况良好，则该资源的计费表将停止。 Microsoft 支持部门将与您联系以确认设备已退回。 然后，您可以在 Azure 门户中删除与设备关联的资源。
- 如果设备严重损坏，可能会处以罚款。 有关详细信息，请参阅[有关丢失或损坏的设备](https://azure.microsoft.com/pricing/details/databox/edge/)以及[产品服务条款的](https://www.microsoft.com/licensing/product-licensing/products)常见问题解答。  


您可以在 Azure 门户中删除设备：
-   下订单后，在设备由 Microsoft 准备之前。
-   将设备返回到 Microsoft 后，它将在 Azure 数据中心通过物理检查，然后通过 Microsoft 支持呼叫以确认设备已返回。

如果您已针对其他订阅或位置激活设备，Microsoft 将在一个工作日内将您的订单移动到新订阅或位置。 移动订单后，可以删除此资源。


执行以下步骤删除 Azure 门户中的设备和资源。

1. 在 Azure 门户中，转到资源，然后转到 **"概述**"。 从命令栏中，选择 **"删除**"。

    ![选择“删除”](media/data-box-edge-return-device/delete-resource-1.png)

2. 在 **"删除设备"** 边栏选项卡中，键入要删除的设备的名称，然后选择 **"删除**"。

    ![确认删除](media/data-box-edge-return-device/delete-resource-2.png)

设备并成功删除关联的资源后，您将收到通知。

## <a name="get-a-replacement-device"></a>获取更换设备

当现有设备出现硬件故障或需要升级时，需要更换设备。 当设备出现硬件问题时，请执行以下步骤：

1. [打开硬件问题的支持票证](#open-a-support-ticket)。 Microsoft 支持将确定现场替换单元 （FRU） 不适用于此实例或设备需要硬件升级。 在这两种情况下，支持将订购更换设备。
2. [为替换设备创建新资源](data-box-edge-deploy-prep.md#create-a-new-resource)。 请确保选择复选框反对**我有一个数据框边缘设备**。 
3. 收到更换设备后，根据新资源[安装](data-box-edge-deploy-install.md)并[激活](data-box-edge-deploy-connect-setup-activate.md)更换设备。
4. 按照所有步骤返回原始设备：
    1. 打开另一张票证以返回原始设备。
    2. [擦除设备上的数据](#erase-data-from-the-device)。
    3. [安排取件](#schedule-a-pickup)。
    5. [删除与](#delete-the-resource)返回的设备关联的资源。



## <a name="next-steps"></a>后续步骤

- 了解如何[管理带宽](data-box-edge-manage-bandwidth-schedules.md)。
