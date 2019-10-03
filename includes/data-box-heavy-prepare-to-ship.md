---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/28/2019
ms.author: alkohli
ms.openlocfilehash: f5b60d862be0d71f71f770c47d88ad39f2fc6ac7
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "66419955"
---
最后一步是准备好要交付的设备。 在此步骤中，所有设备共享将会脱机。 启动此过程后，就无法访问共享。

> [!IMPORTANT]
> 必须进行交付准备，因为这样可以标记不符合 Azure 命名约定的数据。 跳过这一步可能导致数据上传因数据未确认而失败。

1. 转到“准备交付”并单击“开始准备”。   默认情况下，在复制数据时计算校验和。 准备交付步骤完成校验和计算并创建文件列表（也称为 *BOM 文件*或清单）。 校验和计算可能需要数小时到数天的时间，具体取决于数据的大小。
   
    ![准备交付 1](media/data-box-heavy-prepare-to-ship/prepare-to-ship1.png)

    如果因故需要停止设备准备，请单击“停止准备”。  可以在以后继续准备交付。
        
    ![准备交付 2](media/data-box-heavy-prepare-to-ship/prepare-to-ship2.png)
    
2. 准备交付开始，设备共享进入脱机状态。 设备就绪以后，会出现一个要求你下载发货标签的提醒。

    ![“下载发货标签”提醒](media/data-box-heavy-prepare-to-ship/download-shipping-label-reminder.png)

3. 设备准备完成后，设备状态将更新为“交付准备就绪”，设备处于锁定状态。 
        
    ![准备交付 3](media/data-box-heavy-prepare-to-ship/prepare-to-ship3.png)

    若要将更多数据复制到设备，可以解锁设备，复制更多数据，然后再次运行“准备交付”操作。

    如果此步骤出现错误，请下载错误日志并解决错误。 解决错误后，请运行“准备交付”。 

4. “准备交付”操作成功完成（没有错误）以后，请下载在此过程中复制的文件的列表（也称为 *BOM 文件*或清单）。 

    ![下载文件列表或 BOM 文件](media/data-box-heavy-prepare-to-ship/download-list-of-files.png)

   以后可以使用此列表来确认文件是否已上传到 Azure。 有关详细信息，请参阅[在准备交付期间检查 BOM 文件](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship)。
        
    ![示例 BOM 文件](media/data-box-heavy-prepare-to-ship/sample-bom-file.png)

5. 关闭设备。 转到“关闭或重启”页，单击“关闭”。   出现确认提示时，请单击“确定”以继续  。

    ![关闭第一个设备节点](media/data-box-heavy-prepare-to-ship/shut-down-device-node.png)

6. 对其他设备节点重复上述所有步骤。
7. 设备完全关闭后，设备背面的所有 LED 都会熄灭。 下一步是拔下所有电缆并将设备寄送到 Microsoft。
