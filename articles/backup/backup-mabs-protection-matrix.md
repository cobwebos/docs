---
title: Azure 备份服务器可备份哪些内容
description: 本文提供了一个支持矩阵，列出了受 Azure 备份服务器保护的所有工作负荷、数据类型和安装。
author: dcurwin
ms.service: backup
keywords: ''
ms.date: 11/13/2018
ms.topic: conceptual
ms.author: dacurwin
manager: carmonm
ms.openlocfilehash: 37f284f9a3dfe06e4d306b8d56a63d4b18d6cc2e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012089"
---
# <a name="azure-backup-server-protection-matrix"></a>Azure 备份服务器保护矩阵

本文列出可以使用 Azure 备份服务器保护的各种服务器和工作负荷。 以下矩阵列出了可使用 Azure 备份服务器保护的内容。

## <a name="protection-support-matrix"></a>保护支持矩阵

|工作负载|版本|Azure 备份服务器</br> 安装|支持的 Azure 备份服务器|保护和恢复|
|------------|-----------|---------------|--------------|--------------|
|客户端计算机（64 位和 32 位）|Windows 10|物理服务器<br /><br />Hyper-V 虚拟机<br /><br />VMware 虚拟机|V3、V2|卷、共享、文件夹、文件、重复删除的卷<br /><br />受保护的卷必须采用 NTFS 格式。 不支持 FAT 和 FAT32。<br /><br />卷必须至少有 1 GB 空间。 Azure 备份服务器使用卷影复制服务 (VSS) 来创建数据快照；仅当卷至少有 1 GB 空间时，快照才能正常工作。|
|客户端计算机（64 位和 32 位）|Windows 8.1|物理服务器<br /><br />Hyper-V 虚拟机|V3、V2|文件<br /><br />受保护的卷必须采用 NTFS 格式。 不支持 FAT 和 FAT32。<br /><br />卷必须至少有 1 GB 空间。 Azure 备份服务器使用卷影复制服务 (VSS) 来创建数据快照；仅当卷至少有 1 GB 空间时，快照才能正常工作。|
|客户端计算机（64 位和 32 位）|Windows 8.1|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）|V3、V2|卷、共享、文件夹、文件、重复删除的卷<br /><br />受保护的卷必须采用 NTFS 格式。 不支持 FAT 和 FAT32。<br /><br />卷必须至少有 1 GB 空间。 Azure 备份服务器使用卷影复制服务 (VSS) 来创建数据快照；仅当卷至少有 1 GB 空间时，快照才能正常工作。|
|客户端计算机（64 位和 32 位）|Windows 8|物理服务器<br /><br />本地 Hyper-V 虚拟机|V3、V2|卷、共享、文件夹、文件、重复删除的卷<br /><br />受保护的卷必须采用 NTFS 格式。 不支持 FAT 和 FAT32。<br /><br />卷必须至少有 1 GB 空间。 Azure 备份服务器使用卷影复制服务 (VSS) 来创建数据快照；仅当卷至少有 1 GB 空间时，快照才能正常工作。|
|客户端计算机（64 位和 32 位）|Windows 8|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）|V3、V2|卷、共享、文件夹、文件、重复删除的卷<br /><br />受保护的卷必须采用 NTFS 格式。 不支持 FAT 和 FAT32。<br /><br />卷必须至少有 1 GB 空间。 Azure 备份服务器使用卷影复制服务 (VSS) 来创建数据快照；仅当卷至少有 1 GB 空间时，快照才能正常工作。|
|客户端计算机（64 位和 32 位）|Windows 7|物理服务器<br /><br />本地 Hyper-V 虚拟机|V3、V2|卷、共享、文件夹、文件、重复删除的卷<br /><br />受保护的卷必须采用 NTFS 格式。 不支持 FAT 和 FAT32。<br /><br />卷必须至少有 1 GB 空间。 Azure 备份服务器使用卷影复制服务 (VSS) 来创建数据快照；仅当卷至少有 1 GB 空间时，快照才能正常工作。|
|客户端计算机（64 位和 32 位）|Windows 7|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）|V3、V2|卷、共享、文件夹、文件、重复删除的卷<br /><br />受保护的卷必须采用 NTFS 格式。 不支持 FAT 和 FAT32。<br /><br />卷必须至少有 1 GB 空间。 Azure 备份服务器使用卷影复制服务 (VSS) 来创建数据快照；仅当卷至少有 1 GB 空间时，快照才能正常工作。|
|服务器（64 位）|Windows Server 2019|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /><br />VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /><br />物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3 <br />非 Nano Server|卷、共享、文件夹、文件、系统状态/裸机、已删除重复数据的卷|
|服务器（32 位和 64 位）|Windows Server 2016|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /><br />VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /><br />物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3、V2<br />非 Nano Server|卷、共享、文件夹、文件、系统状态/裸机、已删除重复数据的卷|
|服务器（32 位和 64 位）|Windows Server 2012 R2 - Datacenter 和 Standard|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /> <br /> Azure Stack|V3、V2|卷、共享、文件夹、文件<br /><br />Azure 备份服务器必须至少在 Windows Server 2012 R2 上运行才能保护 Windows Server 2012 已删除重复数据的卷。|
|服务器（32 位和 64 位）|Windows Server 2012 R2 - Datacenter 和 Standard|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|卷、共享、文件夹、文件、系统状态/裸机<br /><br />Azure 备份服务器必须在 Windows Server 2012 或 2012 R2 上运行才能保护 Windows Server 2012 已删除重复数据的卷。|
|服务器（32 位和 64 位）|Windows Server 2012/2012 SP1 - Datacenter 和 Standard|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3、V2|卷、共享、文件夹、文件、系统状态/裸机<br /><br />Azure 备份服务器必须至少在 Windows Server 2012 R2 上运行才能保护 Windows Server 2012 已删除重复数据的卷。|
|服务器（32 位和 64 位）|Windows Server 2012/2012 SP1 - Datacenter 和 Standard|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /> <br /> Azure Stack|V3、V2|卷、共享、文件夹、文件<br /><br />Azure 备份服务器必须至少在 Windows Server 2012 R2 上运行才能保护 Windows Server 2012 已删除重复数据的卷。|
|服务器（32 位和 64 位）|Windows Server 2012/2012 SP1 - Datacenter 和 Standard|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|卷、共享、文件夹、文件、系统状态/裸机<br /><br />Azure 备份服务器必须至少在 Windows Server 2012 R2 上运行才能保护 Windows Server 2012 已删除重复数据的卷。|
|服务器（32 位和 64 位）|Windows Server 2008 R2 SP1 - Standard 和 Enterprise|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3、V2<br />需要运行 SP1 并安装 [Windows Management Frame 4.0](https://www.microsoft.com/download/details.aspx?id=40855)|卷、共享、文件夹、文件、系统状态/裸机|
|服务器（32 位和 64 位）|Windows Server 2008 R2 SP1 - Standard 和 Enterprise|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /> <br /> Azure Stack|V3、V2<br />需要运行 SP1 并安装 [Windows Management Frame 4.0](https://www.microsoft.com/download/details.aspx?id=40855)|卷、共享、文件夹、文件|
|服务器（32 位和 64 位）|Windows Server 2008 R2 SP1 - Standard 和 Enterprise|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2<br />需要运行 SP1 并安装 [Windows Management Frame 4.0](https://www.microsoft.com/download/details.aspx?id=40855)|卷、共享、文件夹、文件、系统状态/裸机|
|服务器（32 位和 64 位）|Windows Server 2008 SP2|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|不支持|卷、共享、文件夹、文件、系统状态/裸机|
|服务器（32 位和 64 位）|Windows Server 2008 SP2|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|卷、共享、文件夹、文件、系统状态/裸机|
|服务器（32 位和 64 位）|Windows Storage Server 2008|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3、V2|卷、共享、文件夹、文件、系统状态/裸机|
|SQL Server|SQL Server 2017|物理服务器 <br /><br /> 本地 Hyper-V 虚拟机 <br /> <br /> Azure 虚拟机 <br /><br /> VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3|所有部署方案：数据库|
|SQL Server|SQL Server 2016 SP2|物理服务器 <br /><br /> 本地 Hyper-V 虚拟机 <br /> <br /> Azure 虚拟机 <br /><br /> VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2016 SP1|物理服务器 <br /><br /> 本地 Hyper-V 虚拟机 <br /> <br /> Azure 虚拟机 <br /><br /> VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2016|物理服务器 <br /><br /> 本地 Hyper-V 虚拟机 <br /> <br /> Azure 虚拟机 <br /><br /> VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2014|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2014|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2012 SP2|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2012 SP2|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2012 SP2|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2008 R2|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2008 R2|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2008 R2|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2008|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2008|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|SQL Server|SQL Server 2008|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|所有部署方案：数据库|
|Exchange|Exchange 2016|物理服务器<br/><br/> 本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3、V2|保护（所有部署方案）：独立 Exchange Server、数据库可用性组 (DAG) 中的数据库<br /><br />恢复（所有部署方案）：邮箱、DAG 中的邮箱数据库<br/><br/> 不支持通过 ReFS 备份 Exchange |
|Exchange|Exchange 2016|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|保护（所有部署方案）：独立 Exchange Server、数据库可用性组 (DAG) 中的数据库<br /><br />恢复（所有部署方案）：邮箱、DAG 中的邮箱数据库<br/><br/> 不支持通过 ReFS 备份 Exchange |
|Exchange|Exchange 2013|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3、V2|保护（所有部署方案）：独立 Exchange Server、数据库可用性组 (DAG) 中的数据库<br /><br />恢复（所有部署方案）：邮箱、DAG 中的邮箱数据库<br/><br/> 不支持通过 ReFS 备份 Exchange |
|Exchange|Exchange 2013|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|保护（所有部署方案）：独立 Exchange Server、数据库可用性组 (DAG) 中的数据库<br /><br />恢复（所有部署方案）：邮箱、DAG 中的邮箱数据库<br/><br/> 不支持通过 ReFS 备份 Exchange |
|Exchange|Exchange 2010|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3、V2|保护（所有部署方案）：独立 Exchange Server、数据库可用性组 (DAG) 中的数据库<br /><br />恢复（所有部署方案）：邮箱、DAG 中的邮箱数据库<br/><br/> 不支持通过 ReFS 备份 Exchange |
|Exchange|Exchange 2010|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|保护（所有部署方案）：独立 Exchange Server、数据库可用性组 (DAG) 中的数据库<br /><br />恢复（所有部署方案）：邮箱、DAG 中的邮箱数据库<br/><br/> 不支持通过 ReFS 备份 Exchange |
|SharePoint|SharePoint 2016|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /><br />Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /><br />VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|保护（所有部署方案）：场、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器<br /><br />请注意，不支持使用内容数据库的 SQL Server 2012 AlwaysOn 功能保护 SharePoint 场。|
|SharePoint|SharePoint 2013|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3、V2|保护（所有部署方案）：场、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器<br /><br />请注意，不支持使用内容数据库的 SQL Server 2012 AlwaysOn 功能保护 SharePoint 场。|
|SharePoint|SharePoint 2013|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）- <br /> <br /> Azure Stack|V3、V2|保护（所有部署方案）：场、SharePoint 搜索、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器<br /><br />请注意，不支持使用内容数据库的 SQL Server 2012 AlwaysOn 功能保护 SharePoint 场。|
|SharePoint|SharePoint 2013|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|保护（所有部署方案）：场、SharePoint 搜索、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器<br /><br />请注意，不支持使用内容数据库的 SQL Server 2012 AlwaysOn 功能保护 SharePoint 场。|
|SharePoint|SharePoint 2010|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /> <br /> Azure Stack|V3、V2|保护（所有部署方案）：场、SharePoint 搜索、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器|
|SharePoint|SharePoint 2010|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /> <br /> Azure Stack|V3、V2|保护（所有部署方案）：场、SharePoint 搜索、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器|
|SharePoint|SharePoint 2010|VMWare 中 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机上运行的工作负荷）<br /> <br /> Azure Stack|V3、V2|保护（所有部署方案）：场、SharePoint 搜索、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器|
|Hyper-V 主机 - Hyper-V 主机服务器、群集或 VM 上的 MABS 保护代理|Windows Server 2019|物理服务器<br /><br />本地 Hyper-V 虚拟机|V3|保护：Hyper-V 计算机、群集共享卷 (CSV)<br /><br />恢复：虚拟机，以及文件和文件夹、卷、虚拟硬盘的项级恢复|
|Hyper-V 主机 - Hyper-V 主机服务器、群集或 VM 上的 MABS 保护代理|Windows Server 2016|物理服务器<br /><br />本地 Hyper-V 虚拟机|V3、V2|保护：Hyper-V 计算机、群集共享卷 (CSV)<br /><br />恢复：虚拟机，以及文件和文件夹、卷、虚拟硬盘的项级恢复|
|Hyper-V 主机 - Hyper-V 主机服务器、群集或 VM 上的 MABS 保护代理|Windows Server 2012 R2 - Datacenter 和 Standard|物理服务器<br /><br />本地 Hyper-V 虚拟机|V3、V2|保护：Hyper-V 计算机、群集共享卷 (CSV)<br /><br />恢复：虚拟机，以及文件和文件夹、卷、虚拟硬盘的项级恢复|
|Hyper-V 主机 - Hyper-V 主机服务器、群集或 VM 上的 MABS 保护代理|Windows Server 2012 - Datacenter 和 Standard|物理服务器<br /><br />本地 Hyper-V 虚拟机|V3、V2|保护：Hyper-V 计算机、群集共享卷 (CSV)<br /><br />恢复：虚拟机，以及文件和文件夹、卷、虚拟硬盘的项级恢复|
|Hyper-V 主机 - Hyper-V 主机服务器、群集或 VM 上的 MABS 保护代理|Windows Server 2008 R2 SP1 - Enterprise 和 Standard|物理服务器<br /><br />本地 Hyper-V 虚拟机|V3、V2|保护：Hyper-V 计算机、群集共享卷 (CSV)<br /><br />恢复：虚拟机，以及文件和文件夹、卷、虚拟硬盘的项级恢复|
|Hyper-V 主机 - Hyper-V 主机服务器、群集或 VM 上的 MABS 保护代理|Windows Server 2008 SP2|物理服务器<br /><br />本地 Hyper-V 虚拟机|不支持|保护：Hyper-V 计算机、群集共享卷 (CSV)<br /><br />恢复：虚拟机，以及文件和文件夹、卷、虚拟硬盘的项级恢复|
|VMware VM|VMware vCenter/vSphere ESX/ESXi 许可版本 5.5/6.0/6.5 |物理服务器、 <br/>本地 Hyper-V VM、 <br/> VMWare 中的 Windows VM|V3、V2|群集共享卷 (CSV)、NFS 和 SAN 存储中的 VMware VM<br /> 文件和文件夹的项目级恢复仅适用于 Windows VM，不支持 VMware vApp。|
|VMware VM|[VMware vSphere 许可版本 6.7](backup-azure-backup-server-vmware.md#vmware-vsphere-67) |物理服务器、 <br/>本地 Hyper-V VM、 <br/> VMWare 中的 Windows VM|V3|群集共享卷 (CSV)、NFS 和 SAN 存储中的 VMware VM<br /> 文件和文件夹的项目级恢复仅适用于 Windows VM，不支持 VMware vApp。|
|Linux|以 Hyper-V 或 VMware 来宾身份运行的 Linux|物理服务器、 <br/>本地 Hyper-V VM、 <br/> VMWare 中的 Windows VM|V3、V2|Hyper-V 必须在 Windows Server 2012 R2 或 Windows Server 2016 上运行。 保护：整个虚拟机<br /><br />恢复：整个虚拟机 <br/><br/> 有关支持的 Linux 分发版和版本的完整列表，请参阅 [Azure 认可的分发中的 Linux](../virtual-machines/linux/endorsed-distros.md) 一文。|

## <a name="azure-expressroute-support"></a>Azure ExpressRoute 支持

如果 Azure ExpressRoute 配置了专用或 Microsoft 对等互连，则不能使用它将数据备份到 Azure。

如果 Azure ExpressRoute 配置了公共对等互连，则可以使用它将数据备份到 Azure。

>[!NOTE]
>对于新线路，公共对等互连已弃用。

## <a name="cluster-support"></a>群集支持

Azure 备份服务器可以保护以下群集应用程序中的数据：

- 文件服务器

- SQL Server

- Hyper-V - 如果使用横向扩展的 MABS 保护代理来保护 Hyper-V 群集，则无法为受保护的 Hyper-V 工作负荷添加辅助保护。

    如果在 Windows Server 2008 R2 上运行 Hyper-V，请确保安装知识库文章 [975354](https://support.microsoft.com/kb/975354) 中所述的更新。
    如果在群集配置中的 Windows Server 2008 R2 上运行 Hyper-V，请确保根据知识库文章 [971394](https://support.microsoft.com/kb/971394) 中所述安装 SP2。

- Exchange Server - Azure 备份服务器可以保护受支持 Exchange Server 版本的非共享磁盘群集（群集连续复制），而且还可以保护针对本地连续复制配置的 Exchange Server。

- SQL Server - Azure 备份服务器不支持备份托管在群集共享卷 (CSV) 上的 SQL Server 数据库。

Azure 备份服务器可以保护 MABS 服务器所在的同一个域中以及子域或受信任域中的群集工作负荷。 若要保护不受信任的域或工作组中的数据源，请对单个服务器使用 NTLM 或证书身份验证，或者只对群集使用证书身份验证。
