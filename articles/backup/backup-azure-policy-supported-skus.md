---
title: Azure Policy 支持的 VM SKU
description: 描述备份提供的内置 Azure 策略支持的 VM SKU（按发布服务器、映像提供和映像 SKU）的文章
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980109"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Azure Policy 支持的 VM SKU

Azure 备份提供一个内置策略（使用 Azure 策略），可以分配给**订阅或资源组中指定位置中的所有 Azure VM。** 当此策略分配给给定作用域时，该作用域中创建的所有新 VM 将自动配置为备份到**同一位置和订阅中的现有保管库**。 下表列出了此策略支持的所有 VM SKU。

### <a name="supported-vms"></a>**支持的 VM**

**策略名称：** 将位置 VM 上的备份配置为同一位置的现有中央保管库

图像发布者 | 图像优惠 | 映像 SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows 服务器 2008 R2 SP1 （2008-R2-SP1）
MicrosoftWindowsServer | WindowsServer | [小磁盘]Windows 服务器 2008 R2 SP （2008-R2-SP1 小磁盘）
MicrosoftWindowsServer | WindowsServer | Windows 服务器 2012 数据中心 （2012-数据中心）
MicrosoftWindowsServer | WindowsServer | [小磁盘]Windows 服务器 2012 数据中心 （2012-数据中心-小磁盘）
MicrosoftWindowsServer | WindowsServer | Windows 服务器 2012 R2 数据中心 （2012-R2-数据中心）
MicrosoftWindowsServer | WindowsServer | [小磁盘]Windows 服务器 2012 R2 数据中心 （2012-R2-数据中心-小磁盘）
MicrosoftWindowsServer | WindowsServer | Windows 服务器 2016 数据中心 （2016-数据中心）
MicrosoftWindowsServer | WindowsServer | Windows 服务器 2016 数据中心 - 服务器核心 （2016-数据中心-服务器核心）
MicrosoftWindowsServer | WindowsServer | [小磁盘]Windows 服务器 2016 数据中心 - 服务器核心 （2016-数据中心-服务器-核心小磁盘）
MicrosoftWindowsServer | WindowsServer | [小磁盘]Windows 服务器 2016 数据中心 （2016-数据中心-小磁盘）
MicrosoftWindowsServer | WindowsServer | Windows 服务器 2019 数据中心服务器核心与容器 （2016-数据中心与容器）
MicrosoftWindowsServer | WindowsServer | Windows 服务器 2016 远程桌面会话主机 2016 （2016-数据中心与 RDSH）
MicrosoftWindowsServer | WindowsServer | Windows 服务器 2019 数据中心 （2019-数据中心）
MicrosoftWindowsServer | WindowsServer | Windows 服务器 2019 数据中心服务器核心 （2019-数据中心核心）
MicrosoftWindowsServer | WindowsServer | [小磁盘]Windows 服务器 2019 数据中心服务器核心 （2019-数据中心-核心小磁盘）
MicrosoftWindowsServer | WindowsServer | Windows 服务器 2019 数据中心服务器核心与容器（2019-数据中心核心与容器）
MicrosoftWindowsServer | WindowsServer | [小磁盘]Windows 服务器 2019 数据中心服务器核心与容器 （2019-数据中心-核心与容器小磁盘）
MicrosoftWindowsServer | WindowsServer | [小磁盘]Windows 服务器 2019 数据中心 （2019-数据中心-小磁盘）
MicrosoftWindowsServer | WindowsServer | Windows 服务器 2019 数据中心与容器 （2019-数据中心带容器）
MicrosoftWindowsServer | WindowsServer | [小磁盘]Windows 服务器 2019 数据中心与容器 （2019-数据中心与容器小磁盘）
MicrosoftWindowsServer | WindowsServer | 视窗服务器 2019 数据中心 （zh-cn） （2019-数据中心-zhcn）
MicrosoftWindowsServer | 视窗服务器半年 | 数据中心-核心-1709-小磁盘
MicrosoftWindowsServer | 视窗服务器半年 | 数据中心-核心-1709-带容器的小磁盘
MicrosoftWindowsServer | 视窗服务器半年 | 数据中心-核心-1803-带容器的小磁盘
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 所有图像 SKU
MicrosoftSQLServer | SQL2016SP1-WS2016 | 所有图像 SKU
MicrosoftSQLServer | SQL2016-WS2016  | 所有图像 SKU
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | 所有图像 SKU
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | 所有图像 SKU
MicrosoftSQLServer | SQL2016-WS2012R2 | 所有图像 SKU
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | 所有图像 SKU
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | 所有图像 SKU
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | 所有图像 SKU
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | 所有图像 SKU
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | 所有图像 SKU
MicrosoftRServer | MLServer-WS2016 | 所有图像 SKU
微软视觉工作室 | VisualStudio | 所有图像 SKU
微软视觉工作室 | Windows | 所有图像 SKU
微软动力AX | Dynamics  | 预召回-AX7-一盒-U8
microsoft-ads | 窗口-数据-科学-vm | 所有图像 SKU
MicrosoftWindowsDesktop | Windows-10 | 所有图像 SKU
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-SAP-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12.X
SUSE | SLES-HPC | 12.X
SUSE | SLES-HPC-优先级 | 12.X
SUSE | SLES-SAP | 12.X
SUSE | SLES-SAP-BYOS | 12.X
SUSE | SLES 优先 | 12.X
SUSE | SLES-BYOS | 12.X
SUSE | SLES-SAPCAL | 12.X
SUSE | SLES 标准 | 12.X
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
Oracle | Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X， 7.X
OpenLogic | CentOS_LVM | 6.X， 7.X
OpenLogic | 森托斯·斯里罗夫 | 6.X， 7.X
cloudera | cloudera-centos-os | 7.X
