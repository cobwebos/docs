---
title: Azure 策略支持的 VM Sku
description: 本文介绍了备份提供的内置 Azure 策略支持的 VM Sku （由发布者、映像服务和映像 SKU 提供）
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980109"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Azure 策略支持的 VM Sku

Azure 备份提供了一个可分配给**订阅或资源组中指定位置的所有 Azure vm**的内置策略（使用 Azure 策略）。 将此策略分配到给定作用域时，会自动将该作用域中创建的所有新 Vm 配置为备份到**同一位置和订阅中的现有保管库**。 下表列出了此策略支持的所有 VM Sku。

### <a name="supported-vms"></a>**支持的 Vm**

**策略名称：** 在同一位置的现有中央保管库中配置某个位置的 Vm 备份

映像发布者 | 映像提供 | 映像 SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 （2008-SP1）
MicrosoftWindowsServer | WindowsServer | [smalldisk]Windows Server 2008 R2 SP （2008-R2-SP1-smalldisk）
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Datacenter （2012-Datacenter）
MicrosoftWindowsServer | WindowsServer | [smalldisk]Windows Server 2012 Datacenter （2012-Datacenter-smalldisk）
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter （2012-R2-Datacenter）
MicrosoftWindowsServer | WindowsServer | [smalldisk]Windows Server 2012 R2 Datacenter （2012-R2-smalldisk）
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter （2016-Datacenter）
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter-Server Core （2016-Datacenter-Server Core）
MicrosoftWindowsServer | WindowsServer | [smalldisk]Windows Server 2016 Datacenter-Server Core （2016-smalldisk）
MicrosoftWindowsServer | WindowsServer | [smalldisk]Windows Server 2016 Datacenter （2016-Datacenter-smalldisk）
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core with 容器（2016-含容器的数据中心）
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 远程桌面会话主机2016（2016-具有-RDSH 的数据中心）
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter （2019-Datacenter）
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core （2019-Datacenter-内核）
MicrosoftWindowsServer | WindowsServer | [smalldisk]Windows Server 2019 Datacenter Server Core （2019-smalldisk）
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core with 容器（2019-Datacenter-with 容器）
MicrosoftWindowsServer | WindowsServer | [smalldisk]Windows Server 2019 Datacenter Server Core with 容器（2019-smalldisk）
MicrosoftWindowsServer | WindowsServer | [smalldisk]Windows Server 2019 Datacenter （2019-Datacenter-smalldisk）
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter with 容器（2019-含容器的数据中心）
MicrosoftWindowsServer | WindowsServer | [smalldisk]Windows Server 2019 Datacenter with 容器（2019-smalldisk）
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter （zh-chs）（2019-zhcn）
MicrosoftWindowsServer | WindowsServerSemiAnnual | 数据中心-核心-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-核心-1709-with 容器-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-核心-1803-with 容器-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 所有映像 Sku
MicrosoftSQLServer | SQL2016SP1-WS2016 | 所有映像 Sku
MicrosoftSQLServer | SQL2016-WS2016  | 所有映像 Sku
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | 所有映像 Sku
MicrosoftSQLServer | SQL2012SP3-SQL2014SP1-WS2012R2 | 所有映像 Sku
MicrosoftSQLServer | SQL2016-SQL2014SP1-WS2012R2 | 所有映像 Sku
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | 所有映像 Sku
MicrosoftSQLServer | SQL2012SP3-SQL2014SP1-WS2012R2-BYOL | 所有映像 Sku
MicrosoftSQLServer | SQL2014SP1-WS2012R2-SQL2014SP1-WS2012R2-BYOL | 所有映像 Sku
MicrosoftSQLServer | SQL2014SP2-SQL2014SP1-WS2012R2-BYOL | 所有映像 Sku
MicrosoftSQLServer | SQL2016-SQL2014SP1-WS2012R2-BYOL | 所有映像 Sku
MicrosoftRServer | MLServer-WS2016 | 所有映像 Sku
MicrosoftVisualStudio | VisualStudio | 所有映像 Sku
MicrosoftVisualStudio | Windows | 所有映像 Sku
MicrosoftDynamicsAX | Dynamics  | 预先请求-AX7-Onebox-U8
microsoft-ads | windows-数据科学-vm | 所有映像 Sku
MicrosoftWindowsDesktop | Windows-10 | 所有映像 Sku
RedHat | RHEL | 6.7、6.8、6.9、6.10、7.2、7.3、7.4、7.5、7.6、7。7
RedHat | RHEL-SAP-HANA | 6.7、7.2、7。3
SUSE | SLES | 12. X
SUSE | SLES-HPC | 12. X
SUSE | SLES-HPC-优先级 | 12. X
SUSE | SLES-SAP | 12. X
SUSE | SLES-SAP-BYOS | 12. X
SUSE | SLES 优先级 | 12. X
SUSE | SLES-BYOS | 12. X
SUSE | SLES-SAPCAL | 12. X
SUSE | SLES-标准 | 12. X
Canonical | UbuntuServer | 14.04.0-LTS
Canonical | UbuntuServer | 14.04.1-LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3-LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5-DAILY-LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16.04-DAILY-LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0-LTS
Canonical | UbuntuServer | 18.04-每日-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8、6.9、6.10、7.3、7.4、7.5、7。6
OpenLogic | CentOS | 1.X、7、WINDOWS
OpenLogic | CentOS – LVM | 1.X、7、WINDOWS
OpenLogic | CentOS – SRIOV | 1.X、7、WINDOWS
cloudera | cloudera-centos-os | 7、windows
