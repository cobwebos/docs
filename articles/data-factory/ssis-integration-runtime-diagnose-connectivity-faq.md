---
title: 使用 SSIS 集成运行时中的“诊断连接”功能
description: 使用“诊断连接”功能排查 SSIS 集成运行时中的连接问题。
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: cf41da685036770144ebf7eb2befd0c3d126362d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87446026"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>使用 SSIS 集成运行时中的“诊断连接”功能

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在执行 SSIS 基础运行时中的 SQL Server Integration Services (SSIS) 包时，可能会遇到连接问题。 如果 SSIS 集成运行时加入 Azure 虚拟网络，尤其会发生这些问题。

排查连接问题时，请使用“诊断连接”功能来测试连接。 此功能位于 Azure 数据工厂门户的“监视 SSIS 集成运行时”页面上。

 ![“监视”页面 - 诊断连接](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![“监视”页面 - 测试连接](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

使用以下部分来了解在测试连接时出现的最常见错误。 每部分都介绍了以下内容：

- 错误代码
- 错误消息
- 错误的可能原因
- 建议的解决方案

## <a name="error-code-invalidinput"></a>错误代码：InvalidInput

- **错误消息**：“请验证你的输入是否正确。”
- **可能原因**：输入不正确。
- **建议**：请检查输入。

## <a name="error-code-firewallornetworkissue"></a>错误代码：FirewallOrNetworkIssue

- **错误消息**：“请验证此端口在防火墙/服务器/NSG 上是否是打开的，且网络是否稳定。”
- **可能的原因：**
  - 服务器未打开此端口。
  - 你的网络安全组拒绝了此端口上的出站流量。
  - 你的 NVA/Azure 防火墙/本地防火墙未打开此端口。
- **建议：**
  - 打开服务器上的此端口。
  - 更新网络安全组以允许此端口上的出站流量。
  - 打开 NVA/Azure 防火墙/本地防火墙上的此端口。

## <a name="error-code-misconfigureddnssettings"></a>错误代码：MisconfiguredDnsSettings

- **错误消息**： "如果你在由 Azure-SSIS IR 联接的 VNet 中使用你自己的 DNS 服务器，请验证它是否可以解析你的主机名。"
- **可能的原因：**
  -  你的自定义 DNS 存在问题。
  -  你没有为专用主机名使用完全限定的域名 (FQDN)。
- **建议：**
  -  解决自定义 DNS 的问题，确保它能够解析主机名。
  -  使用 FQDN。 Azure-SSIS IR 不会自动追加你自己的 DNS 后缀。 例如，请使用 **<your_private_server>.contoso.com** 而非 **<your_private_server>** 。

## <a name="error-code-servernotallowremoteconnection"></a>错误代码：ServerNotAllowRemoteConnection

- **错误消息**：“请验证服务器是否允许通过此端口进行远程 TCP 连接。”
- **可能的原因：**
  -  你的服务器防火墙不允许远程 TCP 连接。
  -  你的服务器未联机。
- **建议：**
  -  在服务器防火墙上允许远程 TCP 连接。
  -  启动服务器。
   
## <a name="error-code-misconfigurednsgsettings"></a>错误代码：MisconfiguredNsgSettings

- **错误消息**：“请验证 VNet 的 NSG 是否允许出站流量通过此端口。 如果使用的是 Azure ExpressRoute 和/或 UDR，请验证是否在防火墙/服务器上打开了此端口。”
- **可能的原因：**
  -  你的网络安全组拒绝了此端口上的出站流量。
  -  你的 NVA/Azure 防火墙/本地防火墙未打开此端口。
- **建议：**
  -  更新网络安全组以允许此端口上的出站流量。
  -  打开 NVA/Azure 防火墙/本地防火墙上的此端口。

## <a name="error-code-genericissues"></a>错误代码：GenericIssues

- **错误消息**：“测试连接失败，因为出现一般性错误。”
- **可能原因**：测试连接遇到一般性的临时问题。
- **建议**：稍后重试测试连接。 如果重试不起作用，请联系 Azure 数据工厂支持团队。

## <a name="error-code-pspingexecutiontimeout"></a>错误代码：PSPingExecutionTimeout

- **错误消息**：“测试连接超时，请稍后重试。”
- **可能原因**：测试连接超时。
- **建议**：稍后重试测试连接。 如果重试不起作用，请联系 Azure 数据工厂支持团队。

## <a name="error-code-networkinstable"></a>错误代码：NetworkInstable

- **错误消息**：“测试连接不定期成功，因为网络不稳定。”
- **可能原因**：暂时性的网络问题。
- **建议**：检查服务器或防火墙网络是否稳定。

## <a name="next-steps"></a>后续步骤

- [使用 SSMS 将 SSIS 项目部署到 Azure](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)
- [使用 SSMS 在 Azure 中运行 SSIS 程序包](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)
- [在 Azure 中计划 SSIS 程序包](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)
