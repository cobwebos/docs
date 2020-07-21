---
title: 适用于 Azure VM 中 SQL Server 备份的 Azure 备份支持矩阵
description: 提供有关在使用 Azure 备份服务备份 Azure VM 中的 SQL Server 时的支持设置和限制摘要。
ms.topic: conceptual
ms.date: 03/05/2020
ms.custom: references_regions
ms.openlocfilehash: 4d197f8b3c1ed74ef45c1f7942ead52ccef0c14a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86513177"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>适用于 Azure VM 中 SQL Server 备份的支持矩阵

可以使用 Azure 备份来备份 Microsoft Azure 云平台上托管的 Azure Vm 中的 SQL Server 数据库。 本文汇总了 Azure VM 中的 SQL Server 备份方案和部署的一般支持设置和限制。

## <a name="scenario-support"></a>方案支持

**支持** | **详细信息**
--- | ---
**支持的部署** | 支持 SQL 市场 Azure VM 和非市场（手动安装的 SQL Server）VM。
**支持的区域** | 澳大利亚东南部 (ASE)、东澳大利亚 (AE)、澳大利亚中部 (AC)、澳大利亚中部 2 (AC) <br> 巴西南部 (BRS)<br> 加拿大中部 (CNC)、加拿大东部 (CE)<br> 东南亚 (SEA)、东亚 (EA) <br> 美国东部 (EUS)、美国东部 2 (EUS2)、美国中西部 (WCUS)、美国西部 (WUS)；美国西部 2 (WUS 2) 美国中北部 (NCUS) 美国中部 (CUS) 美国中南部 (SCUS) <br> 印度中部 (INC)、印度南部 (INS)、印度西部 <br> 日本东部 (JPE)、日本西部 (JPW) <br> 韩国中部 (KRC)、韩国南部 (KRS) <br> 北欧 (NE)、西欧 <br> 英国南部 (UKS)、英国西部 (UKW) <br> US Gov 亚利桑那州、US Gov 弗吉尼亚州、US Gov 德克萨斯州、US DoD 中部、US DoD 东部 <br> 德国北部、德国中西部 <br> 瑞士北部、瑞士西部 <br> 法国中部 <br> 中国东部、中国东部 2、中国北部、中国北部 2
**受支持的操作系统** | Windows Server 2019、Windows Server 2016、Windows Server 2012、Windows Server 2008 R2 SP1 <br/><br/> 目前不支持 Linux。
**支持的 SQL Server 版本** | SQL Server 2019、SQL Server 2017（详见[“搜索产品生命周期”页](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017)）、SQL Server 2016 和 SP（详见[“搜索产品生命周期”页](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack)）、SQL Server 2014、SQL Server 2012、SQL Server 2008 R2、SQL Server 2008 <br/><br/> Enterprise、Standard、Web、Developer、Express。
**支持的 .NET 版本** | 安装在 VM 上的 .NET Framework 4.5.2 或更高版本

## <a name="feature-consideration-and-limitations"></a>功能注意事项和限制

* SQL Server 备份可配置在 Azure 门户或 PowerShell 中。 我们不支持 CLI。
* 此解决方案在 Azure 资源管理器 VM 和经典 VM 这两种[部署](../azure-resource-manager/management/deployment-models.md)上均受支持。
* 运行 SQL Server 的 VM 需要建立 Internet 连接才能访问 Azure 公共 IP 地址。
* 不支持 SQL Server 故障转移群集实例 (FCI)。
* 不支持对镜像数据库和数据库快照执行备份和还原操作。
* 使用多个备份解决方案来备份独立的 SQL Server 实例或 SQL Always On 可用性组可能导致备份失败，请避免执行此操作。
* 如果通过相同或不同的解决方案单独备份可用性组的两个节点，可能也会导致备份失败。
* Azure 备份支持只读数据库的仅完整备份和仅复制完整备份类型
* 无法保护包含大量文件的数据库。 支持的最大文件数约为 1000。  
* 在一个保管库中最多可以备份约 2000 个 SQL Server 数据库。 如果有大量数据库，可创建多个保管库。
* 一次最多可配置 50 个数据库的备份；此限制有助于优化备份负载。
* 我们支持最大 2 TB 大小的数据库；对于超过此大小的数据库，可能会出现性能问题。
* 若要了解每个服务器可以保护多少个数据库，请考虑带宽、VM 大小、备份频率、数据库大小等因素。 [下载](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx)资源规划器，它根据 VM 资源和备份策略来计算每个服务器可以具有的大致数据库数。
* 配置可用性组时，将基于几个因素从不同节点获取备份。 下面概述了可用性组的备份行为。

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Always On 可用性组的备份行为

建议只在可用性组 (AG) 的一个节点上配置备份。 应该始终在主节点所在的区域配置备份。 换句话说，必须始终确保主节点存在于进行备份配置的区域。 如果 AG 的所有节点位于进行备份配置的区域，则没有任何可担心的事情。

#### <a name="for-cross-region-ag"></a>对于跨区域 AG

* 不管备份首选项如何，备份都仅在那些与备份配置在同一区域的节点中运行。 这是因为跨区域备份不受支持。 如果只有两个节点，而辅助节点位于另一区域，备份会继续在主节点中运行（除非备份首选项为“仅限辅助节点”）。
* 如果节点故障转移与备份配置不在同一区域，则已故障转移的区域中的节点上的备份会失败。

根据备份首选项和备份类型（完整/差异/日志/仅复制完整），从特定节点（主要/次要）获取备份。

#### <a name="backup-preference-primary"></a>备份首选项：主要

**备份类型** | **Node**
--- | ---
完整 | 主要
差异 | 主要
日志 |  主要
仅复制完整 |  主要

#### <a name="backup-preference-secondary-only"></a>备份首选项：仅次要节点

**备份类型** | **Node**
--- | ---
完整 | 主要
差异 | 主要
日志 |  辅助副本
仅复制完整 |  次要

#### <a name="backup-preference-secondary"></a>备份首选项：次要

**备份类型** | **Node**
--- | ---
完整 | 主要
差异 | 主要
日志 |  辅助副本
仅复制完整 |  次要

#### <a name="no-backup-preference"></a>无备份首选项

**备份类型** | **Node**
--- | ---
完整 | 主要
差异 | 主要
日志 |  辅助副本
仅复制完整 |  次要

## <a name="next-steps"></a>后续步骤

了解如何[备份 Azure VM 上运行的 SQL Server 数据库](backup-azure-sql-database.md)。
