---
title: 排查 SSIS 集成运行时中的连接问题
description: 本文提供了有关在 SSIS 集成运行时中诊断连接的疑难解答指南
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 8e520048a6067f134e847953f4f4aa0598d9926e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172539"
---
# <a name="troubleshoot-diagnose-connectivity-in-the-ssis-integration-runtime"></a>排查 SSIS 集成运行时中的连接问题

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

如果在 SSIS 集成运行时中执行 SSIS 包时遇到连接问题，尤其是在 SSIS 集成运行时已加入 Azure 虚拟网络的情况下。 可以尝试通过使用 Azure 数据工厂门户的 "监视 SSIS 集成运行时" 页上的 "诊断连接" 功能来自行诊断问题。 

 ![监视页面-诊断连接 ](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png) ![ 监视器页面-测试连接](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)
 
本文包含在 SSIS 集成运行时中测试连接时可能会发现的最常见错误。 并描述了潜在的原因，以及解决这些错误的操作。 

## <a name="common-errors-potential-causes-and-recommendation-solutions"></a>常见错误、可能的原因和建议解决方案

### <a name="error-code-invalidinput"></a>错误代码： InvalidInput。
* **错误消息**：请验证输入是否正确。
* **可能的原因：** 输入不正确。
* **建议：** 请检查输入。

### <a name="error-code-firewallornetworkissue"></a>错误代码： FirewallOrNetworkIssue。
* **错误消息**：请验证此端口在防火墙/服务器/NSG 上是否打开，并且网络是稳定的。
* **可能的原因：** 
  * 你的服务器不打开此端口。
  * 网络安全组拒绝此端口上的出站流量
  * NVA/Azure firewall/本地防火墙不会打开此端口。
* **建议：** 
  * 在服务器上打开此端口。
  * 更新网络安全组以允许此端口上的出站流量。
  * 在 NVA/Azure firewall/本地防火墙上打开此端口。

### <a name="error-code-misconfigureddnssettings"></a>错误代码： MisconfiguredDnsSettings。
* **错误消息**：如果你在由 Azure-SSIS IR 联接的 VNet 中使用你自己的 DNS 服务器，请验证它是否可以解析你的主机名。
* **可能的原因：** 
  *  自定义 DNS 的问题
  *  你没有使用完全限定的域名 (FQDN) 用于你的专用主机名
* **建议：** 
  *  修复自定义 DNS 问题，确保它能够解析主机名。
  *   (FQDN) 使用完全限定的域名，例如，使用 <your_private_server>，而不是 <your_private_server>，因为 Azure-SSIS IR 不会自动追加你自己的 DNS 后缀。

### <a name="error-code-servernotallowremoteconenction"></a>错误代码： ServerNotAllowRemoteConenction。
* **错误消息**：请确认您的服务器允许通过此端口进行远程 TCP 连接。
* **可能的原因：** 
  *  服务器防火墙不允许远程 TCP 连接。
  *  你的服务器未联机。
* **建议：** 
  *  允许服务器防火墙上的远程 TCP 连接。
  *  启动服务器。
   
### <a name="error-code-misconfigurednsgsettings"></a>错误代码： MisconfiguredNsgSettings。
* **错误消息**：请验证 VNET 的 NSG 是否允许通过此端口的出站流量。 如果使用的是 Azure ExpressRoute 和或 UDR，请验证此端口在防火墙/服务器上是否已打开。
* **可能的原因：** 
  *  网络安全组拒绝此端口上的出站流量。
  *  NVA/Azure firewall/本地防火墙不会打开此端口。
* **建议：** 
  *  更新网络安全组以允许此端口上的出站流量。
  *  在 NVA/Azure firewall/本地防火墙上打开此端口。

### <a name="error-code-genericissues"></a>错误代码： GenericIssues。
* **错误消息**：由于一般问题，测试连接失败。
* **可能的原因：** 测试连接遇到一般的临时问题。
* **建议：** 请稍后重试测试连接。 如果重试不会有帮助，请联系 Azure 数据工厂支持团队。


### <a name="error-code-pspingexecutiontimeout"></a>错误代码： PSPingExecutionTimeout。
* **错误消息**：测试连接超时，请稍后重试。
* **可能的原因：** 测试连接超时。
* **建议：** 请稍后重试测试连接。 如果重试不会有帮助，请联系 Azure 数据工厂支持团队。

### <a name="error-code-networkinstable"></a>错误代码： NetworkInstable。
* **错误消息**：由于网络不稳定，测试连接不规则地成功。
* **可能的原因：** 暂时性的网络问题。
* **建议：** 请检查服务器或防火墙网络是否稳定。

## <a name="next-steps"></a>后续步骤

- 部署包。 有关详细信息，请参阅[使用 SSMS 将 SSIS 项目部署到 Azure](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)。
- 运行包。 有关详细信息，请参阅[使用 SSMS 运行 Azure 中的 SSIS 包](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)。
- 计划包。 有关详细信息，请参阅[计划 Azure 中的 SSIS 包](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)。

