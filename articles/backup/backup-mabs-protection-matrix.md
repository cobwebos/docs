---
title: "Azure 备份服务器可备份哪些内容 | Microsoft Docs"
description: "本文提供了一个支持矩阵，列出了受 Azure 备份服务器 v2 保护的所有工作负荷、数据类型和安装。"
services: backup
documentation center: 
author: markgalioto
ms.assetid: 
ms.service: backup
ms.workload: storage-backup-recovery
keywords: 
ms.date: 11/28/2017
ms.topic: article
ms.author: markgal,masaran
manager: carmonm
ms.openlocfilehash: 45e3e7e1288c9c468619bd553963cfd018298c32
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="azure-backup-server-protection-matrix"></a>Azure 备份服务器保护矩阵

本文列出了可使用 Azure 备份服务器保护的各种服务器和工作负荷。 以下矩阵列出了可使用 Azure 备份服务器 v1 和 v2 保护的内容。

## <a name="protection-support-matrix"></a>保护支持矩阵

|工作负载|版本|Azure 备份服务器</br> installation|Azure 备份</br> 服务器 v2|Azure 备份</br> 服务器 v1 |保护和恢复|
|------------|-----------|--------------------|--------------------------------------------|--------------------------------|---------------------------|
|System Center VMMM|VMM 2016、<br/>VMM 2012、SP1、R2|物理服务器<br /><br />Hyper-V 虚拟机|Y|Y|所有部署方案：数据库|
|客户端计算机（64 位和 32 位）|Windows 10|物理服务器<br /><br />Hyper-V 虚拟机<br /><br />VMware 虚拟机|Y|Y|文件<br /><br />受保护的卷必须是 NTFS。 不支持保护 FAT 和 FAT32。<br /><br />卷必须至少为 1GB。 DPM 使用卷影复制服务 (VSS) 获取数据快照，且只有卷至少为 1GB 时，快照才有效。|
|客户端计算机（64 位和 32 位）|Windows 8.1|物理服务器<br /><br />Hyper-V 虚拟机|Y|Y|文件<br /><br />受保护的卷必须是 NTFS。 不支持保护 FAT 和 FAT32。<br /><br />卷必须至少为 1GB。 DPM 使用卷影复制服务 (VSS) 获取数据快照，且只有卷至少为 1GB 时，快照才有效。|
|客户端计算机（64 位和 32 位）|Windows 8.1|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y|文件<br /><br />受保护的卷必须是 NTFS 且至少为 1GB。|
|客户端计算机（64 位和 32 位）|Windows 8|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|文件<br /><br />受保护的卷必须是 NTFS 且至少为 1GB。|
|客户端计算机（64 位和 32 位）|Windows 8|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y|文件<br /><br />受保护的卷必须是 NTFS 且至少为 1GB。|
|客户端计算机（64 位和 32 位）|Windows 7|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|文件<br /><br />受保护的卷必须是 NTFS 且至少为 1GB。|
|客户端计算机（64 位和 32 位）|Windows 7|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y |文件<br /><br />受保护的卷必须是 NTFS 且至少为 1GB。|
|客户端计算机（64 位和 32 位）|Windows Vista SP2|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|文件<br /><br />受保护的卷必须是 NTFS 且至少为 1GB。|
|客户端计算机（64 位和 32 位）|Windows Vista SP1|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|文件<br /><br />受保护的卷必须是 NTFS 且至少为 1GB。|
|客户端计算机（64 位和 32 位）|Windows Vista|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|文件<br /><br />受保护的卷必须是 NTFS 且至少为 1GB。|
|客户端计算机（64 位和 32 位）|Windows Vista|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|卷、共享、文件夹、文件、系统状态/裸机）、删除重复数据的卷|
|服务器（32 位和 64 位）|Windows Server 2016|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /><br />VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）<br /><br />物理服务器<br /><br />本地 Hyper-V 虚拟机|Y<br /><br />非 Nano Server|N|卷、共享、文件夹、文件、系统状态/裸机）、删除重复数据的卷|
|服务器（32 位和 64 位）|Windows Server 2012 R2 - 数据中心和标准版|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）|Y|Y |卷、共享、文件夹、文件<br /><br />必须至少在 Windows Server 2012 R2 上运行 DPM，才能保护 Windows Server 2012 删除重复数据的卷。|
|服务器（32 位和 64 位）|Windows Server 2012 R2 - 数据中心和标准版|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y|卷、共享、文件夹、文件、系统状态/裸机）<br /><br />必须在 Windows Server 2012 或 2012 R2 上运行 DPM，才能保护 Windows Server 2012 删除重复数据的卷。|
|服务器（32 位和 64 位）|Windows Server 2012/Windows Server 2012 SP1 - 数据中心和标准版|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|卷、共享、文件夹、文件、系统状态/裸机<br /><br />必须至少在 Windows Server 2012 R2 上运行 DPM，才能保护 Windows Server 2012 删除重复数据的卷。|
|服务器（32 位和 64 位）|Windows Server 2012/Windows Server 2012 SP1 - 数据中心和标准版|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）|Y|Y|卷、共享、文件夹、文件<br /><br />必须至少在 Windows Server 2012 R2 上运行 DPM，才能保护 Windows Server 2012 删除重复数据的卷。|
|服务器（32 位和 64 位）|Windows Server 2012/Windows Server 2012 SP1 - 数据中心和标准版|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y|卷、共享、文件夹、文件、系统状态/裸机<br /><br />必须至少在 Windows Server 2012 R2 上运行 DPM，才能保护 Windows Server 2012 删除重复数据的卷。|
|服务器（32 位和 64 位）|Windows Server 2008 R2 SP1 - 标准版和企业版|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y<br /><br />需运行 SP1 并安装 [Windows Management Frame 4.0](https://www.microsoft.com/en-us/download/details.aspx?id=40855)|Y|卷、共享、文件夹、文件、系统状态/裸机|
|服务器（32 位和 64 位）|Windows Server 2008 R2 SP1 - 标准版和企业版|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）|Y<br /><br />需运行 SP1 并安装 [Windows Management Frame 4.0](https://www.microsoft.com/en-us/download/details.aspx?id=40855)|Y |卷、共享、文件夹、文件|
|服务器（32 位和 64 位）|Windows Server 2008 R2 SP1 - 标准版和企业版|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y<br /><br />需运行 SP1 并安装 [Windows Management Frame 4.0](https://www.microsoft.com/en-us/download/details.aspx?id=40855)|Y |卷、共享、文件夹、文件、系统状态/裸机|
|服务器（32 位和 64 位）|Windows Server 2008 R2|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|卷、共享、文件夹、文件、系统状态/裸机|
|服务器（32 位和 64 位）|Windows Server 2008 R2|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）|N|Y|卷、共享、文件夹、文件|
|服务器（32 位和 64 位）|Windows Server 2008 R2|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|N|Y|卷、共享、文件夹、文件、系统状态/裸机|
|服务器（32 位和 64 位）|Windows Server 2008|物理服务器<br /><br />本地 Hyper-V 虚拟机|N|Y|卷、共享、文件夹、文件、系统状态/裸机|
|服务器（32 位和 64 位）|Windows Server 2008|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y |卷、共享、文件夹、文件、系统状态/裸机|
|服务器（32 位和 64 位）|Windows Storage Server 2008|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|卷、共享、文件夹、文件、系统状态/裸机|
|SQL Server|SQL Server 2016|物理服务器 <br /><br /> 本地 Hyper-V 虚拟机 <br /> <br /> Azure 虚拟机 <br /><br /> VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y |N|所有部署方案：数据库|
|SQL Server|SQL Server 2014|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）|Y|Y |所有部署方案：数据库|
|SQL Server|SQL Server 2014|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y|所有部署方案：数据库|
|SQL Server|SQL Server 2012 SP2|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y |所有部署方案：数据库|
|SQL Server|SQL Server 2012 SP2|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）|Y|Y|所有部署方案：数据库|
|SQL Server|SQL Server 2012 SP2|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y|所有部署方案：数据库|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|所有部署方案：数据库|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）|Y|Y|所有部署方案：数据库|
|SQL Server|SQL Server 2012、SQL Server 2012 SP1|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y|所有部署方案：数据库|
|SQL Server|SQL Server 2008 R2|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|所有部署方案：数据库|
|SQL Server|SQL Server 2008 R2|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）|Y|Y|所有部署方案：数据库|
|SQL Server|SQL Server 2008 R2|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y |所有部署方案：数据库|
|SQL Server|SQL Server 2008|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|所有部署方案：数据库|
|SQL Server|SQL Server 2008|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）|Y|Y|所有部署方案：数据库|
|SQL Server|SQL Server 2008|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y|所有部署方案：数据库|
|Exchange|Exchange 2016|物理服务器<br/><br/> 本地 Hyper-V 虚拟机|Y|Y|保护（所有部署方案）：独立 Exchange 服务器，数据库可用性组 (DAG) 下的数据库<br /><br />恢复（所有部署方案）：邮箱，DAG 下的邮箱数据库|
|Exchange|Exchange 2016|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y|保护（所有部署方案）：独立 Exchange 服务器，数据库可用性组 (DAG) 下的数据库<br /><br />恢复（所有部署方案）：邮箱，DAG 下的邮箱数据库|
|Exchange|Exchange 2013|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|保护（所有部署方案）：独立 Exchange 服务器，数据库可用性组 (DAG) 下的数据库<br /><br />恢复（所有部署方案）：邮箱，DAG 下的邮箱数据库|
|Exchange|Exchange 2013|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y |保护（所有部署方案）：独立 Exchange 服务器，数据库可用性组 (DAG) 下的数据库<br /><br />恢复（所有部署方案）：邮箱，DAG 下的邮箱数据库|
|Exchange|Exchange 2010|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|保护（所有部署方案）：独立 Exchange 服务器，数据库可用性组 (DAG) 下的数据库<br /><br />恢复（所有部署方案）：邮箱，DAG 下的邮箱数据库|
|Exchange|Exchange 2010|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y |保护（所有部署方案）：独立 Exchange 服务器，数据库可用性组 (DAG) 下的数据库<br /><br />恢复（所有部署方案）：邮箱，DAG 下的邮箱数据库|
|Exchange|Exchange 2007|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|保护（所有部署方案）：存储组<br /><br />恢复（所有部署方案）：存储组、数据库、邮箱|
|Exchange|Exchange 2007|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y |保护（所有部署方案）：存储组<br /><br />恢复（所有部署方案）：存储组、数据库、邮箱|
|SharePoint|SharePoint 2016|物理服务器<br /><br />本地 Hyper-V 虚拟机<br /><br />Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）<br /><br />VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y |N|保护（所有部署方案）：场，前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器<br /><br />请注意，不支持保护使用内容数据库的 SQL Server 2012 AlwaysOn 功能的 SharePoint 场。|
|SharePoint|SharePoint 2013|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|保护（所有部署方案）：场，前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器<br /><br />请注意，不支持保护使用内容数据库的 SQL Server 2012 AlwaysOn 功能的 SharePoint 场。|
|SharePoint|SharePoint 2013|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）- DPM 2012 R2 更新汇总 3 及更高版本|Y|Y|保护（所有部署方案）：场、SharePoint 搜索、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器<br /><br />请注意，不支持保护使用内容数据库的 SQL Server 2012 AlwaysOn 功能的 SharePoint 场。|
|SharePoint|SharePoint 2013|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y |保护（所有部署方案）：场、SharePoint 搜索、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器<br /><br />请注意，不支持保护使用内容数据库的 SQL Server 2012 AlwaysOn 功能的 SharePoint 场。|
|SharePoint|SharePoint 2010|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|保护（所有部署方案）：场、SharePoint 搜索、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器|
|SharePoint|SharePoint 2010|Azure 虚拟机（工作负荷作为 Azure 虚拟机运行时）|Y|Y |保护（所有部署方案）：场、SharePoint 搜索、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器|
|SharePoint|SharePoint 2010|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y|保护（所有部署方案）：场、SharePoint 搜索、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器|
|SharePoint|SharePoint 2007|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|保护（所有部署方案）：场、SharePoint 搜索、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器|
|SharePoint|SharePoint 2007|VMWare 中的 Windows 虚拟机（保护在 VMWare 中的 Windows 虚拟机中运行的工作负荷）|Y|Y|保护（所有部署方案）：场、SharePoint 搜索、前端 Web 服务器内容<br /><br />恢复（所有部署方案）：场、数据库、Web 应用程序、文件或列表项、SharePoint 搜索、前端 Web 服务器|
|Hyper-V 主机 - Hyper-V 主机服务器、群集或 VM 上的 DPM 保护代理|Windows Server 2016|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|N|保护：Hyper-V 计算机、群集共享卷 (CSV)<br /><br />恢复：文件和文件夹、卷、虚拟硬盘驱动器的虚拟机项目级恢复|
|Hyper-V 主机 - Hyper-V 主机服务器、群集或 VM 上的 DPM 保护代理|Windows Server 2012 R2 - 数据中心和标准版|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|保护：Hyper-V 计算机、群集共享卷 (CSV)<br /><br />恢复：文件和文件夹、卷、虚拟硬盘驱动器的虚拟机项目级恢复|
|Hyper-V 主机 - Hyper-V 主机服务器、群集或 VM 上的 DPM 保护代理|Windows Server 2012 - 数据中心和标准版|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|保护：Hyper-V 计算机、群集共享卷 (CSV)<br /><br />恢复：文件和文件夹、卷、虚拟硬盘驱动器的虚拟机项目级恢复|
|Hyper-V 主机 - Hyper-V 主机服务器、群集或 VM 上的 DPM 保护代理|Windows Server 2008 R2 SP1 - 数据中心和标准版|物理服务器<br /><br />本地 Hyper-V 虚拟机|Y|Y|保护：Hyper-V 计算机、群集共享卷 (CSV)<br /><br />恢复：文件和文件夹、卷、虚拟硬盘驱动器的虚拟机项目级恢复|
|Hyper-V 主机 - Hyper-V 主机服务器、群集或 VM 上的 DPM 保护代理|Windows Server 2008|物理服务器<br /><br />本地 Hyper-V 虚拟机|N|N|保护：Hyper-V 计算机、群集共享卷 (CSV)<br /><br />恢复：文件和文件夹、卷、虚拟硬盘驱动器的虚拟机项目级恢复|
|VMware VM|VMware 服务器 5.5、6.0 或 6.5 |本地 Hyper-V 虚拟机|Y|Y（具有 UR1）|群集共享卷 (CSV)、NFS 和 SAN 存储中的 VMware VM<br /> 仅适用于 Windows 的项目级恢复文件和文件夹<br /> 不支持 VMware vApp|
|Linux|以 Hyper-V 或 VMware 来宾身份运行的 Linux|本地 Hyper-V 虚拟机|Y|Y|Hyper-V 必须在 Windows Server 2012 R2 或 Windows Server 2016 上运行。 保护：整个虚拟机<br /><br />恢复：整个虚拟机 <br/><br/> 有关支持的 Linux 分发版和版本的完整列表，请参阅 [Azure 认可的分发中的 Linux](../virtual-machines/linux/endorsed-distros.md) 一文。|

## <a name="cluster-support"></a>群集支持
Azure 备份服务器可保护以下群集应用程序中的数据：

-   文件服务器

-   SQL Server

-   Hyper-V - 如果使用扩展的 DPM 保护来保护 Hyper-V 群集，则无法为受保护的 Hyper-V 工作负荷添加辅助保护。

    如果在 Windows Server 2008 R2 上运行 Hyper-V，请务必安装知识库 [975354](https://support.microsoft.com/en-us/kb/975354) 中描述的更新。
    如果在群集配置中的 Windows Server 2008 R2 上运行 Hyper-V，请务必安装 SP2 和知识库 [971394](https://support.microsoft.com/en-us/kb/971394)。

-   Exchange Server - Azure 备份服务器可保护受支持 Exchange Server 版本的非共享磁盘群集（群集连续的复制），还可保护针对本地连续复制配置的 Exchange Server。

-   SQL Server - Azure 备份服务器不支持备份托管在群集共享卷 (CSV) 上的 SQL Server 数据库。

Azure 备份服务器可保护与 DPM 服务器位于同一域中的群集工作负荷，也可保护位于子域或受信任域中的群集工作负荷。 如果要保护不受信任域或工作组中的数据源，请针对单个服务器使用 NTLM 或证书身份验证，或针对群集仅使用证书身份验证。
