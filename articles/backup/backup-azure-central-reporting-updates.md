---
title: 更新 Azure 备份中心报告内容包
description: 介绍如何更新 Power BI 中的 Azure 备份内容包
services: backup
documentationcenter: ''
author: adigan
manager: shivamg
editor: ''
ms.assetid: 59df5bec-d959-457d-8731-7b20f7f1013e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: adigan;
ms.openlocfilehash: 07774234849d96b9a44678b68ca7b13b6b4830f9
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39266645"
---
# <a name="updating-azure-backup-central-reporting-content-pack"></a>更新 Azure 备份中心报告内容包 

[Azure 备份内容包](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi)可用于集中查看备份报表。 内容包定期更新，以添加更多的功能和进行 Bug 修复。 本文将介绍有关更新内容包、延迟此更新和逐步更新的步骤。

## <a name="how-to-get-updates-to-the-content-pack"></a>如何获取内容包的更新

### <a name="to-get-the-updated-content-pack"></a>获取更新的内容包
如果未对内容包进行任何更改，则其副本将自动更新。 如果已更改内容包，则将在 PowerBI 中获得通知，并收到类似的电子邮件通知。 可选择在便利之时获取更新的内容包。 

### <a name="to-delay-the-update"></a>延迟更新
最佳做法是将内容包导入到[自定义工作区](https://youtu.be/26zyOtyHPJM?t=1m57s)。 现在可对报表进行编辑。
如上所述，如果已更改内容包，则将在 PowerBI 中看到通知。 可以选择稍后获取内容包。 

## <a name="coming-soon"></a>即将支持
   
正在更新 Azure 备份内容包，除了对 MAB 和 Azure VM 备份的当前支持外，还如 IaaS VM 备份中的 SQL 和 SC DPM 一样支持更多的工作负载。 这意味着，你很快就能够在一个中心位置查看和分析所有备份数据。 [也可自定义报表](https://youtu.be/26zyOtyHPJM)以满足组织的需求。

为了使报表跨工作负载更有意义，即将更改预配置有 Azure 备份内容包的一组报表。 可在下方抢先了解即将推出的一组报表：

### <a name="summary"></a>摘要
   
![摘要](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>计费

![计费](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>合规性

![合规性](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>存储

![存储](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>备份项
![BackupItems](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>警报

![警报](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>作业

![作业](.\media\backup-azure-central-reporting\AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>后续步骤：

* [在整个组织中共享报表](https://youtu.be/26zyOtyHPJM)
* [Azure 备份常见问题](backup-azure-backup-faq.md)
