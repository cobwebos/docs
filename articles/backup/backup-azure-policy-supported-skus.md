---
title: Azure Policy 支持的 VM SKU
description: 本文介绍受支持的 VM SKU（包含发布者、映像产品/服务和映像 SKU），它们受备份提供的内置 Azure 策略支持
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 4658d1beffe707682e173491edea1eac0db9c811
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82183613"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Azure Policy 支持的 VM SKU

Azure 备份提供了一个内置策略（使用 Azure Policy），可以将其分配给“订阅或资源组中指定位置的所有 Azure VM”****。 将此策略分配到给定范围时，该范围中创建的所有新 VM 都将自动配置为备份到位于“同一位置和订阅中的现有保管库”****。 下表列出了此策略支持的所有 VM SKU。

## <a name="supported-vms"></a>支持的 VM*

策略名称：**** 将某个位置的 VM 的备份配置到同一位置中的现有中央保管库

映像发布者 | 映像产品/服务 | 映像 SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-R2-SP1-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Datacenter (2012-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 Datacenter (2012-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 Datacenter (2012-R2-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter (2016-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter - Server Core (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter - Server Core (2016-Datacenter-Server-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter (2016-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | 包含容器的 Windows Server 2019 Datacenter Server Core (2016-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 远程桌面会话主机 2016 (2016-Datacenter-with-RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (2019-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core-smalldisk)
MicrosoftWindowsServer | WindowsServer | 包含容器的 Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] 包含容器的 Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter (2019-Datacenter-smalldisk)
MicrosoftWindowsServer | WindowsServer | 包含容器的 Windows Server 2019 Datacenter (2019-Datacenter-with-Containers)
MicrosoftWindowsServer | WindowsServer | [smalldisk] 包含容器的 Windows Server 2019 Datacenter (2019-Datacenter-with-Containers-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-cn) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-with-Containers-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1803-with-Containers-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 所有映像 SKU
MicrosoftSQLServer | SQL2016SP1-WS2016 | 所有映像 SKU
MicrosoftSQLServer | SQL2016-WS2016 | 所有映像 SKU
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | 所有映像 SKU
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | 所有映像 SKU
MicrosoftSQLServer | SQL2016-WS2012R2 | 所有映像 SKU
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | 所有映像 SKU
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | 所有映像 SKU
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | 所有映像 SKU
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | 所有映像 SKU
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | 所有映像 SKU
MicrosoftRServer | MLServer-WS2016 | 所有映像 SKU
MicrosoftVisualStudio | VisualStudio | 所有映像 SKU
MicrosoftVisualStudio | Windows | 所有映像 SKU
MicrosoftDynamicsAX | Dynamics | Pre-Req-AX7-Onebox-U8
microsoft-ads | windows-data-science-vm | 所有映像 SKU
MicrosoftWindowsDesktop | Windows-10 | 所有映像 SKU
RedHat | RHEL | 6.7、6.8、6.9、6.10、7.2、7.3、7.4、7.5、7.6、7.7
RedHat | RHEL-SAP-HANA | 6.7、7.2、7.3
SUSE | SLES | 12.X
SUSE | SLES-HPC | 12.X
SUSE | SLES-HPC-Priority | 12.X
SUSE | SLES-SAP | 12.X
SUSE | SLES-SAP-BYOS | 12.X
SUSE | SLES-Priority | 12.X
SUSE | SLES-BYOS | 12.X
SUSE | SLES-SAPCAL | 12.X
SUSE | SLES-Standard | 12.X
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
Canonical | UbuntuServer | 18.04-DAILY-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8、6.9、6.10、7.3、7.4、7.5、7.6
OpenLogic | CentOS | 6.X、7.X
OpenLogic | CentOS – LVM | 6.X、7.X
OpenLogic | CentOS – SRIOV | 6.X、7.X
cloudera | cloudera-centos-os | 7.X
