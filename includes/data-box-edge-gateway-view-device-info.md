---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "67173410"
---
1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 使用`Get-HcsApplianceInfo`获取设备的信息。

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

    下表总结了一些重要的设备信息：
    
    | 参数                             | 描述                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | 设备部署期间通过本地 Web UI 配置的设备友好名称。 默认的友好名称为设备序列号。  |   |
    | SerialNumber                   | 设备序列号是在工厂分配的唯一编号。                                                                             |   |
    | “模型”                          | 数据框边缘或数据框网关设备的模型。 该模型对于数据框网关是虚拟的，对于数据框边缘是物理的。                   |   |
    | FriendlySoftwareVersion        | 对应于设备软件版本的友好字符串。 对于运行预览的系统，友好的软件版本将是数据框边缘 1902。 |   |
    | HcsVersion                     | 设备上运行的 HCS 软件版本。 例如，与数据框边缘 1902 对应的 HCS 软件版本是 1.4.771.324。            |   |
    | 本地容量InMb              | 以兆位表示设备的总本地容量。                                                                                                        |   |
    | 已注册                   | 此值指示您的设备是否已随服务激活。                                                                                         |   |


