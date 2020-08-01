---
title: 在 SSIS 集成运行时中使用诊断连接功能
description: 使用诊断连接功能排查 SSIS 集成运行时中的连接问题。
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
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446026"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>在 SSIS 集成运行时中使用诊断连接功能

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

在 SSIS 集成运行时中执行 SQL Server Integration Services （SSIS）包时，可能会发现连接问题。 如果 SSIS 集成运行时加入 Azure 虚拟网络，则会发生这些问题。

排查连接问题时，请使用*诊断连接*功能来测试连接。 此功能位于 Azure 数据工厂门户的 "监视 SSIS 集成运行时" 页上。

 ![监视页-诊断连接](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![监视页面-测试连接](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

使用以下部分来了解在测试连接时出现的最常见错误。 每节描述：

- 错误代码
- 错误消息
- 错误的可能原因
- 建议的解决方案

## <a name="error-code-invalidinput"></a>错误代码： InvalidInput

- **错误消息**： "请验证输入是否正确。"
- **可能的原因**：输入不正确。
- **建议**：检查输入。

## <a name="error-code-firewallornetworkissue"></a>错误代码： FirewallOrNetworkIssue

- **错误消息**： "请验证此端口是否在防火墙/服务器/NSG 上打开并且网络稳定。"
- **可能的原因：**
  - 服务器未打开端口。
  - 网络安全组拒绝端口上的出站流量。
  - NVA/Azure 防火墙/本地防火墙不会打开端口。
- **针对**
  - 打开服务器上的端口。
  - 更新网络安全组以允许端口上的出站流量。
  - 打开 NVA/Azure Firewall/本地防火墙上的端口。

## <a name="error-code-misconfigureddnssettings"></a>错误代码： MisconfiguredDnsSettings

- **错误消息**： "如果你在由 Azure-SSIS IR 联接的 VNet 中使用你自己的 DNS 服务器，请验证它是否可以解析你的主机名。"
- **可能的原因：**
  -  你的自定义 DNS 出现问题。
  -  你没有为你的专用主机名使用完全限定的域名（FQDN）。
- **针对**
  -  修复自定义 DNS 问题，确保它能够解析主机名。
  -  使用 FQDN。 Azure-SSIS IR 不会自动追加你自己的 DNS 后缀。 例如，使用 **<your_private_server> contoso.com** ，而不是 **<your_private_server **>。

## <a name="error-code-servernotallowremoteconnection"></a>错误代码： ServerNotAllowRemoteConnection

- **错误消息**： "请确认你的服务器允许通过此端口进行远程 TCP 连接"。
- **可能的原因：**
  -  服务器防火墙不允许远程 TCP 连接。
  -  你的服务器未联机。
- **针对**
  -  允许服务器防火墙上的远程 TCP 连接。
  -  启动服务器。
   
## <a name="error-code-misconfigurednsgsettings"></a>错误代码： MisconfiguredNsgSettings

- **错误消息**： "请验证 VNET 的 NSG 是否允许通过此端口的出站流量。 如果使用的是 Azure ExpressRoute 和或 UDR，请验证此端口是否在防火墙/服务器上打开。 "
- **可能的原因：**
  -  网络安全组拒绝端口上的出站流量。
  -  NVA/Azure 防火墙/本地防火墙不会打开端口。
- **建议：**
  -  更新网络安全组以允许端口上的出站流量。
  -  打开 NVA/Azure Firewall/本地防火墙上的端口。

## <a name="error-code-genericissues"></a>错误代码： GenericIssues

- **错误消息**： "由于一般问题，测试连接失败"。
- **可能的原因**：测试连接遇到一般临时问题。
- **建议**：稍后重试测试连接。 如果重试不起作用，请联系 Azure 数据工厂支持团队。

## <a name="error-code-pspingexecutiontimeout"></a>错误代码： PSPingExecutionTimeout

- **错误消息**： "测试连接超时，请稍后再试。"
- **可能的原因**：测试连接超时。
- **建议**：稍后重试测试连接。 如果重试不起作用，请联系 Azure 数据工厂支持团队。

## <a name="error-code-networkinstable"></a>错误代码： NetworkInstable

- **错误消息**： "由于网络不稳定，测试连接不规则地成功"。
- **可能的原因**：暂时性的网络问题。
- **建议**：检查服务器或防火墙网络是否稳定。

## <a name="next-steps"></a>后续步骤

- [使用 SSMS 将 SSIS 项目部署到 Azure](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)
- [在 Azure 中通过 SSMS 运行 SSIS 包](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)
- [计划 Azure 中的 SSIS 包](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)
