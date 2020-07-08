---
title: MABS （Azure 备份服务器） V3 UR1 protection 矩阵
description: 本文提供了一个支持矩阵，列出了受 Azure 备份服务器保护的所有工作负荷、数据类型和安装。
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: f950c7be28a4ff0dd0caa12d1327c2a0e2ac4fc5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84677315"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>MABS （Azure 备份服务器） V3 UR1 protection 矩阵

本文列出可以使用 Azure 备份服务器保护的各种服务器和工作负荷。 以下矩阵列出了可使用 Azure 备份服务器保护的内容。

对于 MABS v3 UR1，请使用以下矩阵：

* 工作负荷 - 技术的工作负荷类型。

* 版本-支持的工作负荷的 MABS 版本。

* MABS 安装-要在其中安装 MABS 的计算机/位置。

* 保护和恢复 - 列出工作负荷的详细信息，如支持的存储容器或支持的部署。

## <a name="protection-support-matrix"></a>保护支持矩阵

以下部分详细介绍了 MABS 的保护支持矩阵：

* [应用程序备份](#applications-backup)
* [VM 备份](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>应用程序备份

| **工作负荷**               | **Version**                                                  | **Azure 备份服务器安装**                       | **支持的 Azure 备份服务器** | **保护和恢复**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| 客户端计算机（64位） | Windows 10                                                  | 物理服务器  <br><br>    Hyper-V 虚拟机   <br><br>   VMware 虚拟机 | V3 UR1                            | 卷、共享、文件夹、文件、重复数据卷   <br><br>   受保护的卷必须是 NTFS。 不支持保护 FAT 和 FAT32。  <br><br>    卷必须至少为 1GB。 Azure 备份服务器使用卷影复制服务（VSS）来获取数据快照，仅当卷至少为 1 GB 时快照才起作用。 |
| 服务器（64位）          | Windows Server 2019、2016、2012 R2、2012                    | Azure 虚拟机（当工作负荷作为 Azure 虚拟机运行时）  <br><br>    物理服务器  <br><br>    Hyper-V 虚拟机 <br><br>     VMware 虚拟机  <br><br>    Azure Stack | V3 UR1                            | 卷、共享、文件夹、文件、重复数据卷（NTFS 和 ReFS）  <br><br>   系统状态和裸机（当工作负荷作为 Azure 虚拟机运行时不支持） |
| 服务器（64位）          | Windows Server 2008 R2 SP1、Windows Server 2008 SP2 （需要安装[Windows 管理帧 4.0](https://www.microsoft.com/download/details.aspx?id=40855)） | 物理服务器  <br><br>    Hyper-V 虚拟机  <br><br>      VMware 虚拟机  <br><br>   Azure Stack | V3 UR1                            | 卷、共享、文件夹、文件、系统状态/裸机        |
| SQL Server                | SQL Server 2019、2017、2016和[支持的 sp](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016)、2014和支持的[sp](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | 物理服务器  <br><br>     Hyper-V 虚拟机   <br><br>     VMware 虚拟机  <br><br>   Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）  <br><br>     Azure Stack | V3 UR1                            | 所有部署方案：数据库       <br><br>  MABS v3 UR1 支持通过 ReFS 卷备份 SQL 数据库                  |
| Exchange                   | Exchange 2019、2016                                         | 物理服务器   <br><br>   Hyper-V 虚拟机  <br><br>      VMware 虚拟机  <br><br>   Azure Stack  <br><br>    Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时） | V3 UR1                            | 保护（所有部署方案）：独立 Exchange 服务器、数据库可用性组（DAG）下的数据库  <br><br>    恢复（所有部署方案）：邮箱，DAG 下的邮箱数据库    <br><br>  MABS v3 UR1 支持备份 over ReFS 的 Exchange |
| SharePoint                 | SharePoint 2019、2016和最新的 Sp                       | 物理服务器  <br><br>    Hyper-V 虚拟机 <br><br>    VMware 虚拟机  <br><br>   Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）   <br><br>   Azure Stack | V3 UR1                            | 保护（所有部署方案）：场、前端 web 服务器内容  <br><br>    恢复（所有部署方案）：场、数据库、web 应用程序、文件或列表项、SharePoint 搜索、前端 web 服务器  <br><br>    不支持保护对内容数据库使用 SQL Server 2012 AlwaysOn 功能的 SharePoint 场。 |

## <a name="vm-backup"></a>VM 备份

| **工作负荷**                                                 | **Version**                                             | **Azure 备份服务器安装**                      | **支持的 Azure 备份服务器** | **保护和恢复**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-v 主机服务器、群集或 VM 上的 MABS 保护代理 | Windows Server 2019、2016、2012 R2、2012               | 物理服务器  <br><br>    Hyper-V 虚拟机 <br><br>    VMware 虚拟机 | V3 UR1                             | 保护： Hyper-v 计算机、群集共享卷（Csv）  <br><br>    恢复：虚拟机、文件和文件夹的项目级恢复仅适用于 Windows、卷、虚拟硬盘驱动器 |
| VMware Vm                                                  | VMware server 5.5、6.0、6.5、6.7 （许可的版本） | Hyper-v 虚拟机  <br><br>   VMware 虚拟机         | V3 UR1                             | 保护：群集共享卷（Csv）、NFS 和 SAN 存储上的 VMware Vm   <br><br>     恢复：虚拟机、文件和文件夹的项目级恢复仅适用于 Windows、卷、虚拟硬盘驱动器 <br><br>    不支持 VMware vApps。 |

## <a name="linux"></a>Linux

| **工作负荷** | **Version**                               | **Azure 备份服务器安装**                      | **支持的 Azure 备份服务器** | **保护和恢复**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | 作为 Hyper-v 或 VMware 来宾运行的 Linux | 物理服务器、本地 Hyper-v VM、VMWare 中的 Windows VM | V3 UR1                             | Hyper-v 必须在 Windows Server 2012 R2 或 Windows Server 2016 上运行。 保护：整个虚拟机   <br><br>   恢复：整个虚拟机   <br><br>    仅支持文件一致性快照。    <br><br>   有关受支持的 Linux 分发版和版本的完整列表，请参阅[Azure 认可的分发](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)中的 Linux。 |

## <a name="azure-expressroute-support"></a>Azure ExpressRoute 支持

可以使用公共对等互连（适用于旧线路）和 Microsoft 对等互连通过 Azure ExpressRoute 备份数据。 不支持通过专用对等互连进行备份。

使用公共对等互连：确保访问以下域/地址：

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

对于 Microsoft 对等互连，请选择下列服务/区域和相关团体值：

* Azure Active Directory (12076:5060)
* Microsoft Azure 区域（根据恢复服务保管库的位置）
* Azure 存储（根据恢复服务保管库的位置）

有关详细信息，请参阅[ExpressRoute 路由要求](https://docs.microsoft.com/azure/expressroute/expressroute-routing)。

>[!NOTE]
>对于新线路，公共对等互连已弃用。

## <a name="cluster-support"></a>群集支持

Azure 备份服务器可保护以下群集应用程序中的数据：

* 文件服务器

* SQL Server

* Hyper-V - 如果使用横向扩展的 MABS 保护代理来保护 Hyper-V 群集，则无法为受保护的 Hyper-V 工作负荷添加辅助保护。

* Exchange Server - Azure 备份服务器可保护受支持 Exchange Server 版本的非共享磁盘群集（群集连续的复制），还可保护针对本地连续复制配置的 Exchange Server。

* SQL Server - Azure 备份服务器不支持备份托管在群集共享卷 (CSV) 上的 SQL Server 数据库。

Azure 备份服务器可以保护 MABS 服务器所在的同一个域中以及子域或受信任域中的群集工作负荷。 如果要保护不受信任域或工作组中的数据源，请针对单个服务器使用 NTLM 或证书身份验证，或针对群集仅使用证书身份验证。
