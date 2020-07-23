---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 07/01/2020
ms.author: alkohli
ms.openlocfilehash: d099e33e7b35381f5404c9f8964d3ea90d4f3908
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959432"
---
最后一步是准备好要交付的设备。 在此步骤中，所有设备共享将会脱机。 启动此过程后，就无法访问共享。

> [!IMPORTANT]
> 必须进行交付准备，因为这样可以标记不符合 Azure 命名约定的数据。 此步骤可防止由于数据未确认而导致的潜在数据上传失败。

1. 转到“准备交付”并单击“开始准备”。  默认情况下，在复制数据时计算校验和。 准备交付步骤完成校验和计算并创建文件列表（ *-BOM 文件*）。 校验和计算可能需要数小时到数天的时间，具体取决于数据的大小。 
   
    ![准备交付 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    如果因故需要停止设备准备，请单击“停止准备”。 可以在以后继续准备交付。
        
    ![准备交付 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. 准备交付开始，设备共享进入脱机状态。 <!--You see a reminder to download the shipping label once the device is ready.--> 设备准备完成后，设备状态将更新为“交付准备就绪”，设备处于锁定状态。
        
    ![准备交付 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    若要将更多数据复制到设备，可以解锁设备，复制更多数据，然后再次运行“准备交付”操作。

    如果此步骤中出现错误，状态将更新为“扫描已完成，但有错误”。 解锁设备并转到“连接并复制”页，下载问题列表，然后解决错误。

    ![准备交付 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

    解决错误后，请运行“准备交付”。

4. 准备交付成功完成（没有错误）后，请执行以下步骤：

    1. 记下完成引用编号。 根据你所在的国家/地区，此编号可能是进行不同操作所必需的。
    2. 下载在此过程中复制的文件的列表（也称为清单）。 以后可以使用此列表来确认文件是否已上传到 Azure。 有关详细信息，请参阅[在准备交付期间检查 BOM 文件](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship)。
        
        ![准备交付 1](media/data-box-prepare-to-ship/prepare-to-ship5.png)
    3. 选择并下载设备的发货说明。 根据你所在的国家/地区，发货说明会有所不同。
    4. 如果电子墨水屏未显示发货标签，则可以在此处下载反向发货标签。 

5. 关闭设备。 转到“关闭或重启”页，单击“关闭”。  出现确认提示时，请单击“确定”以继续。

6. 拔下电缆。 下一步是将设备寄送到 Microsoft。
