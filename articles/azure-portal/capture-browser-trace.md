---
title: 捕获用于疑难解答的浏览器跟踪 |Microsoft Docs
description: 从浏览器跟踪捕获网络信息，以帮助解决与 Azure 门户相关的问题。
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310690"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>捕获用于疑难解答的浏览器跟踪

如果对 Azure 门户的问题进行故障排除，并且你需要联系 Microsoft 支持部门，我们建议你首先捕获浏览器跟踪和其他一些信息。 收集的信息可以在出现问题时提供有关门户的重要详细信息。 按照本文中的步骤，了解你使用的浏览器中的开发人员工具： Google Chrome 或 Microsoft Edge （Chromium）、Microsoft Edge （EdgeHTML）或 Apple Safari。

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome 和 Microsoft Edge （Chromium）

Google Chrome 和 Microsoft Edge （Chromium）都基于[Chromium 开源项目](https://www.chromium.org/Home)。 以下步骤演示如何使用开发人员工具，这些工具在这两个浏览器中非常类似。 有关详细信息，请参阅[Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) And [Microsoft Edge （Chromium）开发人员工具](/microsoft-edge/devtools-guide-chromium)。

1. 登录 [Azure 门户](https://portal.azure.com)。 在开始跟踪_之前_登录，以便跟踪不包含与您的登录相关的敏感信息，这一点很重要。 

1. 开始使用[步骤记录器](https://support.microsoft.com/help/22878/windows-10-record-steps)记录在门户中执行的步骤。

1. 在门户中，导航到问题发生之前的步骤。

1. 按 F12 或选择 "浏览器设置" 图标 ![屏幕截图](media/capture-browser-trace/chromium-icon-settings.png) > **更多工具** > **开发人员工具**。

1. 默认情况下，浏览器仅为当前加载的页面保留跟踪信息。 设置以下选项，使浏览器保留所有跟踪信息，即使重现需要转到多个页面：

    1. 选择 "**网络**" 选项卡，然后选择 "**保留日志**"。

          !["保留日志" 屏幕截图](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. 选择 "**控制台**" 选项卡，选择 "**控制台设置**"，然后选择 "**保留日志**"。 再次选择**控制台设置**以关闭 "设置" 窗格。

          !["保留日志" 屏幕截图](media/capture-browser-trace/chromium-console-preserve-log.png)

1. 选择 "**网络**" 选项卡，然后选择 "**停止记录网络日志**" 和 "**清除**"。

    !["停止记录网络日志" 和 "清除" 的屏幕截图](media/capture-browser-trace/chromium-stop-clear-session.png)

1. 选择 "**记录网络日志**"，然后在门户中重现问题。

    !["启动分析会话" 的屏幕截图](media/capture-browser-trace/chromium-start-session.png)

    你将看到类似于下图的会话输出。

    ![浏览器跟踪结果的屏幕截图](media/capture-browser-trace/chromium-browser-trace-results.png)

1. 再现了意外的门户行为后，选择 "**停止记录网络日志**"，然后选择 "**导出 HAR** " 并保存文件。

    !["导出 HAR" 的屏幕截图](media/capture-browser-trace/chromium-network-export-har.png)

1. 停止步骤记录器，并保存该文件。

1. 返回浏览器开发人员工具窗格，选择 "**控制台**" 选项卡。右键单击，然后选择 "**另存为 ...** "，并将控制台输出保存到文本文件中。

    ![控制台输出的屏幕截图](media/capture-browser-trace/chromium-console-select.png)

1. 以压缩格式（例如 .zip）打包 HAR 文件、控制台输出和屏幕录制，并与 Microsoft 支持部门共享。

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge （EdgeHTML）

以下步骤演示如何使用 Microsoft Edge 中的开发人员工具（EdgeHTML）。 有关详细信息，请参阅[Microsoft Edge （EdgeHTML）开发人员工具](/microsoft-edge/devtools-guide)。

1. 登录 [Azure 门户](https://portal.azure.com)。 在开始跟踪_之前_登录，以便跟踪不包含与您的登录相关的敏感信息，这一点很重要。 

1. 开始使用[步骤记录器](https://support.microsoft.com/help/22878/windows-10-record-steps)记录在门户中执行的步骤。

1. 在门户中，导航到问题发生之前的步骤。

1. 按 F12 或选择 "浏览器设置" 图标 ![屏幕截图](media/capture-browser-trace/edge-icon-settings.png) > **更多工具** > **开发人员工具**。

1. 默认情况下，浏览器仅为当前加载的页面保留跟踪信息。 设置以下选项，使浏览器保留所有跟踪信息，即使重现需要转到多个页面：

    1. 选择 "**网络**" 选项卡，然后清除 "**导航时清除条目**" 选项。

          !["导航时清除项" 的屏幕截图](media/capture-browser-trace/edge-network-clear-entries.png)

    1. 选择 "**控制台**" 选项卡，然后选择 "**保留日志**"。

          !["保留日志" 屏幕截图](media/capture-browser-trace/edge-console-preserve-log.png)

1. 选择 "**网络**" 选项卡，然后选择 "**停止分析会话**" 和 "**清除会话**"。

    !["停止分析会话" 和 "清除会话" 的屏幕截图](media/capture-browser-trace/edge-stop-clear-session.png)

1. 选择 "**开始分析会话**"，并在门户中重现问题。

    !["启动分析会话" 的屏幕截图](media/capture-browser-trace/edge-start-session.png)

    你将看到类似于下图的会话输出。

    ![浏览器跟踪结果的屏幕截图](media/capture-browser-trace/edge-browser-trace-results.png)

1. 在重现意外的门户行为后，选择 "**停止分析会话**"，然后选择 "**导出为 HAR** " 并保存文件。

    !["导出为 HAR" 的屏幕截图](media/capture-browser-trace/edge-network-export-har.png)

1. 停止步骤记录器，并保存该文件。

1. 返回到浏览器开发人员工具窗格，选择 "**控制台**" 选项卡，然后展开该窗口。 将光标置于控制台输出的开头，并拖动并选择输出的全部内容。 右键单击，然后选择 "**复制**"，然后将控制台输出保存到文本文件中。

    ![控制台输出的屏幕截图](media/capture-browser-trace/edge-console-select.png)

1. 以压缩格式（例如 .zip）打包 HAR 文件、控制台输出和屏幕录制，并与 Microsoft 支持部门共享。

## <a name="apple-safari"></a>Apple Safari

以下步骤演示如何使用 Apple Safari 中的开发人员工具。 有关详细信息，请参阅[Safari 开发人员工具概述](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)。

1. 在 Apple Safari 中启用开发人员工具：

    1. 选择**Safari**，然后选择 "**首选项**"。

        ![Safari 首选项的屏幕截图](media/capture-browser-trace/safari-preferences.png)

    1. 选择 "**高级**" 选项卡，然后选择**菜单栏中的 "显示开发" 菜单**。

        ![Safari 高级首选项的屏幕截图](media/capture-browser-trace/safari-show-develop-menu.png)

1. 登录 [Azure 门户](https://portal.azure.com)。 在开始跟踪_之前_登录，以便跟踪不包含与您的登录相关的敏感信息，这一点很重要。 

1. 开始记录在门户中执行的步骤。 有关详细信息，请参阅[如何在 Mac 上录制屏幕](https://support.apple.com/HT208721)。

1. 在门户中，导航到问题发生之前的步骤。

1. 选择 "**开发**"，然后选择 "**显示 Web 检查器**"。

    !["显示 Web 检查器" 的屏幕截图](media/capture-browser-trace/safari-show-web-inspector.png)

1. 默认情况下，浏览器仅为当前加载的页面保留跟踪信息。 设置以下选项，使浏览器保留所有跟踪信息，即使重现需要转到多个页面：

    1. 选择 "**网络**" 选项卡，然后选择 "**保留日志**"。

          !["保留日志" 屏幕截图](media/capture-browser-trace/safari-network-preserve-log.png)

    1. 选择 "**控制台**" 选项卡，然后选择 "**保留日志**"。

          !["保留日志" 屏幕截图](media/capture-browser-trace/safari-console-preserve-log.png)

1. 选择 "**网络**" 选项卡，然后选择 "**清除网络项**"。

    !["清除网络项" 的屏幕截图](media/capture-browser-trace/safari-clear-session.png)

1. 在门户中再现问题。 你将看到类似于下图的会话输出。

    ![浏览器跟踪结果的屏幕截图](media/capture-browser-trace/safari-browser-trace-results.png)

1. 再现了意外的门户行为后，选择 "**导出**" 并保存文件。

    !["导出" 的屏幕截图](media/capture-browser-trace/safari-network-export-har.png)

1. 停止屏幕录制器并保存文件。

1. 返回到浏览器开发人员工具窗格，选择 "**控制台**" 选项卡，然后展开该窗口。 将光标置于控制台输出的开头，并拖动并选择输出的全部内容。 使用命令 C 复制输出，并将其保存到文本文件中。

    ![控制台输出的屏幕截图](media/capture-browser-trace/safari-console-select.png)

1. 以压缩格式（例如 .zip）打包 HAR 文件、控制台输出和屏幕录制，并与 Microsoft 支持部门共享。

## <a name="next-steps"></a>后续步骤

[Azure 门户概述](azure-portal-overview.md)