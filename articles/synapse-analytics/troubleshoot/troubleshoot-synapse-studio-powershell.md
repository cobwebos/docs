---
title: 排查 Synapse Studio (预览) 与 PowerShell 的连接
description: 使用 PowerShell 对 Azure Synapse Studio 连接进行故障排除
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 7cf9f18701317482284a3f1fa4b69a2c2927f64a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91287706"
---
# <a name="diagnose-azure-synapse-studio-preview-connectivity-issues-with-powershell-script"></a>诊断 Azure Synapse Studio (预览) PowerShell 脚本的连接问题

Azure Synapse Studio (预览) 依赖于一组 Web API 终结点才能正常工作。 本指南将帮助你在以下情况下确定连接问题的原因：
- 配置本地网络 (如在企业防火墙后面的网络) 用于访问 Azure Synapse Studio。
- 使用 Azure Synapse Studio 遇到连接问题。

## <a name="prerequisite"></a>先决条件

* PowerShell 5.0 或更高版本的 Windows，或者
* Windows 或 Linux 上的 PowerShell Core 6.0 或更高版本。

## <a name="troubleshooting-steps"></a>疑难解答步骤

右键单击以下链接，然后选择 "将目标另存为"：

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

或者，您可以直接打开链接，并保存打开的脚本文件。 请勿保存上面的链接地址，因为将来可能会更改。

在文件资源管理器中，右键单击下载的脚本文件，然后选择 "用 PowerShell 运行"。

![通过 PowerShell 运行下载的脚本文件](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

出现提示时，输入当前遇到问题或要测试连接的 Azure Synapse 工作区名称，然后按 enter。

![输入工作区名称](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

将启动诊断会话。 等待检查完成。

![等待诊断完成](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

最终，将显示诊断摘要。 如果你的电脑无法连接到一个或多个终结点，它将在 "摘要" 部分中显示一些建议。

![查看诊断摘要](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

此外，此会话的诊断日志文件将在故障排除脚本所在的文件夹中生成。 它的位置显示在 "常规提示" 部分中， (`D:\TestAzureSynapse_2020....log`) 。 如果需要，你可以将此文件发送给技术支持人员。

如果你是网络管理员并调整 Azure Synapse Studio 的防火墙配置，则 "摘要" 部分上面显示的技术详细信息可能会有所帮助。

* 使用 "通过" 标记)  (请求的所有测试项意味着它们已通过连接测试，而不考虑 HTTP 状态代码。
 对于失败的请求，原因显示为黄色，如 `NamedResolutionFailure` 或 `ConnectFailure` 。 这些原因可能会帮助你确定网络环境是否存在配置错误。


## <a name="next-steps"></a>后续步骤
如果前面的步骤不能帮助解决问题，请 [创建支持票证](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)。
