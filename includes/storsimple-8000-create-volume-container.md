---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 467af776af95cf035121250fdcadd2fee65d9805
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "67172995"
---
#### <a name="to-create-a-volume-container"></a>创建卷容器
1. 转到 StorSimple Device Manager 服务并单击“设备”。**** 从设备的表格式列表中，选择并单击某个设备。 

    ![卷容器边栏选项卡](./media/storsimple-8000-create-volume-container/createvolumecontainer1.png)

2. 在设备仪表板中，单击“+ 添加卷容器”。****

    ![卷容器边栏选项卡](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

3. 在“添加卷容器”**** 边栏选项卡中：
   
   1. 将自动选择设备。
   2. 提供卷容器的“名称”****。 名称长度必须为 3 到 32 个字符。 创建卷容器后，无法重命名卷容器。
   3. 选择“启用云存储加密” **** 启用从设备发送到云中的数据加密。
   4. 提供并确认长度在 8 到 32 个字符之间的 **“云存储加密密钥”**。 设备使用此密钥访问加密的数据。
   5. 选择与该卷容器关联的 **“存储帐户”**。 可以选择现有存储帐户或选择在创建服务时生成的默认帐户。 还可以使用 **“添加新帐户”** 选项，指定未与此服务订阅链接的存储帐户。
   6. 如果你希望使用所有可用带宽，请在 **“指定带宽”** 下拉列表中选择 **“无限制”**。 还可以将此选项设置为“自定义”**** 以进行带宽控制，并指定一个介于 1 Mbps 和 1000 Mbps 之间的值。
      如果你已获取带宽使用信息，则可以通过指定“选择带宽模板”**** 来根据计划分配带宽。 有关分步过程，请转到 [添加带宽模板](../articles/storsimple/storsimple-8000-manage-bandwidth-templates.md#add-a-bandwidth-template)。

      ![卷容器边栏选项卡](./media/storsimple-8000-create-volume-container/createvolumecontainer6b.png)
   7. 单击“创建”。 

        ![卷容器边栏选项卡](./media/storsimple-8000-create-volume-container/createvolumecontainer6.png)
   
       成功创建卷容器后，会收到通知。

       ![卷容器创建通知](./media/storsimple-8000-create-volume-container/createvolumecontainer8.png)

   新创建的卷容器会在设备的卷容器列表中列出。

   ![“添加卷容器”边栏选项卡](./media/storsimple-8000-create-volume-container/createvolumecontainer9.png)


