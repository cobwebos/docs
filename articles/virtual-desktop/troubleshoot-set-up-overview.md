---
title: Windows 虚拟桌面故障排除概述-Azure
description: 有关在设置 Windows 虚拟桌面租户环境时解决问题的概述。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 196c4f692b425d2fc6592888ba89f2fa6dafd3de
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612497"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>故障排除概述、反馈和支持

>[!IMPORTANT]
>此内容适用于带有 Azure 资源管理器 Windows 虚拟桌面对象的弹簧2020更新。 如果使用的是不带 Azure 资源管理器对象的 Windows 虚拟桌面2019版，请参阅[此文](./virtual-desktop-fall-2019/troubleshoot-set-up-overview-2019.md)。
>
> Windows 虚拟桌面春季2020更新目前为公共预览版。 此预览版本在提供时没有服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本文概述了在设置 Windows 虚拟桌面租户环境时可能会遇到的问题，并提供解决这些问题的方法。

## <a name="report-issues-during-public-preview"></a>公开预览期间的报告问题

若要在2020春季发行版的公共预览版期间报告问题或提供建议功能，请访问[Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)。 你可以使用技术社区讨论最佳实践，或者建议和投票新功能。 当你报告与公共预览版本相关的问题时，请确保将问题类型标记为 "**春季 2020 Update （预览版）**"。

当你进行 post 请求帮助或建议新功能时，请确保尽可能详细地描述你的主题。 详细信息可以帮助其他用户解答你的问题，或者了解你要为其建议的功能。

## <a name="escalation-tracks"></a>升级跟踪

在执行任何其他操作之前，请确保检查[azure 状态页](https://status.azure.com/status)和[Azure 服务运行状况](https://azure.microsoft.com/features/service-health/)，确保 azure 服务正常运行。

使用下表来确定和解决使用远程桌面客户端设置租户环境时可能遇到的问题。 设置租户后，可以使用新的[诊断服务](diagnostics-role-service.md)来识别常见方案的问题。

| **问题**                                                            | **建议的解决方案**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 在 Azure 门户中访问 Marketplace 模板       | Azure Marketplace Windows 虚拟桌面模板可免费使用。|
| 会话主机池 Azure 虚拟网络（VNET）和快速路由设置               | [打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，然后选择相应的服务（在 "网络" 类别下）。 |
| 当未使用随 Windows 虚拟桌面提供的 Azure 资源管理器模板时，会话主机池虚拟机（VM）创建 | [打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，并为服务选择**运行 Windows 的虚拟机**。 <br> <br> 有关随 Windows 虚拟桌面一起提供的 Azure 资源管理器模板的问题，请参阅创建 Windows 虚拟桌面租户[和创建主机池](troubleshoot-set-up-issues.md)部分。 |
| 从 Azure 门户管理 Windows 虚拟桌面会话主机环境    | [打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> 对于使用远程桌面服务/Windows 虚拟桌面 PowerShell 时的管理问题，请参阅[Windows 虚拟桌面 powershell](troubleshoot-powershell.md)或[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择**windows 虚拟桌面**作为该服务，选择 "**配置和管理**" 作为 "问题类型"，然后选择 "**使用 PowerShell 配置租户**" 的问题子类型。 |
| 管理绑定到主机池和应用程序组的 Windows 虚拟桌面配置（应用组）      | 请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)，或[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择用于该服务的**Windows 虚拟桌面**，然后选择相应的问题类型。|
| 部署和管理 FSLogix 配置文件容器 | 请参阅[FSLogix 产品故障排除指南](/fslogix/fslogix-trouble-shooting-ht/)，如果不解决此问题，请[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择 "服务的**Windows 虚拟桌面**"，选择 " **FSLogix** " 作为 "问题类型"，然后选择相应的问题子类型。 |
| 远程桌面客户端在启动时无法正常工作                                                 | 请参阅对[远程桌面客户端进行故障排除](troubleshoot-client.md)，如果这不能解决问题，请[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择**Windows 虚拟桌面**作为服务，然后选择 "**远程桌面客户端**" 作为 "问题类型"。  <br> <br> 如果这是网络问题，用户需要联系其网络管理员。 |
| 已连接但无源                                                                 | 使用用户连接进行故障排除，但不会显示[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)的 " [（无源）](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed) " 部分。 <br> <br> 如果已将用户分配到应用组，请[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，选择 "用于服务的**Windows 虚拟桌面**"，然后选择 "**远程桌面客户端**" 作为 "问题类型"。 |
| 由于网络导致的源发现问题                                            | 用户需要联系其网络管理员。 |
| 连接客户端                                                                    | 请参阅[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)，如果这不能解决你的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。 |
| 远程应用程序或桌面的响应能力                                      | 如果问题与特定应用程序或产品相关，请联系负责该产品的团队。 |
| 授权消息或错误                                                          | 如果问题与特定应用程序或产品相关，请联系负责该产品的团队。 |
| 第三方身份验证方法的问题 | 验证第三方提供程序是否支持 Windows 虚拟桌面方案，并与任何已知问题相关。 |
| 使用 Windows 虚拟桌面 Log Analytics 的问题 | 有关诊断架构的问题，请[提出 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)。<br><br>对于 Log Analytics 中的查询、可视化效果或其他问题，请在 "Log Analytics" 下选择适当的问题类型。 |

## <a name="next-steps"></a>后续步骤

- 若要在 Windows 虚拟桌面环境中创建主机池时排查问题，请参阅[环境和主机池创建](troubleshoot-set-up-issues.md)。
- 若要解决在 Windows 虚拟桌面中配置虚拟机（VM）时遇到的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 若要解决 Windows 虚拟桌面客户端连接问题，请参阅[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 若要解决远程桌面客户端的问题，请参阅[排查远程桌面客户端](troubleshoot-client.md)问题
- 若要解决将 PowerShell 与 Windows 虚拟桌面结合使用时遇到的问题，请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)。
- 若要了解有关该服务的详细信息，请参阅[Windows 虚拟桌面环境](environment-setup.md)。
- 若要浏览疑难解答教程，请参阅[教程：排查资源管理器模板部署问题](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-resource-manager/management/view-activity-logs.md)。
- 若要了解有关在部署期间确定错误的操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
