---
title: 将外部监视解决方案与 Azure Stack 集成 | Microsoft Docs
description: 了解如何将 Azure Stack 与数据中心内的外部监视解决方案集成。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: jeffgilb
ms.reviewer: thoroet
ms.openlocfilehash: d7c8520602132722fd0c7138de4a276b9ac2208a
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>将外部监视解决方案与 Azure Stack 集成

要在外部监视 Azure Stack 基础结构，需要监视 Azure Stack 软件、物理计算机和物理网络交换机。 上述每个监视区域都提供相应的方法来检索运行状况和警报信息：

- Azure Stack 软件提供基于 REST 的 API 来检索运行状况和警报。 软件定义的技术，如存储空间直通、 存储运行状况和警报的使用是软件监视的一部分。
- 物理计算机可以通过基板管理控制器 (BMC) 提供运行状况和警报信息。
- 物理网络设备可以通过 SNMP 协议提供运行状况和警报信息。

每个 Azure Stack 解决方案随附硬件生命周期主机。 此主机针对物理服务器和网络设备运行原始设备制造商 (OEM) 硬件供应商的监视软件。 如果需要，可以绕过这些监视解决方案，直接与数据中心内现有的监视解决方案集成。

> [!IMPORTANT]
> 使用的外部监视解决方案必须无代理。 不能在 Azure Stack 组件内部安装第三方代理。

下图演示 Azure Stack 集成系统、硬件生命周期主机、外部监视解决方案与外部票证/数据收集系统之间的流量流。

![显示 Azure Stack、监视与票证解决方案之间的流量的示意图。](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

本文介绍如何将 Azure Stack 与外部监视解决方案（例如 System Center Operations Manager 和 Nagios）集成。 此外，还介绍如何使用 PowerShell 或 REST API 调用以编程方式处理警报。

## <a name="integrate-with-operations-manager"></a>与 Operations Manager 集成

可将 Operations Manager 用于 Azure Stack 的外部监视。 用于 Microsoft Azure 堆栈的 System Center 管理包可监视多个 Azure 堆栈部署与单个的 Operations Manager 实例。 该管理包使用运行状况资源提供程序和更新资源提供程序 REST API 来与 Azure Stack 通信。 如果打算绕过硬件生命周期主机上运行的 OEM 监视软件，则可以安装供应商管理包来监视物理服务器。 还可以使用 Operations Manager 网络设备发现来监视网络交换机。

适用于 Azure Stack 的管理包提供以下功能：

- 可以管理多个 Azure Stack 部署
- 支持 Azure Active Directory (Azure AD) 和 Active Directory 联合身份验证服务 (AD FS)
- 可以检索和关闭警报
- 提供运行状况和容量仪表板
- 正在修补和更新 (P&U) 时可以执行自动维护模式检测
- 包含针对部署和区域的强制更新任务
- 可将自定义信息添加到区域
- 支持通知和报告

你可以下载 System Center 管理包为 Microsoft Azure 堆栈和关联[用户指南](https://www.microsoft.com/en-us/download/details.aspx?id=55184)，或直接从 Operations Manager。

对于票证解决方案，可将 Operations Manager 与 System Center Service Manager 集成。 集成的产品连接器支持双向通信，可让你在解决 Service Manager 中的服务请求之后关闭 Azure Stack 和 Operations Manager 中的警报。

下图演示了 Azure Stack 与现有 System Center 部署的集成。 可以进一步使用 System Center Orchestrator 或 Service Management Automation (SMA) 将 Service Manager 自动化，以便在 Azure Stack 中运行操作。

![演示与 OM、Service Manager 和 SMA 集成的示意图。](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>与 Nagios 集成

监视插件 Nagios 开发以及合作伙伴 Cloudbase 解决方案，这是宽松免费软件许可证 – 麻省理工学院 (Massachusetts Institute of Technology) 下可用。

该插件以 Python 编写，利用运行状况资源提供程序 REST API。 它提供在 Azure Stack 中检索和关闭警报的基本功能。 与 System Center 管理包一样，它可以让你添加多个 Azure Stack 部署以及发送通知。

该插件可与 Nagios Enterprise 和 Nagios Core 配合使用。 可以在[此处](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)下载。 下载站点还包含安装和配置详细信息。

### <a name="plugin-parameters"></a>插件参数

使用以下参数来配置插件文件“Azurestack_plugin.py”：

| 参数 | 说明 | 示例 |
|---------|---------|---------|
| *arm_endpoint* | Azure 资源管理器（管理员）终结点 |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Azure 资源管理器（管理员）终结点  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | 管理员订阅 ID | 通过管理员门户或 PowerShell 检索 |
| *User_name* | 操作员订阅用户名 | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | 操作员订阅密码 | mypassword |
| *Client_id* | Client | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Azure Stack 区域名称 | local |
|  |  |

*提供的 PowerShell GUID 是通用的。 可对每个部署使用它。

## <a name="use-powershell-to-monitor-health-and-alerts"></a>使用 PowerShell 监视运行状况和警报

如果不使用 Operations Manager、Nagios 或基于 Nagios 的解决方案，可以使用 PowerShell 来启用广泛的监视解决方案，以便与 Azure Stack 集成。

1. 若要使用 PowerShell，请确保已针对 Azure Stack 操作员环境[安装并配置 PowerShell](azure-stack-powershell-configure-quickstart.md)。 在可以访问资源管理器 （管理员） 终结点的本地计算机上安装 PowerShell (https://adminmanagement。 [区域]。[External_FQDN])。

2. 以 Azure Stack 操作员身份运行以下命令，以连接到 Azure Stack 环境：

   ```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. 使用如下所示的命令来处理警报：
   ```PowerShell
    #Retrieve all alerts
    Get-AzsAlert

    #Filter for active alerts
    $Active=Get-AzsAlert | Where {$_.State -eq "active"}
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    Get-AzsRPHealth

    #Retrieve infrastructure role instance health
    $FRPID=Get-AzsRPHealth|Where-Object {$_.DisplayName -eq "Capacity"}
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId

    ```

## <a name="learn-more"></a>了解详细信息

有关内置运行状况监视的信息，请参阅[在 Azure Stack 中监视运行状况和警报](azure-stack-monitor-health.md)。

## <a name="next-steps"></a>后续步骤

[安全集成](azure-stack-integrate-security.md)
