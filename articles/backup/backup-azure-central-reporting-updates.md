---
title: 更新 Azure 备份中心报表内容包
description: 介绍如何更新 Power BI 中的 Azure 备份内容包
ms.reviewer: kasinh
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: dacurwin
ms.openlocfilehash: 4e217148db42e10e8fe2046cbd062f0708011e96
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689321"
---
# <a name="update-the-azure-backup-central-reporting-content-pack"></a>更新 Azure 备份中心报表内容包 

[Azure 备份内容包](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi)可用于查看关于集中备份的报表。 内容包定期更新，以添加更多的功能并修复 Bug。 本文介绍如何更新内容包。 还介绍如何推迟更新并查看按时间顺序所做的更新。

## <a name="get-updates-to-the-content-pack"></a>获取内容包的更新

### <a name="get-the-updated-content-pack"></a>获取更新的内容包
如果未对内容包副本进行任何更改，则其它将自动更新。 内容包更改时，你将在 Power BI 中收到通知和一封电子邮件通知。 可选择在方便时获取更新的内容包。 

### <a name="postpone-the-update"></a>推迟更新
最佳做法是将内容包导入到[自定义工作区](https://youtu.be/26zyOtyHPJM?t=1m57s)。 现在可以编辑报告。
如前所述，如果内容包发生更改，你会在 Power BI 中看到通知。 可以选择稍后获取内容包。 

## <a name="coming-soon"></a>即将推出
   
更新 Azure 备份内容包以支持更多的工作负载。 工作负载包括用于 IaaS VM 备份的 Azure SQL 数据库和 System Center Data Protection Manager。 此支持将添加到 Azure 备份和 Azure VM 备份的当前支持中。 这种支持意味着你可以在一个中心位置查看和分析所有备份数据。 [也可自定义报表](https://youtu.be/26zyOtyHPJM)以满足组织的需求。

正在对 Azure 备份内容包随附的预配置报表进行更改。 这些更改可使报表在各个工作负载中更有意义。 可在下方抢先了解即将发布的一组报告。

### <a name="summary"></a>总结
   
![总结](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>帐单

![帐单](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>符合性

![符合性](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>存储

![存储](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>备份项
![备份项](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>警报

![警报](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>作业(Job)

![作业(Job)](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>后续步骤

* [在整个组织中共享报表](https://youtu.be/26zyOtyHPJM)
* [Azure 备份常见问题](backup-azure-backup-faq.md)
