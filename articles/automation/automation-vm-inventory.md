---
title: "使用清单收集管理 Azure 虚拟机 | Microsoft Docs"
description: "使用清单收集管理虚拟机"
services: automation
keywords: "清单, 自动化, 更改, 跟踪"
author: jennyhunter-msft
ms.author: jehunte
ms.date: 09/13/2017
ms.topic: hero-article
manager: carmonm
ms.openlocfilehash: bfdd67ac1018f242e415da1a66d93f22bea8d054
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>使用清单收集管理 Azure 虚拟机

可以从 Azure 虚拟机的资源页为该虚拟机启用清单跟踪。 此方法提供一个基于浏览器的用户界面，用于设置和配置清单收集。

## <a name="before-you-begin"></a>开始之前
如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。
如果没有 Azure 虚拟机，请[创建虚拟机](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户
登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>从虚拟机资源页启用清单收集

1. 在 Azure 门户的左窗格中，选择“虚拟机”。
2. 在虚拟机列表中，选择一个虚拟机。
3. 在“操作”下方的“资源”菜单中选择“清单(预览版)”。  
    将在窗口顶部显示一个横幅，通知你该解决方案未启用。 
4. 若要启用该解决方案，请选择横幅。
5. 选择用于存储数据日志的 Log Analytics 工作区。  
    如果该区域没有可用工作区，则会提示创建默认工作区和自动化帐户。 
6. 若要开始载入计算机，请选择“启用”。

   ![查看载入选项](./media/automation-vm-inventory/inventory-onboarding-options.png)  
    状态栏会通知你正在启用解决方案。 此过程最多可能需要 15 分钟。 在此期间，可以关闭窗口，也可以让窗口保持打开状态。启用解决方案后，它会通知你。 可以从通知窗格监视部署状态。

   ![在载入后立即查看清单解决方案](./media/automation-vm-inventory/inventory-onboarded.png)

部署完成后，状态栏消失。 系统仍在收集清单数据，这些数据可能还不可见。 完整收集数据可能需要 24 小时。

## <a name="configure-your-inventory-settings"></a>配置清单设置

默认情况下，为收集配置了软件、Windows 服务和 Linux 守护程序。 若要收集 Windows 注册表和文件清单，需配置清单收集设置。

1. 在“清单(预览)”视图中，选择窗口顶部的“编辑设置”按钮。
2. 若要添加新的收集设置，请选择“Windows 注册表”、“Windows 文件”和“Linux 文件”选项卡，转到要添加的设置类别。 
3. 选择窗口顶部的“添加”。
4. 若要查看详细信息和每个设置属性的说明，请访问[清单文档页](https://aka.ms/configinventorydocs)。

## <a name="disconnect-your-virtual-machine-from-management"></a>将虚拟机与管理断开连接

若要从清单管理中删除虚拟机，请执行以下操作：

1. 在 Azure 门户的左窗格中选择“Log Analytics”，然后选择载入虚拟机时使用过的工作区。
2. 在“Log Analytics”窗口的“资源”菜单的“工作区数据源”类别下选择“虚拟机”。 
3. 在列表中选择要断开连接的虚拟机。 该虚拟机有一个绿色复选标记，位于“OMS 连接”列中“此工作区”的旁边。 
4. 在下一页的顶部，选择“断开连接”。
5. 在配置窗口中，选择“是”。  
    此操作断开计算机与管理的连接。

## <a name="next-steps"></a>后续步骤

* 若要了解如何管理虚拟机上文件和注册表设置中的更改，请参阅[使用更改跟踪解决方案跟踪环境中的软件更改](../log-analytics/log-analytics-change-tracking.md)。
* 若要了解如何管理虚拟机上的 Windows 和程序包更新，请参阅 [OMS 中的更新管理解决方案](../operations-management-suite/oms-solution-update-management.md)。
