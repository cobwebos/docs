---
title: "在 Azure SAP HANA（大型实例）上安装 SAP HANA | Microsoft Docs"
description: "如何在 Azure SAP HANA（大型实例）上安装 SAP HANA。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: 0d998b5347a9da6dd209b0ba106792ab5c34164d
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>如何在 Azure 上安装和配置 SAP HANA（大型实例）

安装 SAP HANA 是你的责任，可以在切换新的 Azure SAP HANA（大型实例）服务器后立即执行。 请注意，根据 SAP 政策，SAP HANA 的安装必须由经过认证的 SAP HANA 安装人员（已通过 SAP 技术认证专员 - SAP HANA 安装认证考试）或经过 SAP 认证的系统集成商 (SI) 完成。

需要考虑具体的与 SAP HANA 服务器端和客户端相关的连接注意事项。 许多情况下，SAP HANA 服务器会将其 IP 地址发送到客户端进行缓存，用于后续的连接尝试。 由于 Azure SAP HANA（大型实例）将用在租户网络中的内部服务器 IP 地址通过 NAT 方式转换成为指定 Azure VNet 提供的 IP 地址范围，SAP HANA 数据库服务器会按设计发送&quot;内部&quot; IP 地址范围。 例如，进行主机名解析时使用缓存的内部 IP 地址，而不使用提供 NAT 型 IP 地址的 SAP HANA。 因此，使用 SAP HANA 客户端（ODBC、JDBC 等）的应用程序无法通过此 IP 地址进行连接。 必须编辑 SAP HANA 全局系统配置文件 (global.ini)，指示 SAP HANA 服务器将 NAT 型 IP 地址传播到客户端。

将以下代码添加到 global.ini（直接添加或者通过 SAP HANA Studio 添加）：
```
[public\_hostname\_resolution]
use\_default\_route = no
map\_<hostname of SAP HANA on Azure (Large Instances) tenant> = <NAT IP Address (IP address that can be accessed in Azure)>
```
如需更多详细信息和示例，请参阅 [SAP HANA Administration Guide](http://help.sap.com/hana/sap_hana_administration_guide_en.pdf)（SAP HANA 管理指南）中的 _Mapping Host Names for Database Client Access_（映射进行数据库客户端访问的主机名）部分。

在客户端（针对 Azure 中的 SAP 应用程序服务器），编辑 /etc/hosts 文件（在 Linux 中）或 /system32/drivers/etc/hosts 文件（在 Windows Server 中），包括一个条目，用于 Azure SAP HANA（大型实例）租户的主机名及其相应的 NAT 型 IP 地址。

>[!NOTE] 
>在安装 SAP HANA 数据库客户端期间，如果在连接到 Azure SAP HANA（大型实例）时出现错误，可隐式使用 HANA 大型实例租户的 NAT IP 地址，而不使用主机名。

## <a name="storage"></a>存储

Azure SAP HANA（大型实例）的存储布局可通过 Azure SAP HANA 服务管理按 SAP 建议的最佳实践进行配置，详见 [SAP HANA Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)（SAP HANA 存储要求）白皮书。

不同 HANA 大型实例存储的确切配置如下所示：

| 内存大小 | HANA/数据 | HANA/日志 | HANA/共享 | HANA/日志/备份 |
| --- | --- | --- | --- | --- |
| 768 GB | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1536 GB | 3456 GB | 768 GB | 1024 GB | 768 GB |
| 3072 GB | 7680 GB | 1536 GB | 1024 GB | 1536 GB |

此外，客户可以购买更多存储容量，增量为 1 TB。 可以将此附加存储作为新卷添加到 HANA 大型实例。

大型实例戳中的存储控制器和节点将同步到 NTP 服务器。 针对 NTP 服务器同步 Azure SAP HANA（大型实例）单元和 Azure VM 以后，在 Azure 或大型实例戳中的基础结构和计算单元之间就不会出现显著的时间偏移。

## <a name="operating-system"></a>操作系统

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) 是针对 Azure SAP HANA（大型实例）安装的 Linux 分发。 此特定分发提供特定于 SAP 的&quot;现成&quot;功能（包括在 SLES 上高效运行 SAP 所需的预设参数）。

请参阅 SUSE 网站上的[资源库/白皮书](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)以及 SAP 社区网络 (SCN) 上的 [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)（基于 SUSE 的 SAP），了解多种与部署 SLES SAP HANA 相关的有用资源（包括设置高可用性、特定于 SAP 操作的安全强化等）。

其他与 SLES 相关的有用链接：

- [SAP HANA on SUSE Linux Site](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)（SUSE Linux 站点上的 SAP HANA）
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)（SAP 最佳实践：排队复制 - 基于 SUSE Linux Enterprise 12 的 SAP NetWeaver）
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap)（ClamSAP - 适用于 SAP 的 SLES 病毒防护）（包括 SLES 12 for SAP Applications）

适用于在 SLES 12 SP1 上实现 SAP HANA 的 SAP 支持说明：

- [SAP Support Note #1944799 – SAP HANA Guidelines for SLES Operating System Installation](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)（SAP 支持说明 1944799 - 适用于 SLES 操作系统安装的 SAP HANA 指南）
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E)（SAP 支持说明 2205917 - SAP HANA DB 建议的适用于 SLES 12 for SAP Applications 的 OS 设置）
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12:  Installation Notes](https://launchpad.support.sap.com/#/notes/1984787)（SAP 支持说明 1984787 - SUSE Linux Enterprise Server 12：安装说明）
- [SAP Support Note #171356 – SAP Software on Linux:  General Information](https://launchpad.support.sap.com/#/notes/1984787)（SAP 支持说明 171356 - Linux 上的 SAP 软件：常规信息）
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070)（SAP 支持说明 1391070 - Linux UUID 解决方案）

## <a name="time-synchronization"></a>时间同步

SAP 对组成 SAP 系统的各组件的时间差异非常敏感。 如果你已使用过 SAP (Basis) 很长时间，则可能熟悉错误标题为 ZDATE\_LARGE\_TIME\_DIFF 的 SAP ABAP 短转储，因为当不同服务器或 VM 的系统时间偏移过大时，就会出现这些短转储。

就 Azure SAP HANA（大型实例）来说，在 Azure 中进行的时间同步不适用于大型实例戳中的计算单元。 这种同步不适用于在 Azure 中（VM 上）以本机方式运行的 SAP 应用程序，因为 Azure 可确保正确同步系统的时间。 因此，必须设置单独的时间服务器，供在 Azure VM 上运行的 SAP 应用程序服务器以及在 HANA 大型实例上运行的 SAP HANA 数据库实例使用。 大型实例戳中的存储基础结构与 NTP 服务器进行时间同步。



