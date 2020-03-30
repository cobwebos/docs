---
title: SAP HANA 备份支持矩阵
description: 在本文中，了解使用 Azure 备份在 Azure VM 上备份 SAP HANA 数据库时受支持的方案和限制。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 8496dc4996cac68535bfe9be30e4b5f72e2d5721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252436"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>针对备份 Azure VM 上的 SAP HANA 数据库的支持矩阵

Azure 备份支持将 SAP HANA 数据库备份到 Azure。 本文总结了使用 Azure 备份在 Azure VM 上备份 SAP HANA 数据库时支持的方案和限制。

> [!NOTE]
> 日志备份频率现在可以设置为至少 15 分钟。 日志备份仅在数据库成功完成完整备份后才开始流动。

## <a name="scenario-support"></a>方案支持

| **方案**               | **支持的配置**                                | **不支持的配置**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **拓扑**               | 仅在 Azure Linux VM 中运行的 SAP HANA                    | HANA 大型实例 （HLI）                                   |
| **地理**                   | **Ga：**<br> **美洲**– 美国中部、美国东部 2、美国东部、美国中北部、美国中南部、美国西部 2、美国中西部、美国西部、加拿大中部、加拿大东部、巴西南部 <br> **亚太地区**– 澳大利亚中部，澳大利亚中部 2，澳大利亚东部，澳大利亚东南部，日本东部，日本西部，韩国中部，韩国南部，东亚，东南亚，印度中部，印度南部，印度西部，中国东部，中国北部，中国东部2，中国北 2 <br> **欧洲**– 西欧、北欧、法国中部、英国南部、英国西部、德国北部、德国中西部、瑞士北部、瑞士西部、瑞士中部北部 <br> **非洲 / ME** - 南非北部，南非西部，阿联酋北部，阿联酋中部  <BR>  **Azure 政府区域** | 法国南部，德国中部，德国东北部，美国政府IOWA |
| **操作系统版本**            | 带有 SP2、SP3 或 SP4 的 SLES 12;SLES 15 带 SP1                              | RHEL                                                |
| **HANA 版本**          | HANA 1.x 上的 SDC，HANA 2.x <上的 MDC = SPS04 Rev 46       | -                                                            |
| **HANA 部署**       | 单个 Azure VM 上的 SAP HANA - 仅向上扩展。 <br><br> 对于高可用性部署，两个不同计算机上的节点都被视为具有单独数据链的单个节点。               | 横向扩展 <br><br> 在高可用性部署中，备份不会自动故障转移到辅助节点。 配置备份应为每个节点单独完成。                                           |
| **HANA 实例**         | 单个 Azure VM 上的单个 SAP HANA 实例 - 仅向上扩展 | 单个 VM 上的多个 SAP HANA 实例                  |
| **HANA 数据库类型**    | 单数据库容器 （SDC） 上 1.x，多数据库容器 （MDC）在 2.x 上 | HANA 1.x 中的 MDC                                              |
| **HANA 数据库大小**     | 2-TB 完整备份大小，由 HANA 报告）                   |                                                              |
| **备份类型**           | 完整、差异和日志备份                          | 增量快照                                       |
| **还原类型**          | 请参阅 SAP HANA 注释[1642148](https://launchpad.support.sap.com/#/notes/1642148)以了解支持的还原类型 |                                                              |
| **备份限制**          | 每个 SAP HANA 实例的完整备份大小高达 2 TB         |                                                              |
| **特殊配置** |                                                              | SAP HANA + 动态分层 <br>  通过 LaMa 进行克隆        |

------

> [!NOTE]
> 当前不支持来自 SAP HANA 本机客户端（SAP HANA 工作室/驾驶舱/DBA 驾驶舱）的备份和还原操作。

## <a name="next-steps"></a>后续步骤

* 了解如何[备份在 Azure VM 上运行的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* 了解如何[还原在 Azure VM 上运行的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* 了解如何[管理使用 Azure 备份进行备份的 SAP HANA 数据库](sap-hana-db-manage.md)
* 了解[关于 SAP HANA 数据库备份常见问题的疑难解答](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
