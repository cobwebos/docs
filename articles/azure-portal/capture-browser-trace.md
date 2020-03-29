---
title: 捕获浏览器跟踪以进行故障排除 | Microsoft Docs
description: 捕获浏览器跟踪中的网络信息，以帮助排查 Azure 门户的问题。
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310690"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>捕获浏览器跟踪以进行故障排除

如果要对 Azure 门户的问题进行故障排除，并且需要联系 Microsoft 支持人员，我们建议您首先捕获浏览器跟踪和一些附加信息。 收集的信息可以提供在出现问题时有关门户的重要详细信息。 请按照本文中有关您使用的浏览器中的开发人员工具的步骤：Google Chrome 或 Microsoft 边缘（铬）、微软边缘（EdgeHTML）或 Apple Safari。

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome 和 Microsoft Edge (Chromium)

谷歌铬和微软边缘（铬）都基于[铬开源项目](https://www.chromium.org/Home)。 以下步骤说明如何使用开发人员工具，这些工具在这两个浏览器中非常类似。 有关详细信息，请参阅[Chrome 开发工具和](https://developers.google.com/web/tools/chrome-devtools)[微软边缘（铬）开发人员工具](/microsoft-edge/devtools-guide-chromium)。

1. 登录到 Azure[门户](https://portal.azure.com)。 必须在开始跟踪之前登录，以免跟踪包含登录相关的敏感信息。__ 

1. 使用[步骤录制器](https://support.microsoft.com/help/22878/windows-10-record-steps)开始录制在门户中执行的步骤。

1. 在门户中，导航到问题发生之前的那个步骤。

1. 按 F12![或选择浏览器设置的](media/capture-browser-trace/chromium-icon-settings.png) > 屏幕截图图标**更多工具** > **开发人员工具**。

1. 默认情况下，浏览器只为当前加载的页面保留跟踪信息。 设置以下选项，使浏览器保留所有跟踪信息，即使在重现过程中需要转到多个页面：

    1. 选择"**网络**"选项卡，然后选择 **"保留日志**"。

          ![“保留日志”的屏幕截图](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. 依次选择“控制台”选项卡、“控制台设置”、“保留日志”。************ 再次选择“控制台设置”关闭设置窗格。****

          ![“Preserve Log”（保留日志）的屏幕截图](media/capture-browser-trace/chromium-console-preserve-log.png)

1. 选择“网络”选项卡，然后依次选择“停止记录网络日志”、“清除”。************

    ![“停止记录网络日志”和“清除”的屏幕截图](media/capture-browser-trace/chromium-stop-clear-session.png)

1. 选择“记录网络日志”，然后在门户中重现问题。****

    ![“启动分析会话”的屏幕截图](media/capture-browser-trace/chromium-start-session.png)

    将会看到如下图所示的会话输出。

    ![浏览器跟踪结果的屏幕截图](media/capture-browser-trace/chromium-browser-trace-results.png)

1. 重现意外的门户行为后，依次选择“停止记录网络日志”、“导出 HAR”并保存文件。********

    ![“导出 HAR”的屏幕截图](media/capture-browser-trace/chromium-network-export-har.png)

1. 停止步骤录制器并保存文件。

1. 返回浏览器开发人员工具窗格中，选择 **"控制台**"选项卡。右键单击，然后选择"**保存为..."，** 然后将控制台输出保存到文本文件中。

    ![控制台输出的屏幕截图](media/capture-browser-trace/chromium-console-select.png)

1. 以压缩格式（例如 .zip）打包 HAR 文件、控制台输出和屏幕录制内容，并将其与 Microsoft 支持人员共享。

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

以下步骤说明如何在 Microsoft Edge (EdgeHTML) 中使用开发人员工具。 有关详细信息，请参阅 [Microsoft Edge (EdgeHTML) 开发人员工具](/microsoft-edge/devtools-guide)。

1. 登录到 Azure[门户](https://portal.azure.com)。 必须在开始跟踪之前登录，以免跟踪包含登录相关的敏感信息。__ 

1. 使用[步骤录制器](https://support.microsoft.com/help/22878/windows-10-record-steps)开始录制在门户中执行的步骤。

1. 在门户中，导航到问题发生之前的那个步骤。

1. 按 F12![或选择浏览器设置的](media/capture-browser-trace/edge-icon-settings.png) > 屏幕截图图标**更多工具** > **开发人员工具**。

1. 默认情况下，浏览器只为当前加载的页面保留跟踪信息。 设置以下选项，使浏览器保留所有跟踪信息，即使在重现过程中需要转到多个页面：

    1. 选择“网络”选项卡，然后清除“清除浏览项”选项。********

          ![“清除浏览项”的屏幕截图](media/capture-browser-trace/edge-network-clear-entries.png)

    1. 依次选择“Console”（控制台）选项卡、“Preserve Log”（保留日志）。********

          ![“Preserve Log”（保留日志）的屏幕截图](media/capture-browser-trace/edge-console-preserve-log.png)

1. 选择“网络”选项卡，然后依次选择“停止分析会话”、“清除会话”。************

    ![“停止分析会话”和“清除会话”的屏幕截图](media/capture-browser-trace/edge-stop-clear-session.png)

1. 选择“启动分析会话”，然后在门户中重现问题。****

    ![“启动分析会话”的屏幕截图](media/capture-browser-trace/edge-start-session.png)

    将会看到如下图所示的会话输出。

    ![浏览器跟踪结果的屏幕截图](media/capture-browser-trace/edge-browser-trace-results.png)

1. 重现意外的门户行为后，依次选择“停止分析会话”、“导出为 HAR”并保存文件。********

    ![“导出为 HAR”的屏幕截图](media/capture-browser-trace/edge-network-export-har.png)

1. 停止步骤录制器并保存文件。

1. 返回浏览器开发人员工具窗格，选择“控制台”选项卡，然后展开窗口。**** 将光标置于控制台输出的开头，然后拖动并选择输出的整个内容。 单击右键并选择“复制”，然后将控制台输出保存到文本文件中。****

    ![控制台输出的屏幕截图](media/capture-browser-trace/edge-console-select.png)

1. 以压缩格式（例如 .zip）打包 HAR 文件、控制台输出和屏幕录制内容，并将其与 Microsoft 支持人员共享。

## <a name="apple-safari"></a>Apple Safari

以下步骤说明如何在 Apple Safari 中使用开发人员工具。 有关详细信息，请参阅 [Safari 开发人员工具概述](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)。

1. 在 Apple Safari 中启用开发人员工具：

    1. 依次选择“Safari”、“Preferences”（首选项）。********

        ![Safari 首选项的屏幕截图](media/capture-browser-trace/safari-preferences.png)

    1. 依次选择“Advanced”（高级）选项卡、“Show Develop menu in menu bar”（在菜单栏中显示“开发”菜单）。********

        ![Safari 高级首选项的屏幕截图](media/capture-browser-trace/safari-show-develop-menu.png)

1. 登录到 Azure[门户](https://portal.azure.com)。 必须在开始跟踪之前登录，以免跟踪包含登录相关的敏感信息。__ 

1. 开始录制在门户中执行的步骤。 有关详细信息，请参阅[如何在 Mac 上录制屏幕](https://support.apple.com/HT208721)。

1. 在门户中，导航到问题发生之前的那个步骤。

1. 依次选择“Develop”（开发）、“Show Web Inspector”（显示 Web 检查器）。********

    ![“Show Web Inspector”（显示 Web 检查器）的屏幕截图](media/capture-browser-trace/safari-show-web-inspector.png)

1. 默认情况下，浏览器只为当前加载的页面保留跟踪信息。 设置以下选项，使浏览器保留所有跟踪信息，即使在重现过程中需要转到多个页面：

    1. 依次选择“Network”（网络）选项卡、“Preserve Log”（保留日志）。********

          ![“Preserve Log”（保留日志）的屏幕截图](media/capture-browser-trace/safari-network-preserve-log.png)

    1. 依次选择“Console”（控制台）选项卡、“Preserve Log”（保留日志）。********

          ![“Preserve Log”（保留日志）的屏幕截图](media/capture-browser-trace/safari-console-preserve-log.png)

1. 依次选择“Network”（网络）选项卡、“Clear Network Items”（清除网络项）。********

    ![“Clear Network Items”（清除网络项）的屏幕截图](media/capture-browser-trace/safari-clear-session.png)

1. 在门户中重现问题。 将会看到如下图所示的会话输出。

    ![浏览器跟踪结果的屏幕截图](media/capture-browser-trace/safari-browser-trace-results.png)

1. 重现意外的门户行为后，选择“Export”（导出）并保存文件。****

    ![“Export”（导出）的屏幕截图](media/capture-browser-trace/safari-network-export-har.png)

1. 停止屏幕录制器并保存文件。

1. 返回浏览器开发人员工具窗格，选择“控制台”选项卡，然后展开窗口。**** 将光标置于控制台输出的开头，然后拖动并选择输出的整个内容。 按命令键+C 复制输出，并将其保存到文本文件中。

    ![控制台输出的屏幕截图](media/capture-browser-trace/safari-console-select.png)

1. 以压缩格式（例如 .zip）打包 HAR 文件、控制台输出和屏幕录制内容，并将其与 Microsoft 支持人员共享。

## <a name="next-steps"></a>后续步骤

[Azure 门户概述](azure-portal-overview.md)