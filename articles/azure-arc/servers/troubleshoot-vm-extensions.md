---
title: 排查支持 Azure Arc 的服务器 VM 扩展问题
description: 本文介绍如何排查和解决启用了 Azure Arc 的服务器上出现的 Azure VM 扩展的问题。
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: ffd7db5ff7da3d7f60762117f80d7b9b5af6f646
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91342671"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>启用 Arc 的服务器故障排除 VM 扩展问题

本文提供了有关故障排除和解决在启用 Arc 的服务器上尝试部署或删除 Azure VM 扩展时可能出现的问题的信息。 有关一般信息，请参阅 [管理和使用 AZURE VM 扩展](./manage-vm-extensions.md)。

## <a name="general-troubleshooting"></a>常规故障排除

可以从 Azure 门户检索有关扩展部署状态的数据。

以下故障排除步骤适用于所有 VM 扩展。

1. 若要检查来宾代理日志，请查看在中 `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` 为 Windows 和 Linux 设置你的扩展时的活动 `/var/lib/GuestConfig/ext_mgr_logs` 。

2. 有关 Windows 中的更多详细信息，请查看特定扩展的扩展日志 `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` 。 对于安装在 Linux 下的每个扩展，会将扩展输出记录到文件中 `/var/lib/GuestConfig/extension_logs` 。

3. 请查看特定于扩展的文档故障排除部分，了解错误代码、已知问题等。有关每个扩展的其他疑难解答信息，请参阅扩展概述中的 **故障排除和支持** 部分。 这包括写入日志的错误代码的说明。 扩展项目链接在 [扩展表](manage-vm-extensions.md#extensions)中。

4. 查看系统日志。 检查其他可能影响了扩展的操作，例如，长时间安装另一个需要包管理器独占访问权限的应用程序。

## <a name="troubleshooting-specific-extension-scenarios"></a>排查特定扩展方案问题

### <a name="vm-insights"></a>VM Insights

- 为启用了 Azure Arc 的服务器启用 VM Insights 时，会安装依赖关系和 Log Analytics 代理。 在慢速计算机上或网络连接速度缓慢的计算机上，可以在安装过程中看到超时。 Microsoft 正在采取措施来解决连接的计算机代理中的此问题，以帮助改进此条件。 在这种情况下，重试安装可能会成功。

### <a name="log-analytics-agent-for-linux"></a>适用于 Linux 的 Log Analytics 代理

- Log Analytics 代理版本 1.13.9 (对应的扩展版本为 1.13.15) 未正确将上传的数据标记为启用了 Azure Arc 的服务器的资源 ID。 尽管日志会发送到服务，但当你尝试在选择 **日志** 或 **见解**后查看选定的已启用服务器中的数据时，不会返回任何数据。 可以通过从 Azure Monitor 日志中运行查询，或从用于 VM 的 Azure Monitor （作用域为工作区）查看其数据。

- 适用于 Linux 的 Log Analytics 代理目前不支持某些分发版。 代理需要安装其他依赖项，包括 Python 2。 在 [此处](../../azure-monitor/platform/agents-overview.md#supported-operating-systems)查看支持矩阵和先决条件。

- 状态消息中的错误代码52指示缺少依赖项。 有关缺少的依赖项的详细信息，请查看输出和日志。

- 如果安装失败，请查看扩展概述中的 **故障排除和支持** 部分。 在大多数情况下，状态消息中包含一个错误代码。 对于 Linux 的 Log Analytics 代理， [此处](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support)介绍了状态消息以及此 VM 扩展的常规疑难解答信息。

## <a name="next-steps"></a>后续步骤

如果未在此处看到你遇到的问题，或者无法解决你遇到的问题，请尝试以下途径之一以获取其他支持：

- 通过 [Microsoft Q&](/answers/topics/azure-arc.html)获取来自 Azure 专家的解答。

- 连接到 [@AzureSupport](https://twitter.com/azuresupport)，这是用于改进客户体验的官方 Microsoft Azure 帐户。 Azure 支持人员会将你连接到 Azure 社区，从中可以获得解答、支持和专家建议。

- 提出 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。
