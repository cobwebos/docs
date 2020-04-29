---
title: 使用 DPM 将 SharePoint 场备份到 Azure
description: 本文概述如何在 Azure 中使用 DPM/Azure 备份服务器保护 SharePoint 场
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 0199495e3b0eb002e58c096ed9abf05d46f43f97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80054116"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>使用 DPM 将 SharePoint 场备份到 Azure

使用 System Center Data Protection Manager (DPM) 将 SharePoint 场备份到 Microsoft Azure，其方法与备份其他数据源极为类似。 Azure 备份提供灵活的备份计划来创建每日、每周、每月或每年备份点，并提供适用于各种备份点的保留策略选项。 利用 DPM，不仅可以存储本地磁盘副本以实现快速的恢复时间目标 (RTO)，还可以将副本存储到 Azure 进行经济高效的长期保留。

使用 DPM 将 SharePoint 备份到 Azure 是一个非常类似的过程，可在本地将 SharePoint 备份到 DPM。 本文中将注明 Azure 的特定注意事项。

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>SharePoint 支持的版本与相关保护方案

有关支持的 SharePoint 和用于进行备份的 DPM 的版本的列表，请参阅 [DPM 可以备份什么？](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019#applications-backup)

## <a name="before-you-start"></a>开始之前

在将 SharePoint 场备份到 Azure 之前，需要确保满足几个条件。

### <a name="prerequisites"></a>先决条件

在继续之前，请确保符合使用 Microsoft Azure 备份保护工作负荷的所有[先决条件](backup-azure-dpm-introduction.md#prerequisites-and-limitations)。 先决条件包括如下任务：创建备份保管库、下载保管库凭据、安装 Azure 备份代理，以及在保管库中注册 DPM/Azure 备份服务器。

有关其他先决条件和限制，可参阅 DPM 上的[备份 SharePoint 一](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#prerequisites-and-limitations)文。

## <a name="configure-backup"></a>配置备份

要备份 SharePoint 场，则使用 ConfigureSharePoint.exe 配置 SharePoint 保护，然后在 DPM 中创建保护组。 有关说明，请参阅 DPM 文档中的[配置备份](https://docs.microsoft.com//system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#configure-backup)。

## <a name="monitoring"></a>监视

若要监视备份作业，请按照[监视 DPM 备份](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#monitoring)中的说明进行操作

## <a name="restore-sharepoint-data"></a>还原 SharePoint 数据

若要了解如何使用 DPM 从磁盘还原 SharePoint 项，请参阅[还原 sharepoint 数据](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#restore-sharepoint-data)。

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>使用 DPM 从 Azure 还原 SharePoint 数据库

1. 若要恢复 SharePoint 内容数据库，请浏览各个恢复点（如上所示），并选择要还原的恢复点。

    ![DPM SharePoint 保护 8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. 双击 SharePoint 恢复点以显示可用的 SharePoint 目录信息。

   > [!NOTE]
   > 由于 SharePoint 场在 Azure 中受长期保留保护，因此 DPM 服务器上没有可用的目录信息（元数据）。 这样，每当需要恢复时间点 SharePoint 内容数据库时，都需要重新编录 SharePoint 场。
   >
   >
3. 单击“重新编目”  。

    ![DPM SharePoint 保护 10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    此时显示“**云重新编录**”状态窗口。

    ![DPM SharePoint 保护 11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    完成编录后，状态更改为“成功”  。 单击“**关闭**”。

    ![DPM SharePoint 保护 12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. 单击 DPM“恢复”选项卡中显示的 SharePoint 对象，获取内容数据库结构  。 右键单击相应的项，并单击“**恢复**”。

    ![DPM SharePoint 保护 13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. 此时，请按照本文前面介绍的恢复步骤，从磁盘恢复 Sharepoint 内容数据库。

## <a name="switching-the-front-end-web-server"></a>切换前端 Web 服务器

如果有多个前端 web 服务器，并且想要切换 DPM 用于保护场的服务器，请按照[切换前端 Web 服务器](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#switching-the-front-end-web-server)中的说明进行操作。

## <a name="next-steps"></a>后续步骤

* [Azure 备份服务器和 DPM - 常见问题解答](backup-azure-dpm-azure-server-faq.md)
* [排查 System Center Data Protection Manager 问题](backup-azure-scdpm-troubleshooting.md)
