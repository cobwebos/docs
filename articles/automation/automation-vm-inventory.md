---
title: "使用清单收集管理 Azure VM | Microsoft Docs"
description: "使用清单收集管理 VM"
services: automation
keywords: "清单, 自动化, 更改, 跟踪"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 5291d112c2cdf157543fe301d5a5c80f3fe561ae
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>使用清单收集管理 Azure 虚拟机

可以从计算机资源页为 Azure 虚拟机启用清单跟踪。 此方法提供一个基于浏览器的用户界面，用于设置和配置清单收集。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。
如果没有 Azure 虚拟机，请在开始之前创建[虚拟机](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal)。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>从虚拟机资源页启用清单收集

1. 在左侧屏幕中选择“虚拟机”。
1. 从列表中选择虚拟机。
1. 从“操作”下方的资源菜单中选择“清单(预览)”。
1. 将在页顶部显示一个横幅，通知你该解决方案未启用。 单击横幅以启用该解决方案。
1. 选择用于存储数据日志的 Log Analytics 工作区。 如果该区域没有可用工作区，则会提示创建默认工作区和自动化帐户。 单击“启用”开始载入计算机。

   ![查看载入选项](./media/automation-vm-inventory/inventory-onboarding-options.png)  

1. 状态栏会通知你正在启用解决方案。 此过程最多可能需要 15 分钟。 在此期间，可以关闭边栏选项卡，也可以使边栏选项卡保持打开（启用解决方案后，它会报告）。 可以从通知窗格监视部署状态。

   ![在载入后立即查看清单解决方案](./media/automation-vm-inventory/inventory-onboarded.png)

1. 部署完成后，状态栏将会消失。 此时，系统仍在收集清单数据，这些数据尚不可见。 完整收集数据可能需要 24 小时。

## <a name="configure-your-inventory-settings"></a>配置清单设置

默认情况下，为收集配置了软件、Windows 服务和 Linux 守护程序。 若要收集 Windows 注册表和文件清单，需配置清单收集设置。

1. 从“清单(预览)”视图中，选择页面顶部的“编辑设置”按钮。
2. 若要新增收集设置，请使用读取 **Windows 注册表**、**Windows 文件**和 **Linux 文件**的选项卡导航到要添加的设置类别。 单击页面顶部的“添加”。
3. 若要查看详细信息和每个设置属性的说明，请访问[清单文档页](https://aka.ms/configinventorydocs)。

## <a name="disconnecting-your-virtual-machine-from-management"></a>将虚拟机与管理断开连接

若要从清单管理中删除虚拟机，请执行以下操作：

1. 在 Azure 门户的左侧菜单上，单击“Log Analytics”，然后单击选择载入虚拟机时使用的工作区。
1. 在 Log Analytics 页上，在资源菜单的“工作区数据源”类别下选择“虚拟机”。 
1. 从列表中选择要断开连接的虚拟机。 “OMS 连接”列中显示“此工作区”的文本旁边将有绿色复选标记。 单击下一页顶部的“断开连接”，并在显示确认对话框时单击“是”，以便将虚拟机与管理断开连接。

## <a name="next-steps"></a>后续步骤

* 若要了解如何管理虚拟机上文件和注册表设置中的更改，请参阅[更改跟踪](../log-analytics/log-analytics-change-tracking.md)。
* 若要了解如何管理虚拟机上的 Windows 和程序包更新，请参阅[更新管理](../operations-management-suite/oms-solution-update-management.md)

