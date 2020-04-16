---
title: 故障排除 Azure 同步工作室（预览）连接 PowerShell
description: 使用 PowerShell 解决 Azure 同步工作室连接问题
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: bbc985407a6cb56f4f1b539f514ab092b5f7d0de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431470"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>诊断使用 PowerShell 脚本的 Azure 突触工作室（预览）连接问题

Azure Synapse Studio（预览版）依赖于一组 Web API 终结点才能正常工作。 本指南将帮助您确定连接问题的原因，当您：
- 配置本地网络（如公司防火墙后面的网络）以访问 Azure 同步工作室。
- 使用 Azure 同步工作室遇到连接问题。

## <a name="prerequisite"></a>先决条件

* Windows 上的 PowerShell 5.0 或更高版本，或
* Windows 或 Linux 上的 PowerShell 酷睿 6.0 或更高版本。

## <a name="troubleshooting-steps"></a>疑难解答步骤

右键单击以下链接，然后单击"将目标另存为"：

- [测试-Azure 合成器.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

或者，您可以直接打开链接，并保存打开的脚本文件。 不要保存上面的链接的地址，因为它将来可能会更改。

在文件资源管理器中，右键单击下载的脚本文件，然后单击"使用 PowerShell 运行"。

![使用 PowerShell 运行下载的脚本文件](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

出现提示后，输入当前遇到问题或要测试连接的 Azure Synapse 工作区名称，然后按 Enter。

![输入工作区名称](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

诊断会话将启动。 等待检查完成。

![等待诊断完成](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

最后，将显示诊断摘要。 如果电脑无法连接到一个或多个终结点，它将在"摘要"部分中显示一些建议。

![查看诊断摘要](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

此外，此会话的诊断日志文件将与故障排除脚本在同一文件夹中生成。 其位置显示在"一般提示"部分 （） 中`D:\TestAzureSynapse_2020....log`。 如有必要，您可以将此文件发送给技术支持人员。

如果您是网络管理员并调整 Azure Synapse Studio 的防火墙配置，则上面显示的技术详细信息"摘要"部分可能会有所帮助。

* 所有标有"已通过"的测试项（请求）都意味着它们已通过连接测试，而不考虑 HTTP 状态代码。
 对于失败的请求，原因显示为黄色，如`NamedResolutionFailure`或`ConnectFailure`。 这些原因可以帮助您确定网络环境是否存在错误配置。


## <a name="next-steps"></a>后续步骤
如果前面的步骤无助于解决您发出"[创建支持票证](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)"。
