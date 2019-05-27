---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66161189"
---
1. [连接到 PowerShell 界面](#connect-to-the-powershell-interface)。
2. 使用`Get-HcsApplianceInfo`若要获取你的设备的信息。

    下面的示例显示了此 cmdlet 的用法：

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    以下是表汇总一些重要的设备信息：
    
    | 参数                             | 描述                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | 部署设备期间通过本地 web UI 配置的设备友好名称。 默认的友好名称为设备序列号。  |   |
    | 序列号                   | 设备序列号是出厂分配的唯一编号。                                                                             |   |
    | 模型                          | 数据框的边缘或数据框网关设备的模型。 该模型是数据框网关的虚拟和物理数据框的边缘。                   |   |
    | FriendlySoftwareVersion        | 对应于设备软件版本的友好字符串。 对于运行预览版的系统，友好的软件版本为数据框边缘 1902年。 |   |
    | HcsVersion                     | 设备上运行的 HCS 软件版本。 例如，对应于数据框边缘 1902年的 HCS 软件版本是 1.4.771.324。            |   |
    | LocalCapacityInMb              | 单位为兆位的设备的本地总容量。                                                                                                        |   |
    | IsRegistered                   | 此值指示是否与服务激活你的设备。                                                                                         |   |


