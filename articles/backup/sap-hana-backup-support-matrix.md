---
title: SAP HANA 备份支持矩阵
description: 本文介绍使用 Azure 备份来备份 Azure Vm 上的 SAP HANA 数据库时支持的方案和限制。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a0a7c25ec718dcd6a903d2149a8b3930fb25941e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514283"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>针对备份 Azure VM 上的 SAP HANA 数据库的支持矩阵

Azure 备份支持将 SAP HANA 数据库备份到 Azure。 本文总结了在使用 Azure 备份来备份 Azure Vm 上的 SAP HANA 数据库时所支持的方案和限制。

## <a name="onboard-to-the-public-preview"></a>加入公共预览版

加入公共预览版的方式如下：

* 在门户中，按照[本文](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)所述操作，将订阅 ID 注册到恢复服务服务提供程序。
* 对于 PowerShell，运行此 cmdlet。 完成时应显示“已注册”。

```PowerShell
Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
```

> [!NOTE]
> 日志备份的频率现在可以设置为最少15分钟。 日志备份仅在数据库的成功完整备份完成后开始流动。

## <a name="scenario-support"></a>方案支持

| **方案**               | **支持的配置**                                | **不支持的配置**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **拓扑**               | 仅在 Azure Linux Vm 中运行 SAP HANA                    | HANA 大型实例（B-HLI）                                   |
| **地域**                   | 澳大利亚东南部、澳大利亚东部巴西南部、加拿大中部、加拿大东部南部东亚、东亚美国东部、美国东部2、美国西部、美国西部、美国西部2、美国中北部、美国中部、美国中南部、美国中部、印度南部、日本东部、日本圣韩国中部、韩国南部北欧、西欧英国南部英国西部 | 澳大利亚中部、澳大利亚中部2中国东部、中国北部、中国东部2、中国北部2西部印度北部、法国南部德国北部、德国中西部瑞士北部、瑞士西部南非北部、南非西部、阿拉伯联合酋长国北部、阿拉伯联合酋长国 Central Azure 政府区域 |
| **操作系统版本**            | SLES 12 SP2、SP3 或 SP4           | SLES 15，RHEL                                                |
| **HANA 版本**          | Hana 1.x 上的 SDC，HANA 2.x 上的 MDC < = SPS04 Rev 44           | -                                                            |
| **HANA 部署**       | 单个 Azure VM 上的 SAP HANA-仅纵向扩展               | 横向扩展                                                    |
| **HANA 实例**         | 单个 Azure VM 上的单个 SAP HANA 实例-仅纵向扩展 | 单个 VM 上的多个 SAP HANA 实例                  |
| **HANA 数据库类型**    | 在1.x 上单一数据库容器（SDC）、在2.x 上的多数据库容器（MDC） | HANA 1.x 中的 MDC                                              |
| **HANA 数据库大小**     | 由 HANA 报告的 2 TB 完整备份大小 |                                                              |
| **备份类型**           | 完整备份、差异备份和日志备份                           | 增量快照                                       |
| **还原类型**          | 请参阅 SAP HANA 说明[1642148](https://launchpad.support.sap.com/#/notes/1642148)以了解支持的还原类型 |                                                              |
| **备份限制**          | 每个 SAP HANA 实例的最大大小为 2 TB 的完整备份大小  |                                                              |
| **特殊配置** |                                                              | SAP HANA + 动态分层 <br>  通过 LaMa 克隆            |

------

> [!NOTE]
> 当前不支持 SAP HANA 本机客户端（SAP HANA Studio/考核中心考核中心）进行备份和还原操作。



## <a name="next-steps"></a>后续步骤

* 了解如何[备份 Azure vm 上运行的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)
* 了解如何[还原在 Azure vm 上运行的 SAP HANA 数据库](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* 了解如何[管理使用 Azure 备份进行备份的 SAP HANA 数据库](sap-hana-db-manage.md)
* 了解[关于 SAP HANA 数据库备份常见问题的疑难解答](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)
