---
title: 排除 Azure 同步工作室（预览）
description: 排除 Azure 突触工作室
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ede38317762e5aac5562c9bb3494713c3edacc69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431314"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Azure 同步工作室（预览）故障排除

此故障排除指南提供有关在打开网络连接问题支持票证时要提供哪些信息的说明。 有了正确的信息，我们可以更快地解决问题。

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>SQL 按需（预览）服务连接问题

### <a name="symptom-1"></a>症状 1

"SQL 按需"选项在"连接到"下拉列表中显示为灰色。

![症状1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>症状 2

使用"SQL 按需"运行查询会提供"无法建立与服务器的连接"错误消息。

![症状2](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>疑难解答步骤

> [!NOTE] 
>    以下故障排除步骤适用于铬边缘和铬。 您可以使用具有相同故障排除步骤的其他浏览器（如 FireFox），但"开发人员工具"窗口的布局可能与此 TSG 中的屏幕截图不同。 如果可能，请勿使用经典边缘进行故障排除，因为它可能会在特定情况下显示不准确的信息。

打开"诊断信息"面板，选择"下载诊断"按钮。 保留下载的信息以进行错误报告。 您可以复制"会话 ID"并在打开支持票证时附加它。

![诊断信息](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

要开始故障排除，请重试在 Azure 同步工作室中执行的操作。

- 对于症状 1，在"SQL 脚本"选项卡中选择"使用数据库"下拉列表右侧的"刷新"按钮，并检查是否可以看到"SQL 按需"。
- 对于症状 2，请尝试再次运行查询以查看其是否成功执行。

如果问题仍然存在，请按浏览器中的 F12 打开"开发人员工具"（DevTools）。

在"开发人员工具"窗口中，切换到"网络"面板。 如有必要，选择"网络"面板中工具栏上的"清除"按钮。
确保选中"网络"面板中的"禁用缓存"。

重试在 Azure 同步工作室中执行的操作。 您可能会在"开发人员工具"中的"网络"列表中看到显示的新项目。 请注意当前系统时间，以在支持票证中提供。

![网络面板](media/troubleshooting-synapse-studio/network-panel.png)

查找 Url 列与以下模式匹配的项目：

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

其中 [*A*] 是工作区名称，并且"按需"可以是"-sqlod"，其中 [*B*] 应为数据库名称，如"master"。 最多应有两个具有相同 URL 值但方法值不同的项;选项和 POST。 检查这两个项目在状态列下是否有"200"或"20x"，其中"x"可以是任何个位数。

如果其中任一人有"20x"以外的东西，并且：

- 状态以"（失败）"开头，请加宽"状态"列或将指针悬停在状态文本上以查看完整文本。 在打开支持票证时包括文本和/或屏幕截图。

    ![状态文本](media/troubleshooting-synapse-studio/status-text.png)

    - 如果您在 10 分钟内看到ERR_NAME_NOT_RESOLVED并创建了工作区，请等待 10 分钟，然后重试以查看问题是否仍然存在。
    - 如果您看到ERR_INTERNET_DISCONNECTED或ERR_NETWORK_CHANGED，则可能表示您的 PC 网络连接有问题。 检查网络连接并重试操作。
    - 如果您看到包含"SSL"的ERR_CONNECTION_RESET、ERR_SSL_PROTOCOL_ERROR或其他错误代码，则可能表明本地 SSL 配置有问题，或者您的网络管理员已阻止对 SQL 按需服务器的访问。 打开支持票证并在说明中附加错误代码。
    - 如果您看到ERR_NETWORK_ACCESS_DENIED，您可能需要与管理员联系，了解本地防火墙策略是否阻止了对 *.database.windows.net 域或远程端口 1443 的访问。
    - 或者，请立即尝试其他计算机和/或网络环境中的相同操作，以排除电脑上的网络配置问题。

- 状态为"40x"、"50x"或其他号码，在项目上选择以查看详细信息。 您应该看到右侧的项目详细信息。 查找"响应标头"部分;然后检查是否存在名为"访问控制-允许源"的项目。 如果是，请检查它是否具有以下值之一：

    - `*`（单星号）
    - https://web.azuresynapse.net/（或浏览器地址栏中的文本开头的其他值）

如果响应标头包含上述值之一，则意味着我们应已收集失败信息。 如果需要，您可以打开支持票证，并可以选择附加项目详细信息的屏幕截图。

如果看不到标头，或者标头没有上面列出的值之一，请打开票证时附加项目详细信息的屏幕截图。

![项目详细信息](media/troubleshooting-synapse-studio/item-details.png)

如果上述步骤不能解决您的问题，您可能需要打开支持票证。 提交支持票证时，请包括本指南开头下载的"会话 ID"或"诊断信息"。

报告问题时，您可以选择在"开发人员工具"中截取"控制台"选项卡的屏幕截图，并将其附加到支持票证。 如有必要，滚动内容并获取多个屏幕截图以捕获整个邮件。

![开发人员工具控制台](media/troubleshooting-synapse-studio/developer-tool-console.png)

如果要附加屏幕截图，请提供截图的时间（或估计时间范围）。 它将有助于我们调查这个问题。

某些浏览器支持在"控制台"选项卡中显示时间戳。对于铬边缘/铬，请在"开发人员工具"中打开"设置"对话框，并在"首选项"选项卡中选中"显示时间戳"。

![开发人员-工具控制台设置](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![显示时间戳](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>后续步骤
如果前面的步骤无助于解决您签发的问题[创建支持票证](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
