---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5a286753e438b7d65f3d33a82669c4f7e79a282
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86544471"
---
#### <a name="to-create-public-endpoints-on-the-cloud-appliance"></a>在云设备上创建公共终结点

1. 登录到 Azure 门户。
2. 转到“虚拟机”****，并选择并单击正用作云设备的虚拟机。
    
3. 需要创建一个网络安全组 (NSG) 规则来控制虚拟机内部和外部的通信流。 执行以下步骤来创建 NSG 规则。
    1. 选择“网络安全组”。****
        ![虚拟机页的屏幕截图。 在 "设置" 部分中，将突出显示 "网络安全组"。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt1.png)

    2. 单击所显示的网络安全组。
        ![网络安全组页的屏幕截图。 将突出显示默认网络安全组。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt2.png)

    3. 选择“入站安全规则”。
        ![页面屏幕截图，显示默认网络安全组的属性。 在导航窗格中，将突出显示 "入站安全规则"。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt3.png)

    4. 单击“+ 添加”以创建入站安全规则。****
        !["入站安全规则" 页的屏幕截图。 加号和单词 Add 彼此相邻，并突出显示。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt4.png)

        在“添加入站安全规则”边栏选项卡中：

        1. 对于 " **名称**"，请为终结点键入以下名称： WinRMHttps。
        
        2. 对于“优先级”，选择一个小于 1000（这是默认规则的优先级）的数字。**** 数字越大，优先级越低。

        3. 将“源”设置为“任何”。********

        4. 对于“服务”，选择“WinRM”。******** “协议”自动设置为“TCP”，“端口范围”设置为“5986”。****************

        5. 单击“确定”创建规则  。

            !["添加入站安全规则" 边栏选项卡的屏幕截图。 按照过程中所述填充值，并突出显示 "确定" 按钮。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt5.png)

4. 最后一步是将网络安全组与子网或特定网络接口相关联。 执行以下步骤来将网络安全组与某个子网相关联。
    1. 转到“子网”。****
    2. 单击“+ 关联”。****
        ![子网页的屏幕截图。 加号和单词 "关联" 彼此相邻，并突出显示。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt7.png)

    3. 选择虚拟网络，并选择相应的子网。
    4. 单击“确定”创建规则  。

        !["关联子网" 页的屏幕截图。 选择虚拟网络，并突出显示 "确定" 按钮。](./media/storsimple-8000-create-public-endpoints-cloud-appliance/sca-create-public-endpt11.png)

创建规则后，可查看其详细信息来确定公用虚拟 IP (VIP) 地址。 记下此地址。


