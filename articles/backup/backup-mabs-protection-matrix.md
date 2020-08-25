---
title: MABS（Azure 备份服务器）V3 UR1 保护矩阵
description: 本文提供了一个支持矩阵，列出了受 Azure 备份服务器保护的所有工作负荷、数据类型和安装。
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: 70d8b8cd26a40b0c7ec8b538bcb702d281f829e6
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826831"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>MABS（Azure 备份服务器）V3 UR1 保护矩阵

本文列出可以使用 Azure 备份服务器保护的各种服务器和工作负荷。 以下矩阵列出了可使用 Azure 备份服务器保护的内容。

对于 MABS v3 UR1，请使用以下矩阵：

* 工作负荷 - 技术的工作负荷类型。

* 版本-支持的工作负荷的 MABS 版本。

* MABS 安装-要在其中安装 MABS 的计算机/位置。

* 保护和恢复 - 列出工作负荷的详细信息，如支持的存储容器或支持的部署。

>[!NOTE]
>MABS v3 UR1 不推荐使用对32位保护代理的支持。 请参阅 [32 位保护代理弃用](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation)。

## <a name="protection-support-matrix"></a>保护支持矩阵

以下部分详细介绍了 MABS 的保护支持矩阵：

* [应用程序备份](#applications-backup)
* [VM 备份](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>应用程序备份

| **工作负载**               | **版本**                                                  | **Azure 备份服务器安装**                       | **支持的 Azure 备份服务器** | **保护和恢复**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| 客户端计算机（64 位） | Windows 10                                                  | 物理服务器  <br><br>    Hyper-V 虚拟机   <br><br>   VMware 虚拟机 | V3 UR1                            | 卷、共享、文件夹、文件、已删除重复数据的卷   <br><br>   受保护的卷必须采用 NTFS 格式。 不支持 FAT 和 FAT32。  <br><br>    卷必须至少有 1 GB 空间。 Azure 备份服务器使用卷影复制服务 (VSS) 来创建数据快照；仅当卷至少有 1 GB 空间时，快照才能正常工作。 |
| 服务器（64 位）          | Windows Server 2019、2016、2012 R2、2012                    | Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）  <br><br>    物理服务器  <br><br>    Hyper-V 虚拟机 <br><br>     VMware 虚拟机  <br><br>    Azure Stack | V3 UR1                            | 卷、共享、文件夹、文件 <br><br>    重复数据卷仅 (NTFS)   <br><br>   系统状态和裸机（当工作负荷作为 Azure 虚拟机运行时不支持） |
| 服务器（64 位）          | Windows Server 2008 R2 SP1、Windows Server 2008 SP2 (你需要安装 [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616))  | 物理服务器  <br><br>    Hyper-V 虚拟机  <br><br>      VMware 虚拟机  <br><br>   Azure Stack | V3 UR1                            | 卷、共享、文件夹、文件、系统状态/裸机        |
| SQL Server                | SQL Server 2019、2017、2016 和[支持的 SP](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016)、2014 和支持的 [SP](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | 物理服务器  <br><br>     Hyper-V 虚拟机   <br><br>     VMware 虚拟机  <br><br>   Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）  <br><br>     Azure Stack | V3 UR1                            | 所有部署方案：数据库       <br><br>  MABS v3 UR1 支持备份基于 ReFS 卷的 SQL 数据库                  |
| Exchange                   | Exchange 2019、2016                                         | 物理服务器   <br><br>   Hyper-V 虚拟机  <br><br>      VMware 虚拟机  <br><br>   Azure Stack  <br><br>    Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时） | V3 UR1                            | 保护（所有部署方案）：独立 Exchange 服务器、数据库可用性组 (DAG) 下的数据库  <br><br>    恢复（所有部署方案）：邮箱、DAG 下的邮箱数据库    <br><br>  MABS v3 UR1 支持备份基于 ReFS 的 Exchange |
| SharePoint                 | 带最新 SP 的 SharePoint 2019、2016                       | 物理服务器  <br><br>    Hyper-V 虚拟机 <br><br>    VMware 虚拟机  <br><br>   Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）   <br><br>   Azure Stack | V3 UR1                            | 保护（所有部署方案）：场、前端 Web 服务器内容  <br><br>    恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器  <br><br>    对于将 SQL Server 2012 AlwaysOn 功能用于内容数据库的 SharePoint 场，不支持对其进行保护。 |

## <a name="vm-backup"></a>VM 备份

| **工作负载**                                                 | **版本**                                             | **Azure 备份服务器安装**                      | **支持的 Azure 备份服务器** | **保护和恢复**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-V 主机 - Hyper-V 主机服务器、群集或 VM 上的 MABS 保护代理 | Windows Server 2019、2016、2012 R2、2012               | 物理服务器  <br><br>    Hyper-V 虚拟机 <br><br>    VMware 虚拟机 | V3 UR1                             | 保护：Hyper-V 计算机、群集共享卷 (CSV)  <br><br>    恢复：虚拟机恢复，以及文件和文件夹（仅适用于 Windows）、卷、虚拟硬盘的项级恢复 |
| VMware VM                                                  | VMware 服务器 5.5、6.0、6.5 或 6.7（经许可版本） | Hyper-V 虚拟机  <br><br>   VMware 虚拟机         | V3 UR1                             | 保护：群集共享卷 (CSV)、NFS 和 SAN 存储中的 VMware VM   <br><br>     恢复：虚拟机恢复，以及文件和文件夹（仅适用于 Windows）、卷、虚拟硬盘的项级恢复 <br><br>    不支持 VMware vApps。 |

## <a name="linux"></a>Linux

| **工作负载** | **版本**                               | **Azure 备份服务器安装**                      | **支持的 Azure 备份服务器** | **保护和恢复**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | 以 Hyper-V 或 VMware 来宾身份运行的 Linux | 物理服务器、本地 Hyper-V VM、VMWare 中的 Windows VM | V3 UR1                             | Hyper-v 必须在 Windows Server 2012 R2、Windows Server 2016 或 Windows Server 2019 上运行。 保护：整个虚拟机   <br><br>   恢复：整个虚拟机   <br><br>    仅支持文件一致性快照。    <br><br>   有关支持的 Linux 发行版和版本的完整列表，请参阅 [Azure 认可的发行版中的 Linux](../virtual-machines/linux/endorsed-distros.md) 一文。 |

## <a name="azure-expressroute-support"></a>Azure ExpressRoute 支持

可以使用公共对等互连（适用于旧线路）和 Microsoft 对等互连通过 Azure ExpressRoute 备份数据。 不支持通过专用对等互连进行备份。

使用公共对等互连：确保访问以下域/地址：

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

使用 Microsoft 对等互连，选择以下服务/区域和相关社区值：

* Azure Active Directory (12076:5060)
* Microsoft Azure 区域（根据恢复服务保管库的位置）
* Azure 存储（根据恢复服务保管库的位置）

有关详细信息，请参阅 [ExpressRoute 路由要求](../expressroute/expressroute-routing.md)。

>[!NOTE]
>对于新线路，公共对等互连已弃用。

## <a name="cluster-support"></a>群集支持

Azure 备份服务器可以保护以下群集应用程序中的数据：

* 文件服务器

* SQL Server

* Hyper-V - 如果使用横向扩展的 MABS 保护代理来保护 Hyper-V 群集，则无法为受保护的 Hyper-V 工作负荷添加辅助保护。

* Exchange Server - Azure 备份服务器可以保护受支持 Exchange Server 版本的非共享磁盘群集（群集连续复制），而且还可以保护针对本地连续复制配置的 Exchange Server。

* SQL Server - Azure 备份服务器不支持备份托管在群集共享卷 (CSV) 上的 SQL Server 数据库。

Azure 备份服务器可以保护 MABS 服务器所在的同一个域中以及子域或受信任域中的群集工作负荷。 若要保护不受信任的域或工作组中的数据源，请对单个服务器使用 NTLM 或证书身份验证，或者只对群集使用证书身份验证。
