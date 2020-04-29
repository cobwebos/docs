---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "67173410"
---
1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. `Get-HcsApplianceInfo`使用获取设备的信息。

    以下示例显示了此 cmdlet 的用法：

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

    下面是汇总一些重要设备信息的表：
    
    | 参数                             | 说明                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | FriendlyName                   | 设备部署过程中通过本地 web UI 配置的设备的友好名称。 默认的友好名称为设备序列号。  |   |
    | SerialNumber                   | 设备序列号是在工厂中分配的唯一编号。                                                                             |   |
    | 型号                          | Data Box Edge 或 Data Box Gateway 设备的模型。 模型是 Data Box Gateway 的虚拟的，物理 Data Box Edge。                   |   |
    | FriendlySoftwareVersion        | 对应于设备软件版本的友好字符串。 对于运行预览版的系统，友好软件版本将为 1902 Data Box Edge。 |   |
    | HcsVersion                     | 设备上运行的 HCS 软件版本。 例如，与 Data Box Edge 1902 对应的 HCS 软件版本为1.4.771.324。            |   |
    | LocalCapacityInMb              | 设备的总本地容量（以 Mb 为单位）。                                                                                                        |   |
    | IsRegistered                   | 此值用于指示是否将设备与服务一起激活。                                                                                         |   |


