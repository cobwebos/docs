---
title: 捕获浏览器跟踪以进行故障排除 | Microsoft Docs
description: 从浏览器跟踪捕获网络信息以帮助解决 Azure 门户的问题。
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 05/11/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: dba321d055e64d62ca91f95461c3299bee5f90d2
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714213"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>捕获浏览器跟踪以进行故障排除

如果你正在对 Azure 门户进行故障排除，并且需要联系 Microsoft 支持，我们建议你首先捕获浏览器跟踪和一些其他信息。 收集的信息可以在问题发生时提供有关门户的重要详细信息。 对于使用的浏览器中的开发人员工具，请按照本文中的步骤进行操作：Google Chrome 或 Microsoft Edge (Chromium)、Microsoft Edge (EdgeHTML)、Apple Safari 或 Firefox。

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome 和 Microsoft Edge (Chromium)

Google Chrome 和 (Chromium)都基于 [Chromium 开放源代码项目](https://www.chromium.org/Home)。 以下步骤演示如何使用开发人员工具，这在两个浏览器中非常相似。 有关详细信息，请参阅 [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) 和 [Microsoft Edge (Chromium) 开发工具](/microsoft-edge/devtools-guide-chromium)。

1. 登录 [Azure 门户](https://portal.azure.com)。 在开始跟踪之前登录很重要，这样跟踪就不会包含与登录相关的敏感信息。 

1. 使用[步骤记录器](https://support.microsoft.com/help/22878/windows-10-record-steps)开始记录在门户中执行的步骤。

1. 在门户中，导航到问题发生之前的步骤。

1. 按 F12 或选择![浏览器设置图标的屏幕截图](media/capture-browser-trace/chromium-icon-settings.png) > “其他工具” > “开发人员工具” 。

1. 默认情况下，浏览器只保留当前加载页的跟踪信息。 设置以下选项，以便浏览器保留所有跟踪信息，即使重现需要转到多个页面：

    1. 选择“网络”选项卡，然后选择“保留日志” 。

          ![“保留日志”屏幕截图](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. 依次选择“控制台”选项卡、“控制台设置”、“保留日志”  。 再次选择“控制台设置”以关闭设置窗格。

          ![“保留日志”屏幕截图](media/capture-browser-trace/chromium-console-preserve-log.png)

1. 依次选择“网络”选项卡、“停止记录网络日志”和“清除”  。

    ![“停止记录网络日志”和“清除”的屏幕截图](media/capture-browser-trace/chromium-stop-clear-session.png)

1. 选择“记录网络日志”，然后在门户中重现该问题。

    ![“启动分析会话”的屏幕截图](media/capture-browser-trace/chromium-start-session.png)

    将看到如下图所示的会话输出。

    ![浏览器跟踪结果的屏幕截图](media/capture-browser-trace/chromium-browser-trace-results.png)

1. 重现意外的门户行为后，选择“停止记录网络日志”，然后选择“导出 HAR”并保存文件 。

    ![“导出 HAR”的屏幕截图](media/capture-browser-trace/chromium-network-export-har.png)

1. 停止步骤记录器，并保存记录。

1. 在“浏览器开发人员工具”窗格中，选择“控制台”选项卡。右键单击其中一条消息，然后选择“另存为…”，并将控制台输出保存为文本文件。

    ![控制台输出屏幕截图](media/capture-browser-trace/chromium-console-select.png)

1. 将 HAR 文件、控制台输出和屏幕录制打包为压缩格式（如 .zip），并与 Microsoft 支持人员共享。

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

以下步骤说明如何在 Microsoft Edge (EdgeHTML) 中使用开发人员工具。 有关详细信息，请参阅 [Microsoft Edge (EdgeHTML) 开发人员工具](/microsoft-edge/devtools-guide)。

1. 登录 [Azure 门户](https://portal.azure.com)。 在开始跟踪之前登录很重要，这样跟踪就不会包含与登录相关的敏感信息。 

1. 使用[步骤记录器](https://support.microsoft.com/help/22878/windows-10-record-steps)开始记录在门户中执行的步骤。

1. 在门户中，导航到问题发生之前的步骤。

1. 按 F12 或选择![浏览器设置图标的屏幕截图](media/capture-browser-trace/edge-icon-settings.png) > “其他工具” > “开发人员工具” 。

1. 默认情况下，浏览器只保留当前加载页的跟踪信息。 设置以下选项，以便浏览器保留所有跟踪信息，即使重现需要转到多个页面：

    1. 选择“网络”选项卡，然后清除“导航上的清除项”选项 。

          ![“导航上的清除项”的屏幕截图](media/capture-browser-trace/edge-network-clear-entries.png)

    1. 选择“控制台”选项卡，然后选择“保留日志” 。

          ![“保留日志”屏幕截图](media/capture-browser-trace/edge-console-preserve-log.png)

1. 选择“网络”选项卡，然后选择“停止分析会话”和“清除会话”  。

    ![“停止分析会话”和“清除会话”的屏幕截图](media/capture-browser-trace/edge-stop-clear-session.png)

1. 选择“开始分析会话”，然后在门户中重现该问题。

    ![“启动分析会话”的屏幕截图](media/capture-browser-trace/edge-start-session.png)

    将看到如下图所示的会话输出。

    ![浏览器跟踪结果的屏幕截图](media/capture-browser-trace/edge-browser-trace-results.png)

1. 重现意外的门户行为后，选择“停止分析会话”，然后选择“导出 HAR”并保存文件 。

    ![“导出为 HAR”的屏幕截图](media/capture-browser-trace/edge-network-export-har.png)

1. 停止步骤记录器，并保存记录。

1. 在“浏览器开发人员工具”窗格中，选择“控制台”选项卡，然后展开窗口。 将光标放在控制台输出的开头，然后拖动并选择输出的全部内容。 右键单击，然后选择“复制”，并将控制台输出保存到文本文件中。

    ![控制台输出屏幕截图](media/capture-browser-trace/edge-console-select.png)

1. 将 HAR 文件、控制台输出和屏幕录制打包为压缩格式（如 .zip），并与 Microsoft 支持人员共享。

## <a name="apple-safari"></a>Apple Safari

以下步骤说明如何在 Apple Safari 中使用开发人员工具。 有关详细信息，请参阅 [Safari 开发人员工具概述](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)。

1. 在 Apple Safari 中启用开发人员工具：

    1. 选择“Safari”，然后选择“首选项” 。

        ![Safari 首选项的屏幕截图](media/capture-browser-trace/safari-preferences.png)

    1. 选择“高级”选项卡，然后选择“在菜单栏中显示开发菜单” 。

        ![Safari 高级首选项的屏幕截图](media/capture-browser-trace/safari-show-develop-menu.png)

1. 登录 [Azure 门户](https://portal.azure.com)。 在开始跟踪之前登录很重要，这样跟踪就不会包含与登录相关的敏感信息。 

1. 开始记录在门户中执行的步骤。 有关详细信息，请参阅[如何在 Mac 上记录屏幕](https://support.apple.com/HT208721)。

1. 在门户中，导航到问题发生之前的步骤。

1. 选择“开发”，然后选择“显示 Web 检查器” 。

    ![“显示 Web 检查器”的屏幕截图](media/capture-browser-trace/safari-show-web-inspector.png)

1. 默认情况下，浏览器只保留当前加载页的跟踪信息。 设置以下选项，以便浏览器保留所有跟踪信息，即使重现需要转到多个页面：

    1. 选择“网络”选项卡，然后选择“保留日志” 。

          ![“保留日志”屏幕截图](media/capture-browser-trace/safari-network-preserve-log.png)

    1. 选择“控制台”选项卡，然后选择“保留日志” 。

          ![“保留日志”屏幕截图](media/capture-browser-trace/safari-console-preserve-log.png)

1. 选择“网络”选项卡，然后选择“清除网络项” 。

    ![“清除网络项”的屏幕截图](media/capture-browser-trace/safari-clear-session.png)

1. 在门户中重现问题。 将看到如下图所示的会话输出。

    ![浏览器跟踪结果的屏幕截图](media/capture-browser-trace/safari-browser-trace-results.png)

1. 重现意外的门户行为后，选择“导出”并保存文件。

    ![“导出”的屏幕截图](media/capture-browser-trace/safari-network-export-har.png)

1. 停止屏幕记录器并保存记录。

1. 在“浏览器开发人员工具”窗格中，选择“控制台”选项卡，然后展开窗口。 将光标放在控制台输出的开头，然后拖动并选择输出的全部内容。 使用 Command-C 复制输出并将其保存到文本文件中。

    ![控制台输出屏幕截图](media/capture-browser-trace/safari-console-select.png)

1. 将 HAR 文件、控制台输出和屏幕录制打包为压缩格式（如 .zip），并与 Microsoft 支持人员共享。

## <a name="firefox"></a>Firefox

以下步骤说明如何在 Firefox 中使用开发人员工具。 有关详细信息，请参阅 [Firefox 开发人员工具](https://developer.mozilla.org/docs/Tools)。

1. 登录 [Azure 门户](https://portal.azure.com)。 在开始跟踪之前登录很重要，这样跟踪就不会包含与登录相关的敏感信息。 

1. 开始记录在门户中执行的步骤。 在 Windows 上使用[步骤记录器](https://support.microsoft.com/help/22878/windows-10-record-steps)，或者查看[如何在 Mac 上记录屏幕](https://support.apple.com/HT208721)。

1. 在门户中，导航到问题发生之前的步骤。

1. 按 F12 或选择![浏览器设置图标的屏幕截图](media/capture-browser-trace/firefox-icon-settings.png) > “Web 开发人员” > “切换工具” 。

1. 默认情况下，浏览器只保留当前加载页的跟踪信息。 设置以下选项，以便浏览器保留所有跟踪信息，即使重现需要转到多个页面：

    1. 选择“网络”选项卡，然后选择“保留日志” 。

          ![“保留日志”的屏幕截图](media/capture-browser-trace/firefox-network-persist-logs.png)

    1. 依次选择“控制台”选项卡、“控制台设置”、“保留日志”  。

          ![“保留日志”的屏幕截图](media/capture-browser-trace/firefox-console-persist-logs.png)

1. 选择“网络”选项卡，然后选择“清除” 。

    ![“清除”的屏幕截图](media/capture-browser-trace/firefox-clear-session.png)

1. 在门户中重现问题。 将看到如下图所示的会话输出。

    ![浏览器跟踪结果的屏幕截图](media/capture-browser-trace/firefox-browser-trace-results.png)

1. 重现意外的门户行为后，请选择“HAR 导出/导入”，然后选择“将所有内容另存为 HAR” 。

    ![“导出 HAR”的屏幕截图](media/capture-browser-trace/firefox-network-export-har.png)

1. 停止 Windows 上的步骤录制或 Mac 上的屏幕录制，并保存录制。

1. 在“浏览器开发人员工具”窗格中，选择“控制台”选项卡。右键单击其中一条消息，然后选择“将可见消息导出到”，并将控制台输出保存为文本文件。

    ![控制台输出屏幕截图](media/capture-browser-trace/firefox-console-select.png)

1. 将 HAR 文件、控制台输出和屏幕录制打包为压缩格式（如 .zip），并与 Microsoft 支持人员共享。

## <a name="next-steps"></a>后续步骤

[Azure 门户概述](azure-portal-overview.md)