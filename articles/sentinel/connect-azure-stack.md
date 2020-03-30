---
title: 将 Azure 堆栈虚拟机装到 Azure 哨兵 |微软文档
description: 本文介绍如何在 Azure Stack 虚拟机上预配 Azure 监视器、更新和配置管理虚拟机扩展，并开始使用 Sentinel 监视它们。
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a8213bd57936f95870324950204dbd6c1473739a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588512"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>将 Azure 堆栈虚拟机连接到 Azure 哨兵




使用 Azure 哨兵，可以在一个位置监视在 Azure 和 Azure 堆栈上运行的 VM。 要将 Azure 堆栈计算机装板到 Azure Sentinel，首先需要将虚拟机扩展添加到现有 Azure 堆栈虚拟机。 

连接 Azure 堆栈计算机后，请从基于数据显示见解的仪表板库中选择。 这些仪表板可以轻松自定义，以满足您的需要。



## <a name="add-the-virtual-machine-extension"></a>添加虚拟机扩展名 

将**Azure 监视器、更新和配置管理**虚拟机扩展添加到 Azure 堆栈上运行的虚拟机。 

1. 在新的浏览器选项卡中，登录到 Azure[堆栈门户](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)。
2. 转到 **"虚拟机"** 页，选择要使用 Azure Sentinel 保护的虚拟机。 有关如何在 Azure 堆栈上创建虚拟机的信息，请参阅[使用 Azure 堆栈门户创建 Windows 服务器 VM](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal)或使用 Azure[堆栈门户创建 Linux 服务器 VM。](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)
3. 选择**扩展**。 此时将显示此虚拟机上安装的虚拟机扩展列表。
4. 单击"**添加"** 选项卡。"**新资源**"菜单边栏选项卡将打开并显示可用虚拟机扩展的列表。 
5. 选择**Azure 监视器、更新和配置管理**扩展，然后单击"**创建**"。 将打开 **"安装扩展配置**"窗口。

   ![Azure 监视器、更新和配置管理设置](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > 如果市场中未列出**Azure 监视器、更新和配置管理**扩展，请与 Azure Stack 运算符联系以使其可用。

6. 在 Azure 哨兵菜单上，选择**工作区设置**后跟**高级**，然后复制**工作区 ID**和**工作区键（主键）。** 
1. 在 Azure 堆栈**安装扩展窗口中**，将它们粘贴到指示的字段中，然后单击"**确定**"。
1. 扩展安装完成后，其状态将显示为 **"预配成功**"。 虚拟机最多可能需要一个小时才能显示在 Azure Sentinel 门户中。

有关安装和配置 Windows 代理的详细信息，请参阅[连接 Windows 计算机](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)。

有关如何排查 Linux 代理问题，请参阅[排查 Azure Log Analytics Linux 代理问题](../azure-monitor/platform/agent-linux-troubleshoot.md)。

在 Azure 上的 Azure Sentinel 门户中，在**虚拟机**下，您可以概览所有 VM 和计算机及其状态。 

## <a name="clean-up-resources"></a>清理资源
如果不再需要，可以通过 Azure Stack 门户删除虚拟机中的扩展。

删除扩展：

1. 打开 **Azure Stack 门户**。
2. 转到“虚拟机”页，选择要从中删除扩展的虚拟机。****
3. 选择“扩展”，然后选择“Microsoft.EnterpriseCloud.Monitoring”扩展。********
4. 单击 **"卸载**"并确认您的选择。

## <a name="next-steps"></a>后续步骤

要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- 将数据从[常见错误格式设备](connect-common-event-format.md)流式传输到 Azure Sentinel。
