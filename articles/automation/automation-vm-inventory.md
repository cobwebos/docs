---
title: 使用清单收集管理 Azure 虚拟机 | Microsoft Docs
description: 使用清单收集管理虚拟机
services: automation
keywords: 清单, 自动化, 更改, 跟踪
author: jennyhunter-msft
ms.author: jehunte
ms.date: 03/30/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6eb1a77613c6f7784e251bb99a03e6ca7e1f7017
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>使用清单收集管理 Azure 虚拟机

可以从 Azure 虚拟机的资源页为该虚拟机启用清单跟踪。 此方法提供一个基于浏览器的用户界面，用于设置和配置清单收集。

## <a name="before-you-begin"></a>开始之前

如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)。

本文假定你有一个要在其上配置解决方案的 VM。 如果没有 Azure 虚拟机，请[创建虚拟机](../virtual-machines/windows/quick-create-portal.md)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>从虚拟机资源页启用清单收集

1. 在 Azure 门户的左窗格中，选择“虚拟机”。
2. 在虚拟机列表中，选择一个虚拟机。
3. 在“操作”下方的“资源”菜单中选择“清单”。
4. 选择用于存储数据日志的 Log Analytics 工作区。
    如果该区域没有可用工作区，则会提示创建默认工作区和自动化帐户。
5. 若要开始载入计算机，请选择“启用”。

   ![查看载入选项](./media/automation-vm-inventory/inventory-onboarding-options.png)

    状态栏会通知你正在启用解决方案。 此过程最多可能需要 15 分钟。 在此期间，可以关闭窗口，也可以让窗口保持打开状态。启用解决方案后，它会通知你。 可以从通知窗格监视部署状态。

   ![在载入后立即查看清单解决方案](./media/automation-vm-inventory/inventory-onboarded.png)

部署完成后，状态栏消失。 系统仍在收集清单数据，这些数据可能还不可见。 完整收集数据可能需要 24 小时。

## <a name="configure-your-inventory-settings"></a>配置清单设置

默认情况下，为收集配置了软件、Windows 服务和 Linux 守护程序。 若要收集 Windows 注册表和文件清单，需配置清单收集设置。

1. 在“清单”视图中，选择窗口顶部的“编辑设置”按钮。
2. 若要添加新的收集设置，请选择“Windows 注册表”、“Windows 文件”和“Linux 文件”选项卡，转到要添加的设置类别。
3. 选择相应的类别，然后单击窗口顶部的“添加”。

下表提供了可以为各种类别配置的每个属性的相关信息。

### <a name="windows-registry"></a>Windows 注册表

|属性  |说明  |
|---------|---------|
|已启用     | 确定是否应用了设置        |
|项目名称     | 要跟踪的文件的友好名称        |
|组     | 一个组名，用于对文件进行逻辑分组        |
|Windows 注册表项   | 用于查看文件的路径，例如“HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="windows-files"></a>Windows 文件

|属性  |说明  |
|---------|---------|
|已启用     | 确定是否应用了设置        |
|项目名称     | 要跟踪的文件的友好名称        |
|组     | 一个组名，用于对文件进行逻辑分组        |
|输入路径     | 用于查看文件的路径，例如“c:\temp\myfile.txt”

### <a name="linux-files"></a>Linux 文件

|属性  |说明  |
|---------|---------|
|已启用     | 确定是否应用了设置        |
|项目名称     | 要跟踪的文件的友好名称        |
|组     | 一个组名，用于对文件进行逻辑分组        |
|输入路径     | 用于查看文件的路径，例如“/etc/*.conf”       |
|路径类型     | 要跟踪的项的类型，可能值为“文件”和“目录”        |
|递归     | 确定在查找要跟踪的项时是否使用递归。        |
|使用 Sudo     | 此设置决定了在查找该项时是否使用 Sudo。         |
|链接     | 此设置决定了在遍历目录时如何处理符号链接。<br> **忽略** - 忽略符号链接，不包括引用的文件/目录<br>**追随** - 在递归期间追随符号链接，并且包括引用的文件/目录<br>**管理** - 追随符号链接并允许修改返回内容的处置方式      |

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
* 若要了解如何管理虚拟机上的 Windows 和程序包更新，请参阅 [Azure 中的更新管理解决方案](../operations-management-suite/oms-solution-update-management.md)。
