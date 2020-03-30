---
title: Windows 虚拟桌面故障排除概述 - Azure
description: 设置 Windows 虚拟桌面租户环境时疑难解答问题的概述。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 02/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b9dfb2ee7e74f94bfc3e6d2c679cb7da3d6bc66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127406"
---
# <a name="troubleshooting-overview-feedback-and-support"></a>故障排除概述、反馈和支持

本文概述了设置 Windows 虚拟桌面租户环境时可能会遇到的问题，并提供了解决问题的方法。

## <a name="provide-feedback"></a>提供反馈

请访问 [Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，与产品团队和活跃的社区成员共同探讨 Windows 虚拟桌面服务。

## <a name="escalation-tracks"></a>升级轨道

使用下表标识并解决使用远程桌面客户端设置租户环境时可能遇到的问题。 设置租户后，您可以使用我们新的[诊断服务](diagnostics-role-service.md)来识别常见方案的问题。

>[!NOTE]
> 我们有一个技术社区论坛，您可以访问该论坛，与产品团队和活跃的社区成员讨论您的问题。 访问[Windows 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)开始讨论。

| **问题**                                                            | **建议的解决方案**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 创建 Windows 虚拟桌面租户                                                    | 如果存在 Azure 中断，[请打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)。否则[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，为服务选择**Windows 虚拟桌面**，为问题类型选择 **"部署"，** 然后为问题子类型选择**创建 Windows 虚拟桌面租户的问题**。|
| 访问 Azure 门户中的应用商店模板       | 如果存在 Azure 中断，[请打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> Azure 应用商店 Windows 虚拟桌面模板可免费使用。|
| 从 GitHub 访问 Azure 资源管理器模板                                  | 请参阅[创建"创建 Windows 虚拟桌面"会话主机 VM](troubleshoot-set-up-issues.md#creating-windows-virtual-desktop-session-host-vms)部分的[租户和主机池创建](troubleshoot-set-up-issues.md)。 如果问题仍未解决，请与[GitHub 支持团队](https://github.com/contact)联系。 <br> <br> 如果在 GitHub 中访问模板后发生错误，请与[Azure 支持](https://azure.microsoft.com/support/create-ticket/)。|
| 会话主机池 Azure 虚拟网络 （VNET） 和快速路由设置               | [打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，然后选择相应的服务（在"网络"类别下）。 |
| 未使用 Windows 虚拟桌面提供的 Azure 资源管理器模板时创建会话主机池虚拟机 （VM） | [打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，然后选择**运行该服务的 Windows**的虚拟机。 <br> <br> 有关 Windows 虚拟桌面提供的 Azure 资源管理器模板的问题，请参阅创建租户的 Windows 虚拟桌面租户部分[和主机池创建](troubleshoot-set-up-issues.md)。 |
| 从 Azure 门户管理 Windows 虚拟桌面会话主机环境    | [打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> 有关使用远程桌面服务/Windows 虚拟桌面 PowerShell 时的管理问题，请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)或[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，为服务选择**Windows 虚拟桌面**，为问题类型选择 **"配置和管理**"，然后选择**使用 PowerShell**配置租户的问题。 |
| 管理绑定到主机池和应用程序组（应用组）的 Windows 虚拟桌面配置      | 请参阅[Windows 虚拟桌面 PowerShell](troubleshoot-powershell.md)，或[打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，为服务选择**Windows 虚拟桌面**，然后选择适当的问题类型。|
| 部署和管理 FSLogix 配置文件容器 | 请参阅[FSLogix 产品的故障排除指南](/fslogix/fslogix-trouble-shooting-ht/)，如果这不能解决问题，[请打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，为服务选择**Windows 虚拟桌面**，为问题类型选择**FSLogix，** 然后选择适当的问题子类型。 |
| 远程桌面客户端在启动时出现故障                                                 | 请参阅[对远程桌面客户端进行故障排除](troubleshoot-client.md)，如果这不能解决问题，[请打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，为服务选择**Windows 虚拟桌面**，然后为问题类型选择**远程桌面客户端**。  <br> <br> 如果是网络问题，则用户需要与其网络管理员联系。 |
| 已连接，但没有源                                                                 | 使用用户连接进行故障排除，但[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)[不显示任何内容（无源）](troubleshoot-service-connection.md#user-connects-but-nothing-is-displayed-no-feed)部分。 <br> <br> 如果用户已分配到应用组，[请打开 Azure 支持请求](https://azure.microsoft.com/support/create-ticket/)，为服务选择**Windows 虚拟桌面**，然后为问题类型选择**远程桌面客户端**。 |
| 由于网络而导致的源发现问题                                            | 您的用户需要与其网络管理员联系。 |
| 连接客户端                                                                    | 请参阅[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)，如果这不能解决您的问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。 |
| 远程应用程序或桌面的响应能力                                      | 如果问题与特定应用程序或产品相关，请与负责该产品的团队联系。 |
| 许可消息或错误                                                          | 如果问题与特定应用程序或产品相关，请与负责该产品的团队联系。 |
| 在 GitHub 上使用 Windows 虚拟桌面工具时的问题（Azure 资源管理器模板、诊断工具和管理工具） | 请参阅[远程桌面服务的 Azure 资源管理器模板](https://github.com/Azure/RDS-Templates/blob/master/README.md)以报告问题。 |

## <a name="next-steps"></a>后续步骤

- 要在 Windows 虚拟桌面环境中创建租户和主机池时解决问题，请参阅[租户和主机池创建](troubleshoot-set-up-issues.md)。
- 要在 Windows 虚拟桌面中配置虚拟机 （VM） 时解决问题，请参阅[会话主机虚拟机配置](troubleshoot-vm-configuration.md)。
- 要解决 Windows 虚拟桌面客户端连接的问题，请参阅[Windows 虚拟桌面服务连接](troubleshoot-service-connection.md)。
- 要解决远程桌面客户端的问题，请参阅[排除远程桌面客户端的故障](troubleshoot-client.md)
- 要解决在 Windows 虚拟桌面中使用 PowerShell 时的问题，请参阅[Windows 虚拟桌面电源外壳](troubleshoot-powershell.md)。
- 要了解有关该服务的更多信息，请参阅[Windows 虚拟桌面环境](environment-setup.md)。
- 要完成疑难解答教程，请参阅[教程：解决资源管理器模板部署的疑难解答](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解审核操作，请参阅[使用 Resource Manager 执行审核操作](../azure-resource-manager/management/view-activity-logs.md)。
- 要了解在部署过程中确定错误的操作，请参阅[查看部署操作](../azure-resource-manager/templates/deployment-history.md)。
