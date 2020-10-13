---
title: 返回 Azure Stack Edge Pro 设备 |Microsoft Docs
description: 了解如何擦除数据并返回 Azure Stack Edge Pro 设备，并删除与设备关联的资源。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/27/2020
ms.author: alkohli
ms.openlocfilehash: 730774f33d43754d4ca198ed170159fa4f872e09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903671"
---
# <a name="return-your-azure-stack-edge-pro-device"></a>返回 Azure Stack Edge Pro 设备

本文介绍如何擦除数据，然后返回 Azure Stack Edge Pro 设备。 返回设备后，还可以删除与设备关联的资源。

在本文中，学习如何：

> [!div class="checklist"]
>
> * 擦除设备上数据磁盘中的数据
> * 在 Azure 门户中开始设备退回
> * 打包设备并安排取件
> * 删除 Azure 门户中的资源

## <a name="erase-data-from-the-device"></a>清除设备上的数据

若要擦除设备上数据磁盘中的数据，需要重置设备。 可使用本地 Web UI 或 PowerShell 界面重置设备。

重置之前，请根据需要创建设备上本地数据的副本。 可将设备中的数据复制到 Azure 存储容器。

即使在重置设备之前，也可以重新启动设备。 

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

## <a name="initiate-device-return"></a>启动设备返回

若要开始退回过程，请执行以下步骤。

1. 在 Azure 门户中转到 Azure Stack Edge Pro/Data Box Gateway 资源。 在 " **概述**" 中，在右窗格中转到命令栏，然后选择 " **返回设备**"。 

    ![返回设备1](media/azure-stack-edge-return-device/return-device-1.png)  

2. 在 " **返回设备** " 边栏选项卡的 " **基本详细信息**" 下：

    1. 提供设备的序列号。 若要获取设备序列号，请转到设备的本地 web UI，然后转到 " **概述**"。  
    
    ![设备序列号1](media/azure-stack-edge-return-device/device-serial-number-1.png) 

    2. 输入服务标记编号，该编号是设备独有的五个或更多字符标识符。 当你面对设备) 时，服务标记位于设备的右下角 (。 拖出信息标记 (是) 的 "滑出标签" 面板。 此面板包含系统信息，如服务标记、NIC、MAC 地址等。 
    
    ![服务标记号1](media/azure-stack-edge-return-device/service-tag-number-1.png)

    3. 从下拉列表中，选择返回的原因。

    ![返回设备2](media/azure-stack-edge-return-device/return-device-2.png) 

3. 在 " **发货详细信息**" 下：

    1. 提供姓名、公司名称和完整公司地址。 输入包含区号的工作电话和通知的电子邮件 ID。
    2. 如果需要退货箱，可以发送请求。 对于问题“需要一个空箱进行退货”，回答“是” 。

    ![返回设备3](media/azure-stack-edge-return-device/return-device-3.png)

4. 查看 **隐私条款** ，并根据说明选中此复选框，并同意隐私条款。

5. 选择 " **启动返回**"。

    ![返回设备4](media/azure-stack-edge-return-device/return-device-4.png) 

6. 捕获设备返回详细信息后，可以通过电子邮件通知 Azure Stack Edge Pro 运营团队。 你可以使用电子邮件应用程序，前提是已安装并配置了电子邮件应用程序。 还可以复制数据来创建和发送电子邮件。

    ![返回设备5](media/azure-stack-edge-return-device/return-device-5.png) 

7. Azure Stack Edge Pro 运营团队收到电子邮件后，他们会向你发送一个反转发货标签。 当你收到此标签时，你可以通过电信公司计划设备装货。 

## <a name="schedule-a-pickup"></a>安排取件

若要计划分拣，请执行以下步骤。

1. 关闭设备。 在本地 Web UI 中，转到“维护”>“电源设置”。
2. 选择“关闭”。 出现确认提示时，请单击“是”以继续。 有关详细信息，请参阅[管理电源](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)。
3. 拔下电源线，并拔下设备的所有网络电缆。
4. 准备发货包装：使用自己的箱子或从 Azure 收到的空箱子。 将设备和设备附带的电源线放置在箱中。
5. 将从 Azure 收到的发货标签贴到包装上。
6. 安排区域快递公司取件。 如果在美国退回设备，则快递公司可能是 UPS 或 FedEx。 安排 UPS 取件：

    1. 给本地 UPS 打电话（特定于国家/地区的免费电话号码）。
    2. 在通话中，引用反向装运跟踪号码，如打印的标签上所示。
    3. 如果未引用跟踪号码，UPS 将在取件时要求你支付额外费用。

    你还可以将 Azure Stack Edge Pro 置于最近的放置位置，而不是计划分拣。

## <a name="delete-the-resource"></a>删除资源

在 Azure 数据中心接收到设备后，会检查设备是否损坏或是否有任何篡改迹象。

- 如果设备完好无损并处于良好的形状，则该资源的计费指示器将停止。 Azure Stack Edge Pro 运营团队将与你联系，以确认返回了设备。 然后，你可以在 Azure 门户中删除与该设备关联的资源。
- 如果设备严重损坏，可能会收取罚款。 有关详细信息，请参阅[有关设备丢失或损坏的 FAQ](https://azure.microsoft.com/pricing/details/databox/edge/) 和[产品服务条款](https://www.microsoft.com/licensing/product-licensing/products)。  


可以在以下时段从 Azure 门户删除设备：

- 下订单后，在 Microsoft 准备好设备之前。
- 将设备返回给 Microsoft 后，它会通过 Azure 数据中心的物理检测，并 Azure Stack Edge Pro 操作团队调用以确认是否返回了设备。

如果你已针对其他订阅或位置激活了设备，Microsoft 将在一个工作日内将你的订单转移到新的订阅或位置。 移动订单后，可以删除此资源。


执行以下步骤以删除 Azure 门户中的设备和资源。

1. 在 Azure 门户中，转到资源，然后转到“概述”。 在命令栏中，选择“删除”。

    ![选择“删除”](media/azure-stack-edge-return-device/delete-resource-1.png)

2. 在“删除设备”边栏选项卡中，键入要删除的设备的名称，然后选择“删除” 。

    ![确认删除](media/azure-stack-edge-return-device/delete-resource-2.png)

设备和关联的资源成功删除后，你会收到通知。


## <a name="next-steps"></a>后续步骤

- 了解如何 [获取 Azure Stack Edge Pro 设备的替换](azure-stack-edge-replace-device.md)。
