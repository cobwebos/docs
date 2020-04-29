---
title: Azure 备份支持矩阵，适用于 Azure Vm 中的 SQL Server 备份
description: 概括介绍了在 Azure Vm 中使用 Azure 备份服务备份 SQL Server 时的支持设置和限制。
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 79a7e30ab9240c489a66b547ff85bea7887131b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79409994"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Azure Vm 中 SQL Server 备份的支持矩阵

可以使用 Azure 备份来备份 Microsoft Azure 云平台上托管的 Azure Vm 中的 SQL Server 数据库。 本文总结了在 Azure Vm 中 SQL Server 备份的方案和部署的一般支持设置和限制。

## <a name="scenario-support"></a>方案支持

**支持** | **详细信息**
--- | ---
**支持的部署** | 支持 SQL 市场 Azure VM 和非市场（手动安装的 SQL Server）VM。
**支持的地理区域** | 澳大利亚东南部 (ASE)、东澳大利亚 (AE)、澳大利亚中部 (AC)、澳大利亚中部 2 (AC) <br> 巴西南部 (BRS)<br> 加拿大中部 (CNC)、加拿大东部 (CE)<br> 东南亚 (SEA)、东亚 (EA) <br> 美国东部 (EUS)、美国东部 2 (EUS2)、美国中西部 (WCUS)、美国西部 (WUS)；美国西部 2 (WUS 2) 美国中北部 (NCUS) 美国中部 (CUS) 美国中南部 (SCUS) <br> 印度中部 (INC)、印度南部 (INS)、印度西部 <br> 日本东部 (JPE)、日本西部 (JPW) <br> 韩国中部 (KRC)、韩国南部 (KRS) <br> 北欧 (NE)、西欧 <br> 英国南部 (UKS)、英国西部 (UKW) <br> US Gov 亚利桑那州、US Gov 弗吉尼亚州、US Gov 德克萨斯州、US DoD 中部、US DoD 东部 <br> 德国北部、德国中西部 <br> 瑞士北部、瑞士西部 <br> 法国中部 <br> 中国东部、中国东部 2、中国北部、中国北部 2
**支持的操作系统** | Windows Server 2019、Windows Server 2016、Windows Server 2012、Windows Server 2008 R2 SP1 <br/><br/> 目前不支持 Linux。
**支持的 SQL Server 版本** | [搜索产品生命周期页](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)中详细介绍了 SQL Server 2019、SQL Server 2017，[搜索产品生命周期页](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)中详细介绍了 SQL Server 2016 和 SP、SQL Server 2014、SQL Server 2012、SQL Server 2008 R2、SQL Server 2008 <br/><br/> Enterprise、Standard、Web、Developer、Express。
**支持的 .NET 版本** | 安装在 VM 上的 .NET Framework 4.5.2 或更高版本

## <a name="feature-consideration-and-limitations"></a>功能注意事项和限制

* SQL Server 备份可配置在 Azure 门户或 PowerShell 中****。 我们不支持 CLI。
* 此解决方案在 Azure 资源管理器 VM 和经典 VM 这两种[部署](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)上均受支持。
* 运行 SQL Server 的 VM 需要建立 Internet 连接才能访问 Azure 公共 IP 地址。
* 不支持 SQL Server**故障转移群集实例（FCI）** 。
* 不支持对镜像数据库和数据库快照执行备份和还原操作。
* 使用多个备份解决方案来备份独立的 SQL Server 实例或 SQL Always On 可用性组可能导致备份失败，请避免执行此操作。
* 如果通过相同或不同的解决方案单独备份可用性组的两个节点，可能也会导致备份失败。
* Azure 备份支持只读数据库的仅完整备份和仅复制完整备份类型****
* 无法保护包含大量文件的数据库。 支持的最大文件数约为 1000****。  
* 你可以备份到保管库中的 **~ 2000** SQL Server 数据库。 如果有大量数据库，可创建多个保管库。
* 一次最多可配置 50 个数据库的备份；此限制有助于优化备份负载****。
* 我们支持最大为**2 TB**的数据库;对于大于该性能问题的大小，可能会出现这种情况。
* 若要了解每个服务器可以保护多少个数据库，请考虑带宽、VM 大小、备份频率、数据库大小等因素。 [下载](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx)资源规划器，根据 VM 资源和备份策略来计算每个服务器可以拥有的数据库的近似数目。
* 当配置可用性组时，将根据几个因素从不同的节点获取备份。 下面概述了可用性组的备份行为。

### <a name="back-up-behavior-with-always-on-availability-groups"></a>备份包含 Always on 可用性组的行为

建议仅在可用性组（AG）的一个节点上配置备份。 始终在与主节点相同的区域中配置备份。 换句话说，你始终需要在配置备份的区域中存在主节点。 如果 AG 的所有节点都在配置了备份的同一区域，则无需考虑任何问题。

#### <a name="for-cross-region-ag"></a>对于跨区域 AG

* 无论备份首选项如何，备份都仅从配置了备份的同一区域中的节点运行。 这是因为不支持跨区域备份。 如果只有两个节点，而辅助节点在另一个区域中，则备份将继续从主节点运行（除非您的备份首选项为 "仅辅助副本"）。
* 如果某个节点故障转移到的区域不同于配置备份的区域，则备份将在故障转移区域中的节点上失败。

根据备份首选项和备份类型（完整/差异/日志/仅复制完整），从特定节点（主要/次要）获取备份。

#### <a name="backup-preference-primary"></a>备份首选项：主要

**备份类型** | **节点**
--- | ---
完全 | 基本
差异 | 基本
日志 |  基本
仅复制完整 |  基本

#### <a name="backup-preference-secondary-only"></a>备份首选项：仅辅助副本

**备份类型** | **节点**
--- | ---
完全 | 基本
差异 | 基本
日志 |  辅助副本
仅复制完整 |  辅助副本

#### <a name="backup-preference-secondary"></a>备份首选项：辅助

**备份类型** | **节点**
--- | ---
完全 | 基本
差异 | 基本
日志 |  辅助副本
仅复制完整 |  辅助副本

#### <a name="no-backup-preference"></a>无备份首选项

**备份类型** | **节点**
--- | ---
完全 | 基本
差异 | 基本
日志 |  辅助副本
仅复制完整 |  辅助副本

## <a name="next-steps"></a>后续步骤

了解如何[备份 Azure VM 上运行的 SQL Server 数据库](backup-azure-sql-database.md)。
