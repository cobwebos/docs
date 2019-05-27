---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: ac708eb2ac79a74b8f4e09a7306a42665b3aca94
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159624"
---
#### <a name="to-delete-a-cloud-appliance"></a>删除云设备

1. 登录到 Azure 门户。
2. 只能删除不含数据的停用设备。 删除设备上的数据，也可[将卷容器中的数据故障转移到其他设备](../articles/storsimple/storsimple-8000-device-failover-cloud-appliance.md)。 删除数据后，即可停用设备。
3. 在 StorSimple 设备管理器服务页中单击“设备”，然后选择该设备。 右键单击并选择“停用”。
4. 停用设备以后，右键单击该设备，然后选择“删除”。

    ![选择停用的设备，然后单击“删除”](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance1.png)

5. 键入设备名称以确认删除。 删除此设备后，设备列表会更新。

    ![确认删除](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance2.png)

6. 设备删除后，将收到通知。

    ![成功删除设备的通知](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance4.png)

7. 设备列表会更新，指示设备已删除。

    ![已更新的设备列表](./media/storsimple-8000-delete-cloud-appliance/delete-cloud-appliance5.png)
