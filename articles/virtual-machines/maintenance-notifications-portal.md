---
title: 使用门户进行维护通知
description: 查看在 Azure 中运行的虚拟机的维护通知，并使用门户启动自助服务维护。
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 46fcc825ac49f0181ac74e9c3e2deaea577f3329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115736"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>使用门户处理计划维护通知

**本文适用于同时运行 Linux 和 Windows 的虚拟机。**

计划[运行](maintenance-notifications.md)波计划后，可以检查受影响的虚拟机列表。 

可以使用 Azure 门户来查找计划进行维护的 VM。

1. 登录到 Azure[门户](https://portal.azure.com)。

2. 在左侧导航栏中，单击“虚拟机”****。

3. 在"虚拟机"窗格中，选择 **"编辑列"** 按钮以打开可用列的列表。

4. 选择并添加以下列：

   **维护状态**：显示 VM 的维护状态。 下面是可能的值：
      
      | “值” | 描述 |
      |-------|-------------|
      | 立即启动 | 虚拟机位于自助维护窗口中，用户可以自行启动维护。 请参阅以下内容，了解如何在虚拟机上启动维护。 | 
      | 计划 | 已安排虚拟机进行维护，无需用户启动维护。 若要了解维护时段，可以在此视图中选择“维护 - 计划”时段，也可以单击 VM。 | 
      | 已经更新 | 你的 VM 已经更新，此时无需任何进一步的操作。 | 
      | 稍后重试 | 已经启动维护，但没有成功。 稍后将能够使用自助式维护选项。 | 
      | 立即重试 | 可以重试以前未成功的自行启动的维护。 | 
      | - | 计划内维护流程不处理你的 VM。 |
      

   维护 - 自助服务时段****：显示可以自行启动 VM 维护的时间范围。
   
   维护 - 计划的时段****：显示 Azure 维护你的 VM 以完成维护的时间范围。 



## <a name="notification-and-alerts-in-the-portal"></a>门户中的通知和警报

Azure 通过向订阅所有者和共有者组发送电子邮件来传达计划维护的安排。 可以通过创建 Azure 活动日志警报，为此通信添加其他收件人和频道。 有关详细信息，请参阅[创建有关服务通知的活动日志警报](../azure-monitor/platform/alerts-activity-log-service-notifications.md)。

请确保将**事件类型**设置为 **"计划维护**"，将**服务**设置为**虚拟机缩放集**和/或**虚拟机**。

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>从门户启动虚拟机维护

在查看虚拟机详细信息时，将能够看到更多维护相关的详细信息。  
如果虚拟机包含在计划的大量维护中，则会在在虚拟机详细信息视图的顶部添加新的通知功能区。 此外，如有必要，可以添加一个新选项来启动维护。 


单击维护通知以查看维护页面，其中包含计划维护的更多详细信息。 从那里，您将能够开始对 VM**进行维护**。

开始维护后，虚拟机将受到维护，维护状态得以更新，在几分钟内反映结果。

如果错过了自助时段，则由 Azure 维护 VM 时，你将仍可以看到该时段。 


## <a name="next-steps"></a>后续步骤

您还可以使用[Azure CLI](maintenance-notifications-cli.md)或[PowerShell](maintenance-notifications-powershell.md)来处理计划维护。