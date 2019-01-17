---
title: 适用于 Azure 的 SAP LaMa 连接器 | Microsoft Docs
description: 使用 SAP LaMa 管理 Azure 上的 SAP 系统
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 17/07/2018
ms.author: sedusch
ms.openlocfilehash: d5d344f47fa46e9fe0adea048db200ec67a3fadc
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262570"
---
# <a name="sap-lama-connector-for-azure"></a>适用于 Azure 的 SAP LaMa 连接器

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> 一般支持声明：如需 SAP LaMa 或 Azure 连接器的支持，请始终向 SAP 提出有关组件 BC-VCM-LVM-HYPERV 的事件。

许多客户使用 SAP LaMa 来操作和监视 SAP 布局。 从 SAP LaMa 3.0 SP05 开始，默认随附了 Azure 的连接器。 可以使用此连接器来解除分配和启动虚拟机、复制和重定位托管磁盘，以及删除托管磁盘。 借助这些基本操作，可以使用 SAP LaMa 重定位、复制、克隆和刷新 SAP 系统。

本指南介绍如何为 SAP LaMa 设置 Azure 连接器、创建可用于安装自适应 SAP 系统的虚拟机，以及如何配置这些虚拟机。

> [!NOTE]
> 该连接器仅适用于 SAP LaMa Enterprise Edition

## <a name="resources"></a>资源

以下 SAP 说明与 Azure 上的 SAP LaMa 主题相关：

| 说明文档编号 | 标题 |
| --- | --- |
| [2343511] |用于 SAP Landscape Management (LaMa) 的 Microsoft Azure 连接器 |
| [2350235] |SAP Landscape Management 3.0 - Enterprise Edition |

另请阅读 [SAP LaMa 的 SAP 帮助门户](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE)。

## <a name="general-remarks"></a>一般备注

* 请务必在“设置”->“设置”->“引擎”中启用“自动装入点创建”  
  在未启用此设置的情况下，如果 SAP LaMa 使用虚拟机上的 SAP 自适应扩展装载卷，则该装入点必须存在。

* 使用单独的子网而不要使用动态 IP 地址，以防止在部署新 VM 并且未准备好 SAP 实例时发生 IP 地址“窃用”  
  如果在子网中使用动态 IP 地址分配（SAP LaMa 也使用这种分配），则使用 SAP LaMa 准备 SAP 系统可能会失败。 如果未准备好 SAP 系统，IP 地址将不会保留，可能会分配到其他虚拟机。

* 如果登录到托管主机，请确保不要阻止卸载文件系统  
  如果登录到 Linux 虚拟机并将工作目录更改为装入点中的某个目录（例如 /usr/sap/AH1/ASCS00/exe），则卷可能无法卸载，并且重定位或取消准备操作将会失败。

## <a name="set-up-azure-connector-for-sap-lama"></a>为 SAP LaMa 设置 Azure 连接器

从 SAP LaMa 3.0 SP05 开始随附了 Azure 连接器。 我们建议始终为 SAP LaMa 3.0 安装最新的支持包和修补程序。 Azure 连接器使用服务主体对 Microsoft Azure 授权。 遵循以下步骤为 SAP Landscape Management (LaMa) 创建服务主体。

1. 转到 https://portal.azure.com
1. 打开“Azure Active Directory”边栏选项卡
1. 单击“应用注册”
1. 单击“添加”
1. 输入名称，选择应用程序类型“Web 应用/API”，输入登录 URL（例如 http://localhost) ），并单击“创建”
1. 不会使用登录 URL，可为它输入任何有效的 URL
1. 选择新应用，并在“设置”选项卡中单击“密钥”
1. 输入新密钥的说明，选择“永不过期”，并单击“保存”
1. 记下值。 此值用作服务主体的密码
1. 记下应用程序 ID。 此值用作服务主体的用户名

默认情况下，服务主体无权访问 Azure 资源。 需要向服务主体授予访问这些资源的权限。

1. 转到 https://portal.azure.com
1. 打开“资源组”边栏选项卡
1. 选择要使用的资源组
1. 选择“访问控制(IAM)”
1. 单击“添加角色分配”
1. 选择“参与者”角色
1. 输入前面创建的应用程序名称
1. 点击“保存”
1. 针对要在 SAP LaMa 中使用的所有资源组重复步骤 3 到 8

打开 SAP LaMa 网站并导航到“基础结构”。 转到“云管理器”选项卡并单击“添加”。 选择“Microsoft Azure 云适配器”并单击“下一步”。 输入以下信息：

* 标签：选择连接器实例的名称
* 用户名：服务主体应用程序 ID
* 密码：服务主体密钥/密码
* URL：保留默认值 https://management.azure.com/
* 监视间隔(秒)：应至少为 300
* 订阅 ID：Azure 订阅 ID
* Azure Active Directory 租户 ID：Azure Active Directory 租户的 ID
* 代理主机：如果 SAP LaMa 需要使用代理连接到 Internet，则为代理的主机名
* 代理端口：代理的 TCP 端口

单击“测试配置”以验证输入。 在网站底部应会看到

连接成功：已成功连接到 Microsoft 云。 找到 7 个资源组（只请求了 10 个组）

。

## <a name="provision-a-new-adaptive-sap-system"></a>预配新的自适应 SAP 系统

可以手动部署新虚拟机，或使用[快速入门存储库](https://github.com/Azure/azure-quickstart-templates)中的 Azure 模板之一。 该存储库包含 [SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs)、[SAP NetWeaver 应用程序服务器](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)和[数据库](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database)的模板。 还可以使用这些模板来预配新主机作为系统副本/复本等的一部分。

我们建议对你想要通过 SAP LaMa 管理的所有虚拟机使用单独的子网，而不要使用动态 IP 地址，以防止在部署新虚拟机并且未准备好 SAP 实例时发生 IP 地址“窃用”。

> [!NOTE]
> 如果可能，请删除所有虚拟机扩展，因为在从虚拟机分离磁盘时，这些扩展可能导致运行时过长。

确保目标计算机上存在具有相同 ID 和 GID 的用户 \<hanasid>adm、\<sapsid>adm 和组 sapsys，或者使用 LDAP。 在用来运行 SAP NetWeaver (A)SCS 的虚拟机上启用并启动 NFS 服务器。

### <a name="manual-deployment"></a>手动部署

SAP LaMa 使用 SAP 主机代理来与虚拟机通信。 如果手动部署了虚拟机，或者未使用快速入门存储库中的 Azure 资源管理器模板，请务必安装最新的 SAP 主机代理和 SAP 自适应扩展。 有关 Azure 所需的修补程序级别的详细信息，请参阅 SAP 说明 [2343511]。

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Linux 虚拟机的手动部署

使用 SAP 说明 [2343511] 中所列的受支持操作系统之一创建新虚拟机。 为 SAP 实例添加附加的 IP 配置。 每个实例至少需要一个 IP 地址，并且必须使用虚拟主机名安装。

SAP NetWeaver ASCS 实例需要 /sapmnt/\<SAPSID>、/usr/sap/\<SAPSID>、/usr/sap/trans 和 /usr/sap/\<sapsid>adm 的磁盘。 SAP NetWeaver 应用程序服务器不需要其他磁盘。 与 SAP 实例相关的所有内容必须存储在 ASCS 上，并通过 NFS 导出。 否则，目前无法使用 SAP LaMa 添加更多的应用程序服务器。

![Linux 上的 SAP NetWeaver ASCS](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>SAP HANA 的手动部署

使用 SAP 说明 [2343511] 中所列的、SAP HANA 支持的操作系统之一创建新虚拟机。 为 SAP HANA 额外添加一个 IP 配置，并为每个 HANA 租户添加一个 IP 配置。

SAP HANA 需要 /hana/shared、/hana/backup、/hana/data 和 /hana/log 的磁盘

![Linux 上的 SAP HANA](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Linux 上的 Oracle 数据库的手动部署

使用 SAP 说明 [2343511] 中所列的、Oracle 数据库支持的操作系统之一创建新虚拟机。 为 Oracle 数据库额外添加一个 IP 配置。

Oracle 数据库需要 /oracle、/home/oraod1 和 /home/oracle 的磁盘

![Linux 上的 Oracle 数据库](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Microsoft SQL Server 的手动部署

使用 SAP 说明 [2343511] 中所列的、Microsoft SQL Server 支持的操作系统之一创建新虚拟机。 为 SQL Server 实例额外添加一个 IP 配置。

SQL Server 数据库服务器需要数据库数据和日志文件的磁盘，以及 c:\usr\sap 的磁盘。

![Linux 上的 Oracle 数据库](media/lama/sap-lama-db-sql.png)

确保在要用于重定位 SAP NetWeaver 应用程序服务器或用作系统复制/克隆目标的虚拟机上，为 SQL Server 安装支持的 Microsoft ODBC 驱动程序。

SAP LaMa 不能重定位 SQL Server 本身，因此，要用于重定位数据库实例或用作系统复制/克隆目标的虚拟机上需要预装 SQL Server。

### <a name="deploy-virtual-machine-using-an-azure-template"></a>使用 Azure 模板部署虚拟机

从 [SAP 软件市场](https://support.sap.com/swdc)下载适用于虚拟机操作系统的以下最新可用存档：

1. SAPCAR 7.21
1. SAP HOST AGENT 7.21
1. SAP ADAPTIVE EXTENSION 1.0 EXT

从 [Microsoft 下载中心](https://www.microsoft.com/download)下载以下组件

1. Microsoft Visual C++ 2010 Redistributable Package (x64)（仅适用于 Windows）
1. Microsoft ODBC Driver for SQL Server（仅适用于 SQL Server）

需要使用这些组件来部署模板。 使其适用于模板的最简单方法是将其上传到 Azure 存储帐户，并创建共享访问签名 (SAS)。

模板具有以下参数：

* sapSystemId：SAP 系统 ID。 此参数用于创建磁盘布局（例如 /usr/sap/\<sapsid>）。

* computerName：新虚拟机的计算机名。 SAP LaMa 也使用此参数。 使用此模板将新虚拟机预配为系统副本的一部分时，SAP LaMa 会一直等到可以访问具有此计算机名的主机为止。

* osType：要部署的操作系统的类型。

* dbtype：数据库的类型。 此参数用于确定需要添加多少个附加的 IP 配置，以及磁盘布局的外观。

* sapSystemSize：要部署的 SAP 系统的大小。 此参数用于确定虚拟机实例类型和大小。

* adminUsername：虚拟机的用户名。

* adminPassword：虚拟机的密码。 也可以提供 SSH 的公钥。

* sshKeyData：虚拟机的 SSH 公钥。 仅受 Linux 操作系统的支持。

* subnetId：要使用的子网的 ID。

* deployEmptyTarget：若要使用该虚拟机作为实例重定位或类似操作的目标，可以部署空目标。 在这种情况下，无需附加其他磁盘或 IP 配置。

* sapcarLocation：与部署的操作系统匹配的 sapcar 应用程序的位置。 sapcar 用于提取在其他参数中提供的存档。

* sapHostAgentArchiveLocation：SAP 主机代理存档的位置。 SAP 主机代理部署为此模板部署的一部分。

* sapacExtLocation：SAP 自适应扩展的位置。 SAP 说明 [2343511] 列出了 Azure 所需的最低修补程序级别。

* vcRedistLocation：安装 SAP 自适应扩展时所需的 VC 运行时的位置。 只有在 Windows 中才需要指定此参数。

* odbcDriverLocation：要安装的 ODBC 驱动程序的位置。 仅支持 Microsoft ODBC Driver for SQL Server。

* sapadmPassword：sapadm 用户的密码。

* sapadmId：sapadm 用户的 Linux 用户 ID。 在 Windows 中不需要指定。

* sapsysGid：sapsys 组的 Linux 组 ID。 在 Windows 中不需要指定。

* _artifactsLocation：基 URI，此模板所需的项目位于其中。 使用随附的脚本部署模板时，将使用订阅中的专用位置，并自动生成此值。 仅当不是从 GitHub 部署模板时，才需要指定此参数。

* _artifactsLocationSasToken：访问 _artifactsLocation 时所需的 sasToken。 使用随附的脚本部署模板时，将自动生成 sasToken。 仅当不是从 GitHub 部署模板时，才需要指定此参数。

### <a name="sap-hana"></a>SAP HANA

以下示例假设要安装系统 ID 为 HN1 的 SAP HANA，以及系统 ID 为 AH1 的 SAP NetWeaver 系统。 HANA 实例、SAP NetWeaver 系统使用的 HANA 租户、SAP NetWeaver ASCS 和第一个 SAP NetWeaver 应用程序服务器的虚拟主机名分别是 hn1-db、ah1-db、ah1-ascs 和 ah1-di-0。

#### <a name="install-sap-netweaver-ascs-for-sap-hana"></a>安装 SAP HANA 的 SAP NetWeaver ASCS

在启动 SAP Software Provisioning Manager (SWPM) 之前，需要装载 ASCS 虚拟主机名的 IP 地址。 建议的方法是使用 sapacext。 如果使用 sapacext 装载 IP 地址，请务必在重新启动后重新装载该 IP 地址。

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

运行 SWPM，并为“ASCS 实例主机名”使用 *ah1-ascs*。

![Linux][Logo_Linux] Linux  
将以下配置文件参数添加到位于 /usr/sap/hostctrl/exe/host_profile 的 SAP 主机代理配置文件。 有关详细信息，请参阅 SAP 说明 [2628497]。
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-hana"></a>安装 SAP HANA

如果使用命令行工具 hdblcm 安装 SAP HANA，请使用参数 --hostname 提供虚拟主机名。 需将数据库虚拟主机名的 IP 地址添加到网络接口。 建议的方法是使用 sapacext。 如果使用 sapacext 装载 IP 地址，请务必在重新启动后重新装载该 IP 地址。

为应用程序服务器使用的名称添加另一个虚拟主机名和 IP 地址，以连接到 HANA 租户。

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

在应用程序服务器虚拟机（而不是 HANA 虚拟机）上运行 SWPM 数据库实例安装。 在“SAP 系统的数据库”对话框中，为“数据库主机”使用 *ah1-db*。

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>安装 SAP HANA 的 SAP NetWeaver 应用程序服务器

在启动 SAP Software Provisioning Manager (SWPM) 之前，需要装载应用程序服务器虚拟主机名的 IP 地址。 建议的方法是使用 sapacext。 如果使用 sapacext 装载 IP 地址，请务必在重新启动后重新装载该 IP 地址。

![Linux][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

建议使用 SAP NetWeaver 配置文件参数 dbs/hdb/hdb_use_ident 来设置标识，用于在 HDB 用户存储中查找密钥。 可以在使用 SWPM 安装或运行数据库实例后手动添加此参数

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

如果手动设置，则还需要创建新的 HDB 用户存储条目。

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

在“主应用程序服务器实例”对话框中，为“PAS 实例主机名”使用 *ah1-di-0*。

#### <a name="post-installation-steps-for-sap-hana"></a>SAP HANA 的安装后步骤

在尝试执行租户复制、租户移动或创建系统复本之前，请务必备份 SYSTEMDB 和所有租户数据库。

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

以下示例假设要安装系统 ID 为 AS1 的 SAP NetWeaver 系统。 SAP NetWeaver 系统使用的 SQL Server 实例、SAP NetWeaver ASCS 和第一个 SAP NetWeaver 应用程序服务器的虚拟主机名分别是 as1-db、as1-ascs 和 as1-di-0。

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>安装 SQL Server 的 SAP NetWeaver ASCS

在启动 SAP Software Provisioning Manager (SWPM) 之前，需要装载 ASCS 虚拟主机名的 IP 地址。 建议的方法是使用 sapacext。 如果使用 sapacext 装载 IP 地址，请务必在重新启动后重新装载该 IP 地址。

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

运行 SWPM，并为“ASCS 实例主机名”使用 *as1-ascs*。

#### <a name="install-sql-server"></a>安装 SQL Server

需将数据库虚拟主机名的 IP 地址添加到网络接口。 建议的方法是使用 sapacext。 如果使用 sapacext 装载 IP 地址，请务必在重新启动后重新装载该 IP 地址。

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

在 SQL Server 虚拟机上运行 SWPM 的数据库实例安装。 使用 SAPINST_USE_HOSTNAME=*as1-db* 来重写用于连接到 SQL Server 的主机名。 如果使用 Azure 资源管理器模板部署了虚拟机，请务必将数据库数据文件使用的目录设置为 *C:\sql\data*，并将数据库日志文件使用的目录设置为 *C:\sql\log*。

确保用户 *NT AUTHORITY\SYSTEM* 有权访问 SQL Server，并且具有服务器角色 *sysadmin*。 有关详细信息，请参阅 SAP 说明 [1877727] 和 [2562184]。

#### <a name="install-sap-netweaver-application-server"></a>安装 SAP NetWeaver 应用程序服务器

在启动 SAP Software Provisioning Manager (SWPM) 之前，需要装载应用程序服务器虚拟主机名的 IP 地址。 建议的方法是使用 sapacext。 如果使用 sapacext 装载 IP 地址，请务必在重新启动后重新装载该 IP 地址。

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

在“主应用程序服务器实例”对话框中，为“PAS 实例主机名”使用 *as1-di-0*。

## <a name="troubleshooting"></a>故障排除

### <a name="errors-and-warnings-during-discover"></a>发现期间的错误和警告

* SELECT 权限被拒绝
  * [Microsoft][ODBC SQL Server Driver][SQL Server]对象 'log_shipping_primary_databases'，数据库 'msdb'，架构 'dbo' 中的 SELECT 权限被拒绝。 [SOAPFaultException]  
  对象 'log_shipping_primary_databases'，数据库 'msdb'，架构 'dbo' 中的 SELECT 权限被拒绝。
  * 解决方案  
    确保 *NT AUTHORITY\SYSTEM* 可以访问 SQL Server。 参阅 SAP 说明 [2562184]


### <a name="errors-and-warnings-for-instance-validation"></a>实例验证错误和警告

* 验证 HDB 用户存储时引发了异常  
  * 查看日志查看器  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException:ID 为 'RuntimeHDBConnectionValidator' 的验证程序发生异常(验证:'VALIDATION_HDB_USERSTORE'):无法检索 hdbuserstore  
    HANA 用户存储的位置不正确
  * 解决方案  
    确保 /usr/sap/AH1/hdbclient/install/installation.ini 正确

### <a name="errors-and-warnings-during-a-system-copy"></a>系统复制期间的错误和警告

* 验证系统预配步骤时出错
  * 原因: 调用 '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r 时发生 com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException
  * 解决方案  
    为源 HANA 系统中的所有数据库创建备份

* 数据库实例的系统复制步骤开始
  * 主机代理操作 '000D3A282BC91EE8A1D76CF1F92E2944' 失败(OperationException。 FaultCode:'127'，消息:“命令执行失败。 : [Microsoft][SQL Server Driver][SQL Server] 用户无权更改数据库 'AS2'、该数据库不存在，或者该数据库不处于允许访问检查的状态。)
  * 解决方案  
    确保 *NT AUTHORITY\SYSTEM* 可以访问 SQL Server。 参阅 SAP 说明 [2562184]

### <a name="errors-and-warnings-during-a-system-clone"></a>系统克隆期间的错误和警告

* 尝试在应用程序服务器或 ASCS 的“强制注册并启动实例代理”步骤中注册实例代理时出错
  * 尝试注册实例代理时出错。 (RemoteException:“无法从配置文件 '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' 加载实例数据:无法访问配置文件 '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0':没有此类文件或目录。”)
  * 解决方案  
   确保 ASCS/SCS 上的 sapmnt 共享对 SAP_AS1_GlobalAdmin 拥有完全访问权限

* “为克隆启用启动保护”步骤出错
  * 无法打开文件 '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0'。原因:没有此类文件或目录
  * 解决方案  
    应用程序服务器的计算机帐户需要拥有配置文件的写访问权限

### <a name="errors-and-warnings-during-create-system-replication"></a>创建系统复本期间的错误和警告

* 单击“创建系统复本”时发生异常
  * 原因: 调用 '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r 时发生 com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException
  * 解决方案  
    测试是否能够以 `<hanasid`>adm 的身份执行 sapacext

* 未在存储步骤中启用完整复制时出错
  * 报告路径 IStorageCopyData.storageVolumeCopyList:1 和字段 targetStorageSystemId 的上下文属性消息时出错
  * 解决方案  
    忽略步骤中的警告，然后重试。 SAP LaMa 的新支持包/修补程序中会修复此问题。

### <a name="errors-and-warnings-during-relocate"></a>重定位期间的错误和警告

* 不允许使用路径 '/usr/sap/AH1' 进行 NFS 重新导出。
  * 查看 SAP 说明 [2628497] 了解详细信息。
  * 解决方案  
    将 ASCS 导出内容添加到 ASCS HostAgent 配置文件。 参阅 SAP 说明 [2628497]

* 重定位 ASCS 时函数未实现
  * 命令输出: exportfs: host:/usr/sap/AX1:函数未实现
  * 解决方案  
    确保在重定位目标虚拟机上启用 NFS 服务器服务

### <a name="errors-and-warnings-during-application-server-installation"></a>应用程序服务器安装期间的错误和警告

* 执行 SAPinst 步骤时出错: getProfileDir
  * 错误:(步骤报告的最后一个错误:在模块调用中捕获到 ESAPinstException:步骤 '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' 的验证程序报告了错误:节点 \\\as1-ascs\sapmnt\AS1\SYS\profile 不存在。 在交互模式下启动 SAPinst 即可解决此问题)
  * 解决方案  
    确保以有权访问该配置文件的用户身份运行 SWPM。 可在应用程序服务器安装向导中配置此用户

* 执行 SAPinst 步骤时出错: askUnicode
  * 错误:(步骤报告的最后一个错误:在模块调用中捕获到 ESAPinstException:步骤 '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_getUnicode|ind|ind|ind|ind|unicode|0|askUnicode' 的验证程序报告了错误:在交互模式下启动 SAPinst 即可解决此问题)
  * 解决方案  
    如果使用最新的 SAP 内核，SWPM 无法使用 ASCS 的消息服务器来确定系统是否仍是 unicode 系统。 请参阅 SAP 说明 [2445033] 了解更多详细信息。  
    SAP LaMa 的新支持包/修补程序中会修复此问题。  
    在 SAP 系统的默认配置文件中设置配置文件参数 OS_UNICODE=uc 即可解决此问题。

* 执行 SAPinst 步骤时出错: dCheckGivenServer
  * 执行 SAPinst 步骤时出错: dCheckGivenServer" version="1.0" 错误:(步骤报告的最后一个错误: \<p> 用户已取消安装。 \</p>
  * 解决方案  
    确保以有权访问该配置文件的用户身份运行 SWPM。 可在应用程序服务器安装向导中配置此用户

* 执行 SAPinst 步骤时出错: checkClient
  * 执行 SAPinst 步骤时出错: checkClient" version="1.0" 错误:(步骤报告的最后一个错误: \<p> 用户已取消安装。 \</p>)
  * 解决方案  
    确保要安装应用程序服务器的虚拟机上已安装了 Microsoft ODBC Driver for SQL Server

* 执行 SAPinst 步骤时出错: copyScripts
  * 步骤报告的最后一个错误:系统调用失败。 详细信息:结合参数(\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w)执行系统调用 'fopenU' 时发生错误 13 (0x0000000d) (权限被拒绝)，文件(\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp)中的行(494)，堆栈跟踪:  
  CThrThread.cpp:85:CThrThread::threadFunction()  
  CSiServiceSet.cpp:63:CSiServiceSet::executeService()  
  CSiStepExecute.cpp:913:CSiStepExecute::execute()  
  EJSController.cpp:179:EJSControllerImpl::executeScript()  
  JSExtension.hpp:1136:CallFunctionBase::call()  
  iaxxcfile.cpp:183: iastring CIaOsFileConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcfile.cpp:1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp:773:CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp:233:CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp:29:CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp:265:CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp:58:CSyFileStream2Impl::CSyFileStream2Impl(const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp:456:CSyFileStream2Impl::open()
  * 解决方案  
    确保以有权访问该配置文件的用户身份运行 SWPM。 可在应用程序服务器安装向导中配置此用户

* 执行 SAPinst 步骤时出错: askPasswords
  * 步骤报告的最后一个错误:系统调用失败。 详细信息:结合参数(...)执行系统调用 'NetValidatePasswordPolicy' 时发生错误 5 (0x00000005) (权限被拒绝)，文件(\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp)中的行(359)，堆栈跟踪:  
  CThrThread.cpp:85:CThrThread::threadFunction()  
  CSiServiceSet.cpp:63:CSiServiceSet::executeService()  
  CSiStepExecute.cpp:913:CSiStepExecute::execute()  
  EJSController.cpp:179:EJSControllerImpl::executeScript()  
  JSExtension.hpp:1136:CallFunctionBase::call()  
  CSiStepExecute.cpp:764:CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp:56:DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp:85:DarkModeDialog::submit()  
  EJSController.cpp:179:EJSControllerImpl::executeScript()  
  JSExtension.hpp:1136:CallFunctionBase::call()  
  iaxxcaccount.cpp:107: iastring CIaOsAccountConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcaccount.cpp:1186: iastring CIaOsAccountConnect::validatePasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp:430:CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp:297:ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,*****) const )
  * 解决方案  
    确保在步骤 *Isolation* 中添加一个 Host 规则，以便能够在 VM 与域控制器之间通信

## <a name="next-steps"></a>后续步骤
* [Azure 上的 SAP HANA 操作指南][hana-ops-guide]
* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]